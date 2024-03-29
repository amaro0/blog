---
title: "templ vs html/template performance benchmark"
slug: "go-templ-benchmark"
date: 2023-11-04T22:01:36+01:00
tags: [ "go", "benchmark", "html", "benchstat", "performance", "templating", "template" ]
---

### Introduction

Performance benchmark of native [html/template](https://pkg.go.dev/html/template) and [templ](https://templ.guide/).
Source code available at https://github.com/amaro0/templ-benchmark.

**TLDR:** Templ is way faster.

After discovering how the code generated by Templ is incredibly procedural, I thought, 'Wow, it must be fast.' and yeah
it is, at least when you compare it to the native html/template. Benchstat results at the bottom

### Methodology

This micro benchmark compares native html/template and templ in three simple scenarios.

#### Simple p tag

```html
<div>Hello, {{ .Name }}</div>
```

#### Table

Table has 1000 rows and is generated before benchmark.

```html
<p>Table: {{ .TableName }}</p>
<table>
    <tr>
        <th>Id</th>
        <th>Name</th>
        <th>Number</th>
        <th>Complex</th>
        <th>Static</th>
    </tr>
    {{ range .Items}}
    <tr>
        <td>{{ .Id }}</td>
        <td>{{ .Name }}</td>
        <td>{{ .Number }}</td>
        <td>
            <div>
                <p>{{ .Id }}</p>
                <p>{{ .Name }}</p>
                <p>{{ .Number }}</p>
            </div>
        </td>
        <td>Static</td>
    </tr>
    {{ end}}
</table>
```

#### Layout with table in body

table component is reused from previous test

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1"/>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@picocss/pico@1/css/pico.min.css"/>
    <title>Hello, world!</title>
</head>
<body>
{{template "table" .}}
</body>
</html>
```

### Results

```text
goos: linux
goarch: amd64
pkg: templ-benchmark
cpu: 11th Gen Intel(R) Core(TM) i7-1165G7 @ 2.80GHz
         │    templ     │                  native                  │
         │    sec/op    │     sec/op      vs base                  │
Hello-8    174.7n ± 48%     971.6n ± 58%   +456.31% (p=0.000 n=10)
Table-8    740.9µ ±  4%   11551.7µ ± 33%  +1459.07% (p=0.000 n=10)
Layout-8   752.2µ ± 11%   11544.0µ ± 31%  +1434.61% (p=0.000 n=10)
geomean    46.00µ           506.0µ        +1000.00%

         │    templ     │                native                │
         │     B/op     │     B/op      vs base                │
Hello-8      200.0 ± 0%     368.0 ± 0%  +84.00% (p=0.000 n=10)
Table-8    2.643Mi ± 0%   4.835Mi ± 0%  +82.92% (p=0.000 n=10)
Layout-8   2.643Mi ± 0%   4.830Mi ± 0%  +82.73% (p=0.000 n=10)
geomean    112.7Ki        206.5Ki       +83.22%

         │    templ    │                 native                 │
         │  allocs/op  │  allocs/op    vs base                  │
Hello-8     5.000 ± 0%    13.000 ± 0%   +160.00% (p=0.000 n=10)
Table-8    2.018k ± 0%   56.781k ± 0%  +2713.73% (p=0.000 n=10)
Layout-8   2.017k ± 0%   56.780k ± 0%  +2715.07% (p=0.000 n=10)
geomean     273.0         3.474k       +1172.28%
```

Without any surprises, Templ achieves significant victories in all categories. Personally I've been enjoying writing
code for this benchmark and win definitely use templ in the future projects. Performance aside, templ docs are
extensive, you can use simple types in templates(no need for unnecessary structs) and you are writing go code in
templates. Two things could be improved:
plugin for goland(possibly on the way) and error messages could use some polishing. Overall, I highly recommend using
Templ. It's possibly the best templating engine I've ever used. 