# wnduan.github.io

I've been using Jekyll Bootstrap (JB) to host my GitHub Page for a while until the new release of Jekyll 3.0. Jekyll Bootstrap really made everything easy and nice then. Unfortunately there are conflicts between Jekyll Bootstrap v0.3.0 (2012) and the new release of Jekyll 3.0. And it seems that the developer of JB
will not fix the issue recently.

GitHub Page turned to Jekyll 3.0 for a while, and my blog was gone because the conflict issue. In order to bring it back, I finally decided to try to build my personal blog site with Jekyll 3.0 myself.

I'd like to make it a simple and clean one. So everything here starts from running 'jekyll new' command. The default appearance looks good for me, however I'd like to do some customizations.

## Updates

- 2016-04-15:
  - Add an {% elsif %} branch in \<header\> of post.html. The control sequence is: if the author name is given in the post as page.author then display `page.author`. Else if the author name is defined in `_config.yml` file display `site.author`. So we had a global author name while we could control the author name separately.
  - Add an author name to `_config.yml` file with `author: Duan`.
  - Change background color for code blocks.
  - Set line numbers for code using \{% highlight <lang> linenos %\}, while the code using code fence "\`\`\`" remain without line number. With reference to [minh's solution](http://www.minh.io/blog/2015/06/28/jekyll-line-numbers/)
  - Changed the font-size of `Posts` part in `_layout.sass`.
  - Add Disqus teatures. With reference to [Sechter's solution](http://sgeos.github.io/jekyll/disqus/2016/02/14/adding-disqus-to-a-jekyll-blog.html)
- 2016-04-13:
  - Published new post "2016-04-13-get-the-latest-version-of-zsh"
- 2016-04-12:
  - Set up the config file
  - Change background color
  - Change the font size of post title in index Page
  - Change the magrin of post title in index Page
  - Add mathjax line to head.html
  - Add copyright and host lines at the bottom
- 2016-04-07:
  - Build the site locally.


Under construction and configuration...
