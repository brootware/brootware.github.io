---
layout: post
title: Grepping for gold in JSON APIs with jq 🔍 📄
categories:
- General-tech
- Misc
tags:
- General-tech
- Grep
- Linux
- JSON
- Json Query
date: 2022-04-08 19:53 +0800
---
## Introduction

When you are working with cloud, REST APIs or any type of web service, you will come across a lightweight data-interchange format called JSON (JavaScript Object Notation). I won't go much into details about JSON here as we are mainly focusing on filtering and finding data using a tool called Jq (Json Query : a command-line json processor) from these API responses in this tutorial. You can think of Jq like `grep` for JSON APIs.

## Guide on setting up

First we will need to install Jq from <https://stedolan.github.io/jq/download/>. The installation process should be fairly straightforward depending on your operating system.

If you are not on a linux distro or on a windows machine, curl might not be installed by default. You can download binary from [here](https://curl.se/windows/) or follow [this tutorial](https://developer.zendesk.com/documentation/developer-tools/getting-started/installing-and-using-curl/#installing-curl) on installing curl on windows.

## Querying sample API responses

With that out of the way, let's start working with a sample API. Below is a sample query response after curling to the endpoint.

```bash
curl https://api.agify.io/\?name\[\]\=michael\&name\[\]\=matthew\&name\[\]\=jane
[{"name":"michael","age":70,"count":233482},{"name":"matthew","age":36,"count":34742},{"name":"jane","age":36,"count":35010}]
```

We can see that the response is a little hard to read. 🥴

This is where Jq comes into save the day. Below is a sample query response piped with Jq.

```bash
curl https://api.agify.io/\?name\[\]\=michael\&name\[\]\=matthew\&name\[\]\=jane | jq
[
  {
    "name": "michael",
    "age": 70,
    "count": 233482
  },
  {
    "name": "matthew",
    "age": 36,
    "count": 34742
  },
  {
    "name": "jane",
    "age": 36,
    "count": 35010
  }
]
```

You can do a lot more with jq than just prettifying JSON responses. For example, if you want to filter on just michael from the response we can do a query as below.

```bash
curl https://api.agify.io/\?name\[\]\=michael\&name\[\]\=matthew\&name\[\]\=jane | jq '.[0]'
{
  "name": "michael",
  "age": 70,
  "count": 233482
}
```

To further drill down to a value such as `age` within michael's profile we can append the key as below in the query.

```bash
curl https://api.agify.io/\?name\[\]\=michael\&name\[\]\=matthew\&name\[\]\=jane | jq '.[0].age'
70
```

How about only returning values of `name` from the responses?

```bash
curl https://api.agify.io/\?name\[\]\=michael\&name\[\]\=matthew\&name\[\]\=jane | jq '.[].name'
"michael"
"matthew"
"jane"
```

## Querying complex API responses

Alright, let's get into a little more complex API response. Below is an output of a response where the response has a key called `"dataseries"` with an array of nested JSON objects in it.

```bash
curl https://www.7timer.info/bin/astro.php\?lon\=113.2\&lat\=23.1\&ac\=0\&unit\=metric\&output\=json\&tzshift\=0 | jq
{
  "product": "astro",
  "init": "2022040806",
  "dataseries": [
    {
      "timepoint": 3,
      "cloudcover": 1,
      "seeing": 6,
      "transparency": 2,
      "lifted_index": 10,
      "rh2m": 0,
      "wind10m": {
        "direction": "NW",
        "speed": 2
      },
      "temp2m": 30,
      "prec_type": "none"
    },
    {
      "timepoint": 6,
      "cloudcover": 1,
      "seeing": 6,
      "transparency": 2,
      "lifted_index": 10,
      "rh2m": 0,
      "wind10m": {
        "direction": "W",
        "speed": 2
      },
      "temp2m": 29,
      "prec_type": "none"
    },
...
```

If we want to access the speed of the wind from the first object of the `"dataseries"` key we will have to run the query as below.

```bash
curl https://www.7timer.info/bin/astro.php\?lon\=113.2\&lat\=23.1\&ac\=0\&unit\=metric\&output\=json\&tzshift\=0 | jq '.dataseries[0].wind10m'
{
  "direction": "NW",
  "speed": 2
}
curl https://www.7timer.info/bin/astro.php\?lon\=113.2\&lat\=23.1\&ac\=0\&unit\=metric\&output\=json\&tzshift\=0 | jq '.dataseries[0].wind10m.speed'
2
```

If you want to get the wind speed and direction of all `"dataseries"` we can do a query like this.

```bash
curl https://www.7timer.info/bin/astro.php\?lon\=113.2\&lat\=23.1\&ac\=0\&unit\=metric\&output\=json\&tzshift\=0 | jq '.dataseries[].wind10m' 
{
  "direction": "NW",
  "speed": 2
}
{
  "direction": "W",
  "speed": 2
}
{
  "direction": "S",
  "speed": 3
}
...
```

## Bonus : A JSON Path Finder VS Code Plugin and free course for practice

As you deal with more and more complex JSON data, you will need definitely need some help filtering and processing data. Here's a little bonus for VS Code users : You can use this plugin called [JSONPath StatusBar](https://marketplace.visualstudio.com/items?itemName=richie5um2.vscode-statusbar-json-path).

You can simply output a json response to a file to grab the position attribute to clipboard via the status bar of vs code as shown in the screenshot below.

![image](https://github.com/richie5um/vscode-statusbar-json-path/raw/HEAD/resources/jsonpath.png)

We've come to the end of this guide for working with JSON data using curl and jq. Of course there are a lot more stuffs you can do with jq. If you want to get hands on with querying json data you can enroll in a [free course by kodekloud](https://kodekloud.com/courses/json-path-quiz/).
