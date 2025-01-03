---
layout: post
title: "How to Make Your Own Blog"
date: 2024-09-24 16:23 +0800
tags: [Guide,IT,Web]
comments: true
author: DavidX
---
## 0. Introduction

After my own try at making this blog, I find it quite easy to make one. You only need a GitHub account!

## 1. Preparing the Files

I\'ve prepared an empty blog sample [here](https://github.com/Davidasx/blog-framework-template). It\'s based on [Jekyll Now](https://github.com/barryclark/jekyll-now) and I added some small improvements.

The sample itself, if deployed without modification, looks like [this](https://davidasx.github.io/blog-framework-template/). With a bit of configuration, you can make a blog of your own.

First, fork that repository and remember to name it as YOUR_GITHUB_HANDLE.github.io. That way, you will have the framework of the blog in your own hands.

Next, upload avatar.png and favicon.ico to the image folder. These are your avatar and the icon of the website respectively.

Finally, place your information into the _config.yml and you\'re all set!

Now, push to GitHub and wait for a minute. If everything goes well you\'ll see an empty blog of your own.

## 2. Hit counter

The hit counter counts the number of visits to your site. [Here](https://cutercounter.com) is a free hit counter.You can choose a style and the number of digits. Then you\'ll get a HTML code snippet like this:

```HTML
<!-- Start of CuterCounter Code -->
<a href="https://www.cutercounter.com/" target="_blank"><img 
src="https://www.cutercounter.com/hits.php?id=???&nd=???&style=???" border="0" 
alt="blog counter"></a>
<!-- End of CuterCounter Code -->
```

Copy the second link and replace the link in this:

```HTML
<div align="center">
  <span class="counter">
    Number of Visitors:
    <img src="https://www.cutercounter.com/hits.php?id=???&nd=???&style=???" 
    border="0" alt="Visitors">
  </span>
</div>
```

Then, append this code to index.html, _layouts/page.html and _layouts/post.html. Save and push to GitHub. As before, wait for a minute and refresh the page. If all goes well, a hit counter will appear at the bottom of the page.

## 3. Start writing!

Now, start your own blog! You can put your personal information in about.md, which will automatically be shown in the About page of your blog.

Any blog should be written in a markdown file _posts/YYYY-MM-DD-SHORT_TITLE_OF_BLOG.md. Please put the following at the beginning of the file:

```Markdown
---
layout: post
title: "Your Blog Title"
date: YYYY-MM-DD
tags: [tag1,tag2,...]
comments: true
author: DavidX
---
```

(The three dashes aren\'t separators of the code block, they should be included in your markdown as well. In preview, the front matter would look like a table.)

Then, write your blog in Markdown just as usual!

Note: You\'d better use 2nd-level header as the highest level because the title of the blog itself is displayed in 1st-level header.
