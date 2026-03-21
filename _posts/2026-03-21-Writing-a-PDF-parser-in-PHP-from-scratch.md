---
title: Writing a PDF parser in PHP from scratch
subtitle: A project years in the making
Description:
categories: [development]
tags: []
readtime: 
---

Over the last decade I've worked at several large companies, and almost every one had some sort of PDF extraction in part of the codebase. PDFs have become the preferred format for companies to share data. And even though PDFs will almost always look identical across devices and readers, accessing their underlying data is very complicated, and therefore this is a very inaccessible format to retrieve data from.

About 4 years ago I worked at a small company that provides other companies with price data, including searchable promotional brochures. Part of the job was to make sure that all data was properly ingested. A lot of time was spent on PDFs that were insanely large (100's of megabytes), or would not parse in ['smalot/pdfparser'](https://github.com/smalot/pdfparser){:target="_blank" rel="noreferrer noopener"}, the most popular PDF Parser library in PHP.

During my time at that job I opened several PRs for the repository to fix issues with text and image extraction. But due to the complexity of that repository and my lack of understanding of the PDF specification, I never had the opportunity to fix some of the deeper issues that caused missing text, decoding issues or memory leaks.

After all these issues we started experimenting with executing commands of server side dependencies. But getting the text content of a pdf requires different dependencies than to get the title, creation date or images from a pdf. We also experimented with [pdf.js](https://github.com/mozilla/pdf.js){:target="_blank" rel="noreferrer noopener"}. To get all the data we needed though, we ended up building an API that returned all the data in a predetermined format that could then be consumed by PHP. But any new field we wanted to extract, any new information meant updating that API and a whole tree of dependencies.

What we were missing was a PHP api that had all the objects fully typed and we didn't have to maintain on our own. A package that we could easily contribute to. And one that was understandable for new contributors.

On May 6th, 2022 I created a new repository because I was looking for a new challenging project: ['prinsfrank/pdfparser'](https://github.com/PrinsFrank/pdfparser){:target="_blank" rel="noreferrer noopener"}, with the idea of "How hard can it be?". I built some basic parsing in May, then a few more commits in August and September. I bit more work In January. And then I gave up because it turned out to be too challenging.

But that wasn't the end of the repository, because otherwise this wouldn't have been a blog post. I picked up the pace in October 2024, and in February version 1.0 was released. It worked in extracting text, but it didn't detect newlines and positional context. On May 19th 2025, version 2.0 was released, which did add support for positional context and image extraction!

Since then a lot of new features have been added. The library now has support for extracting lots of different types of images, and I'm currently working on support for encrypted PDFs!
