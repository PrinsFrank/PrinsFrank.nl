---
title: Optimizing Gitlab pipelines - PHPUnit (2)
subtitle: Parallelization, coverage and more parallelization
Description:
categories: [development]
tags: []
readtime:
---

The last few months I've been working a lot on optimizing our Gitlab pipelines for a large private codebase to keep the runtime of them below 4 minutes. As there is not a lot of info about optimizing Gitlab pipelines for PHP projects in general, I decided it was time to change that. As there is a lot to unpack here, this will be the second in a multipart series. If you haven't read the [first part, I'd recommend to read it here](/2022/02/15/Optimizing-gitlab-pipelines-pt-1-basics).

In this second part I will focus on optimizing PHPUnit pipelines.

---

## Making use of test suites

Besides being useful for development, splitting up your tests into suits for Unit tests, Functional tests and Integration tests is also very useful to speed up your pipeline. When a codebase matures, a Unit test suite will probably contain the most tests, but feature tests are taking more time individually, so as a whole the test suites will take about the same time to finish. You can split them up in your phpunit.xml so they can be run parallel on a per-suite basis;

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="vendor/phpunit/phpunit/phpunit.xsd"
         failOnRisky="true"
         failOnEmptyTestSuite="true"
         failOnIncomplete="true"
         failOnWarning="true"
         bootstrap="tests/bootstrap.php"
         enforceTimeLimit="true"
         defaultTimeLimit="1"
         colors="true">
  <coverage processUncoveredFiles="true">
    <include>
      <directory suffix=".php">./app</directory>
    </include>
  </coverage>
  <testsuites>
    <testsuite name="unit">
      <directory>./tests/Unit</directory>
    </testsuite>
    <testsuite name="feature">
      <directory>./tests/Feature</directory>
    </testsuite>
    <testsuite name="integration">
      <directory>./tests/Integration</directory>
    </testsuite>
  </testsuites>
</phpunit>
```

And we can configure a job for every suit. For example, this is a test suite for the integration tests;

```yml
test-integration:
  stage: test
  interruptible: true
  script:
    - php vendor/bin/phpunit --testsuite integration
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
      changes:
        - '.gitlab-ci.yml'
        - 'phpunit.xml'
        - 'composer.lock'
        - '**/*.php'
        - '*.php'
        - 'tests/Integration/*'
    - if: '$CI_COMMIT_BRANCH == "master"'
  needs:
    - job: build-composer
  cache:
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull
```

You can do the same for the unit and feature tests.

---

## Using Paratest to run even more stuff in parallel

Instead of running all the tests in one suite sequentially, we can [use Paratest to run them in parallel](https://github.com/paratestphp/paratest#paratest){:target="_blank" rel="noreferrer noopener"}. Hopefully, it is as simple as running;

```shell
composer require --dev brianium/paratest
```

and configuring your pipelines to run paratest instead of phpunit:

```yml
test-integration:
  stage: test
  interruptible: true
  script:
    - php vendor/bin/paratest --testsuite integration
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
      changes:
        - '.gitlab-ci.yml'
        - 'phpunit.xml'
        - 'composer.lock'
        - '**/*.php'
        - '*.php'
        - 'tests/Integration/*'
    - if: '$CI_COMMIT_BRANCH == "master"'
  needs:
    - job: build-composer
  cache:
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull
```

For some legacy projects I added paratest to, some tests relied on manipulating global states, singletons, etc, so you might have to enable those checks in phpunit to find why paratest crashes on your test suite.

---

## Code coverage

Code coverage is a valuable tool to see what code is tested and what not. But generally, it is one of the slowest part of pipelines. We can still optimize it though.

### Choosing the right code coverage driver

For years, there wasn't much happening in the coverage world, and everybody was using xdebug. But it was slow, as it wasn't specifically designed to generate coverage statistics, but more as a general debug tool. phpdbg was introduced, and it was a faster alternative. But now the most recent and fastest method to generate coverage data is to use PCOV. 

You want to avoid to have pcov enabled at all times though, so don't enable it in your php.ini. Instead, disable it by default and enable it for your coverage pipelines;

```yml
php-coverage:
  stage: testphp
  interruptible: true
  script:
    - php -dpcov.enabled=1 -dpcov.directory=. -dpcov.exclude="~vendor~" vendor/bin/phpunit --testsuite unit --coverage-cobertura=coverage.cobertura.xml --coverage-html coverage
  artifacts:
    paths:
      - coverage
    reports:
      cobertura: coverage.cobertura.xml
  needs:
    - job: build-composer
  cache:
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull
```

You can also see that the vendor folder is excluded to prevent it from keeping track of executed lines in vendor packages. 

### Only running partial coverage

When running the full coverage as above on every commit, your runners might become very busy and your pipelines very slow, as this is probably the most intensive job in your pipeline. For review purposes, the [line coverage visualization](https://docs.gitlab.com/ee/user/project/merge_requests/test_coverage_visualization.html){:target="_blank" rel="noreferrer noopener"} is the best tool as it gives a visual hint at any untested code in the file diff view.

Instead of running the coverage for our entire project, for this visual hint we only need the coverage of the non-test php files in our app folder. I like to enforce my Unit tests to be in the same class structure as the code it tests. So when there is a class in the folder 'app', 'Application.php' with FQN 'App\Application' the Unit test for that class should be in the 'tests/Unit' folder, be called 'ApplicationTest.php' and have a FQN of 'Tests\Unit\ApplicationTest'. It should also have a @coversDefaultClass mentioning the class it covers. To enforce this, I wrote a custom PHPStan rule:

```php
<?php
declare(strict_types=1);

namespace Development\PHPStan\Rules;

use PhpParser\Node;
use PHPStan\Analyser\Scope;
use PHPStan\Rules\Rule;

class UnitTestClassNameShouldMatchClassNameRule implements Rule
{
	public function getNodeType(): string
	{
		return Node\Stmt\Class_::class;
	}

	public function processNode(Node $node, Scope $scope): array
	{
		/** @var Node\Stmt\Class_ $node */
		if ($node->isAnonymous() || str_contains((string)$node->name, 'AnonymousClass')) {
			return []; // Anonymous classes dont have namespaces
		}

		if ($scope->getNamespace() === null || str_starts_with($scope->getNamespace(), 'Tests\\Unit\\') === false) {
			return [];
		}

		if ($node->isAbstract()) {
			return [];
		}

		$docComment = $node->getDocComment();
		if ($docComment === null || str_contains($docComment->getText(), '@coversDefaultClass') === false) {
			return []; // covered by different rule
		}

		preg_match('/@coversDefaultClass\s+(?<class>[A-z0-9\\\\]+)/', $docComment->getText(), $coverDefaultClass);
		$coverDefaultClassClassName = $coverDefaultClass['class'] ?? null;
		if ($coverDefaultClassClassName === '' || $coverDefaultClassClassName === null) {
			return [];
		}

		$testFqn = $scope->getNamespace() . '\\' . $node->name;
		$expectedTestFQN = str_replace('\App', 'Tests\Unit', $coverDefaultClassClassName) . 'Test';
		if ($testFqn === $expectedTestFQN) {
			return [];
		}

		return ['Unit test should have the same namespace structure as the class they\'re testing. Expected ' . $expectedTestFQN . ', got ' . $testFqn . ''];
	}
}
```

When we organize our Unit tests this way, we can cheat a bit: Whenever a php file in the app folder is changed, we can find the corresponding Unit tests by replacing some parts of the paths, checking if that test actually exists and convert it to the namespace corresponding to that test, so we can instruct phpunit to only run those tests. And this is how you do that:

```yml
test-diff-coverage:
  stage: test
  interruptible: true
  script:
    - git fetch
    - CHANGED_APP_FILES=$(git diff --name-only origin/master... app)
    - echo $CHANGED_APP_FILES
    - EXPECTED_TESTS_FOR_CHANGED_APP_FILES=$(echo $CHANGED_APP_FILES | sed 's/app\//tests\/Unit\//g' | sed 's/\.php/Test\.php/g')
    - echo $EXPECTED_TESTS_FOR_CHANGED_APP_FILES
    - if [ -z "$EXPECTED_TESTS_FOR_CHANGED_APP_FILES" ]; then EXISTING_TESTS_FOR_CHANGED_APP_FILES=$(); else EXISTING_TESTS_FOR_CHANGED_APP_FILES=$(echo $EXPECTED_TESTS_FOR_CHANGED_APP_FILES | xargs ls -d 2>/dev/null || true) ; fi
    - echo $EXISTING_TESTS_FOR_CHANGED_APP_FILES
    - TEST_NAMESPACES_FOR_CHANGED_APP_FILES=$(echo $EXISTING_TESTS_FOR_CHANGED_APP_FILES | sed 's/\//\\\\/g' | sed 's/\.php//g' | sed 's/tests/Tests/g')
    - echo $TEST_NAMESPACES_FOR_CHANGED_APP_FILES
    - PHPUNIT_TEST_FILTER=$(echo $TEST_NAMESPACES_FOR_CHANGED_APP_FILES | sed 's/ /|/g' | sed 's/|$//g')
    - echo $PHPUNIT_TEST_FILTER
    - if [ -z "$PHPUNIT_TEST_FILTER" ]; then echo "No Tests to execute"; else php -dpcov.enabled=1 -dpcov.directory=. -dpcov.exclude="~vendor~" -d memory_limit=1G vendor/bin/phpunit --testdox --coverage-text --coverage-cobertura=coverage.cobertura.xml --filter="$PHPUNIT_TEST_FILTER"; fi
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
      changes:
        - 'app/**/*.php'
        - 'app/*.php'
  needs:
    - job: build:composer
  cache:
    - key:
        files:
          - composer.lock
      paths:
        - vendor/
      policy: pull
  artifacts:
    paths:
      - coverage.cobertura.xml
    reports:
      cobertura: coverage.cobertura.xml
```

The echos between the lines are not necessary, but they are useful for debugging purposes as gitlab hides the actual content of variables in commands as these variables are also used to inject credentials.

Now instead of waiting for 10s of minutes before our coverage data is generated, this runs within a single minute! Faster merge pipelines means faster reviews and earlier merges. But we still want to have a full overview of our coverage to keep track of overall trends and to analyze our codebase. In our case, we run a new pipeline on merges to master that generates a full coverage report in both cobertura and html format.

This concludes the second part of this series. Sit tight for the next part!
