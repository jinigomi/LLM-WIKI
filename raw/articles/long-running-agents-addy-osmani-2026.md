---
source_url: https://addyosmani.com/blog/long-running-agents/
ingested: 2026-05-01
sha256: 26d53cf91a9ebcd81e428724c3e4aab59bbea57e5c0a2cd276e3d164eb16c905
---

<!DOCTYPE html>
<html lang="en">
<head>
    <!-- Why hello, view-sourcerer! -->
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  
  

  <meta name="description" content="A long-running agent can keep making progress over hours, days, or weeks. It can do this across many context windows and sandboxes, recover from failure, lea...">
  <meta name="author" content="Addy Osmani">
  <meta name="twitter:creator" content="@addyosmani">
  <meta name="twitter:url" content="https://addyosmani.com/">
  <meta name="twitter:site" content="@addyosmani" />
  <meta name="twitter:description" content="A long-running agent can keep making progress over hours, days, or weeks. It can do this across many context windows and sandboxes, recover from failure, lea..." />
  <meta name="twitter:card" content="summary_large_image" />
  <meta name="twitter:title" content="Long-running Agents" />
  
  <meta property="og:image" content="https://addyosmani.com/assets/images/long-running.jpg" />
  <meta name="twitter:image" content="https://addyosmani.com/assets/images/long-running.jpg" />
  <meta name="twitter:image:src" content="https://addyosmani.com/assets/images/long-running.jpg" />
  
  <meta property="og:locale" content="en_US" />
  <meta property="og:title" content="Long-running Agents">
  <meta property="og:description" content="A long-running agent can keep making progress over hours, days, or weeks. It can do this across many context windows and sandboxes, recover from failure, lea...">
  


  <link rel="alternate" type="application/rss+xml" title="AddyOsmani.com RSS Feed" href="http://addyosmani.com/rss.xml">

  <script type="application/ld+json">
    {
      "@context": "https://schema.org/",
      "@type": "Person",
      "name": "Addy Osmani",
      "url": "https://addyosmani.com",
      "image": "https://addyosmani.com/assets/photos/Addy2021.jpg",
      "sameAs": [
        "https://twitter.com/addyosmani",
        "https://www.instagram.com/addyist",
        "https://www.linkedin.com/in/addyosmani/",
        "https://www.youtube.com/addyosmani",
        "https://github.com/addyosmani/",
        "https://addyosmani.com",
        "https://www.facebook.com/addyosmaniofficial/",
        "https://golden.com/wiki/Addy_Osmani_(software_engineer)-REKEB86",
        "https://www.oreilly.com/pub/au/5271",
        "https://web.dev/authors/addyosmani/"
      ],
      "jobTitle": "Software Engineer, Author",
      "worksFor": {
        "@type": "Organization",
        "name": "Google"
      }  
    }
    </script>
  <title>AddyOsmani.com - Long-running Agents</title>
  <link rel="apple-touch-icon" sizes="57x57" href="/assets/images/favicons/apple-touch-icon-57x57.png">
<link rel="apple-touch-icon" sizes="60x60" href="/assets/images/favicons/apple-touch-icon-60x60.png">
<link rel="apple-touch-icon" sizes="72x72" href="/assets/images/favicons/apple-touch-icon-72x72.png">
<link rel="apple-touch-icon" sizes="76x76" href="/assets/images/favicons/apple-touch-icon-76x76.png">
<link rel="apple-touch-icon" sizes="114x114" href="/assets/images/favicons/apple-touch-icon-114x114.png">
<link rel="apple-touch-icon" sizes="120x120" href="/assets/images/favicons/apple-touch-icon-120x120.png">
<link rel="apple-touch-icon" sizes="144x144" href="/assets/images/favicons/apple-touch-icon-144x144.png">
<link rel="apple-touch-icon" sizes="152x152" href="/assets/images/favicons/apple-touch-icon-152x152.png">
<link rel="apple-touch-icon" sizes="180x180" href="/assets/images/favicons/apple-touch-icon-180x180.png">
<link rel="icon" type="image/png" href="/assets/images/favicons/favicon-32x32.png" sizes="32x32">
<link rel="icon" type="image/png" href="/assets/images/favicons/android-chrome-192x192.png" sizes="192x192">
<link rel="icon" type="image/png" href="/assets/images/favicons/favicon-96x96.png" sizes="96x96">
<link rel="icon" type="image/png" href="/assets/images/favicons/favicon-16x16.png" sizes="16x16">
<link rel="manifest" href="/manifest.json">
<link rel="mask-icon" href="/assets/images/favicons/safari-pinned-tab.svg" color="#5bbad5">
<meta name="msapplication-TileColor" content="#ffc40d">
<meta name="msapplication-TileImage" content="/assets/images/favicons/mstile-144x144.png">
<meta name="theme-color" content="#eb298c">

  <link rel="stylesheet" href="/assets/css/style.css">
  
  <link rel="stylesheet" href="/assets/css/post-page.css">
  <link rel="stylesheet" href="/assets/css/highlight.css">
  
  
  <link rel="alternate" type="application/rss+xml" title="My Blog" href="/rss.xml">
</head>
<body>

  <style>
@media only screen and (max-width: 700px) {
    .mobile-hidden {
        display:none;
    }
}
</style>
<nav class="main-nav">
    
    <a href="/"> <span class="arrow"></span> Home </a>
    

    
    
    

    <a class="mobile-hidden" href="https://github.com/addyosmani">GitHub </a>

    <a class="mobile-hidden" href="/press">Press </a>