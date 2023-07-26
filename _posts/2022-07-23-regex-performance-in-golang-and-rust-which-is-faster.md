---
layout: post
title: Regex performance in golang and rust. Which is faster? 🤔
categories:
- Projects
- regex-benchmarking
tags:
- Projects
- regex-benchmarking
- Golang
- Rust
- Data-redaction
- data-security
- log-redaction
render_with_liquid: false
date: 2022-07-23 20:31 +0800
---
## Introduction

![govsrust](https://bn1304files.storage.live.com/y4m5enid01oWP4nY4T9-JbCMae1YcrKYl9mZZMDlvToSHzAFEzd7SnVVJ9G0WB9vjEK7CtM1M-3UAXXApf-hxy_gEiM86J0KrGZ8dagQve6Z_jQYBZ-vnjJBDfwY4-KD02GA_puH508PcFHi9qGVfp9nCNVQMLrttqP5olLUKNr_dveADV45L6d77pl6vH3tYtT?width=744&height=350&cropmode=none)

In the previous blog entry, we looked at the implementation details of PyRedactKit for improving performance, reducing code smells by refactoring, Using automated testing and code scanning with Github Actions. The heart of the cli app is working with regular expressions to identify any sensitive data are found in the ascii text files and mask them if any.

The initial constraints I had to work with are:

* The tool needs to run cross platform
* Rapidly prototype new features and push them out asap
* Most of the target audience have Python installed on their devices

The tool works great! It has been packaged and started rolling into production. So What next? I heard good things about Go and Rust being performant as compiled languages. Go being good at concurrency for web services and Rust being good at systems performance. However, there is very limited information available for regular expressions performance of each with my specific use case.

Thus, I decided to test things out myself and document my findings in this blog. Hopefully this would help someone and give insights to make decisions on their future projects with regards to regex.

## Baseline setup

The baseline setup for both of the languages are simple. We will be testing the following of each standard regex libary for Go and Rust:

* Regex Match
* Regex Replace

For these two functions, I try to find the closest regex function for each language to run the benchmark against. The setup is simple, there is a chunk of data. We will match IP addresses in the data for one function and another function will be replacing the matched IP address with some other string. The two functions will be looped and timed for each.

You can find the full code repository for it here.
<https://github.com/brootware/regex-performance-benchmark>

## Running the benchmark

The scripts are first run without being compiled into a binary for 5 times and the results are averaged out to reduce any outliers.

Next, I repeated the same process with compiled binaries for each language. Do note that these binaries are compiled for release, meaning all the debugging and other dependencies are stripped out.

## Results

You can check out the benchmark results below. Currently this is only ran on my macbook. (I am looking to run this in some of my cloud instances when I have time.) You can also run it in the meantime to see the results for yourself.

<https://docs.google.com/spreadsheets/d/1Fg0r0emUTjItqlrCrVgJh6dgUwMJI0GhvSDsrgGpdD8/edit?usp=sharing>

The results for running without compilation are fairly simple. Go performed better than Rust by a second. This was a bit surprising as I thought Rust will do better according to the internet anecdotes. So I started rooting for Go.

![runperformance](https://bn1304files.storage.live.com/y4mH8hsZauKUUkjEZY6roZGRE3PNxZ3PaEmAfCXIRogUJTVqqNWBZ2x-OCInfa4h6qP4_0n5KboNwXfBEO_L26vPkz5k-Cy1ilpHwy_gD2neolrNrJcqII08lE0yyAUKcDXOYmMDimxGR6VsL5v2sU8uSrPd-dsuzsmlK0TD1Pa92Aa_mbPMd6sJyV42IbZGx-U?width=600&height=371&cropmode=none)

However, Rust blew Go out of the water as a compiled binary. The regular expression matching and replacing functions took less than a second to run which is really incredible while Go still took around the same time to run as a binary!

![binaryperformance](https://bn1304files.storage.live.com/y4mmtPcNM3svRX-433l7yj0lxKTCJO_9kxduoqgxb6aISqgoBDut4ZfzMbWTBmI_Iuan4PLCNC6_1WBpg8ClhizKfwTPvInlyPeVyEJt9SI1pOCrBBQvjsg1XoKRiEEaz6ntNRwMJAdlwmoPu3UgQ2dUQnQoWc62sHRS-VDXRoDCTv0YHEP-9_hJZH_12JJ28mC?width=600&height=371&cropmode=none)

Hope this gave you some insight into regular expression libraries for each of the languages. So for the next project that you going to be working on with anything related to regular expressions, you might want to use Rust.
