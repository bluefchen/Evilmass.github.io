---
title: 404
toc: false
comments: false
permalink: /404
---

<!DOCTYPE html>

<html lang="en"><head><meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    
    <title>404 _ Page not found</title>
    <meta name="HandheldFriendly" content="True">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="theme-color" content="#e74c3c">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <link rel="shortcut icon" href="https://of4jd0bcc.qnssl.com/Hexo/404.png">
    <style>
        html,body{
            margin:0;
            padding: 0;
        }
        body{
            background-color: #002b36;
        }
        .content{
            width: 600px;
            margin:200px auto;
        }
        a,a:hover{
            text-decoration: none;
        }
/*
 Solarized Color Schemes originally by Ethan Schoonover
 http://ethanschoonover.com/solarized

 Ported for PrismJS by Hector Matos
 Website: https://krakendev.io
 Twitter Handle: https://twitter.com/allonsykraken)
*/

/*
SOLARIZED HEX
--------- -------
base03    #002b36
base02    #073642
base01    #586e75
base00    #657b83
base0     #839496
base1     #93a1a1
base2     #eee8d5
base3     #fdf6e3
yellow    #b58900
orange    #cb4b16
red       #dc322f
magenta   #d33682
violet    #6c71c4
blue      #268bd2
cyan      #2aa198
green     #859900
*/

code,
pre {
    color: #657b83; /* base00 */
    font-family: Consolas, Monaco, 'Andale Mono', 'Ubuntu Mono', monospace;
    text-align: left;
    white-space: pre-wrap;
    word-spacing: normal;
    word-break: normal;
    word-wrap: normal;

    line-height: 1.5;

    -moz-tab-size: 4;
    -o-tab-size: 4;
    tab-size: 4;

    -webkit-hyphens: none;
    -moz-hyphens: none;
    -ms-hyphens: none;
    hyphens: none;
}

pre::-moz-selection, pre ::-moz-selection,
code::-moz-selection, code ::-moz-selection {
    background: #073642; /* base02 */
}

pre::selection, pre ::selection,
code::selection, code ::selection {
    background: #073642; /* base02 */
}

/* Code blocks */
pre {
    padding: 1em;
    margin: .5em 0;
    overflow: auto;
    border-radius: 0.3em;
}

/* Solarized Green */
.keyword {
  color: #859900;
}

/* Solarized Cyan */
.number,
.string {
  color: #2aa198;
}

/* Solarized Blue */
.title,
.name{
  color: #268bd2;
}
a,a:visited,.link {
  color: #cb4b16;
}

/* Solarized Red */
.built_in {
  color: #dc322f;
}

@media only screen and (max-width: 500px) {
    .content{
        width: 90%
    }
}

    </style>
</head>
<body>
<div class="content">
<pre><code>
<span class="function"><span class="keyword">function</span> <span class="title">errorHandler</span>(<span class="params">req</span>)</span>{
  <span class="keyword">if</span> (req.status == <span class="number">404</span>) {
    <span class="built_in">console</span>.log(<span class="string">"Page not found"</span>);
    <span class="built_in">window</span>.location.href = <span class="link"><a href="https://evilmass.cc/">https://evilmass.cc</a></span>;
  }
}
</code></pre>
</div>

</body></html>