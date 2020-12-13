---
layout: post
title:  "Jekyll + MathJax + CMU Serif = Machine Learning Blog"
date:   2019-03-31 21:54:56 +0200
categories: jekyll update
---
After fighting battles with WordPress on and off for over a year, I decided to look for new blog software that would give me the following:

* Attractive $$ \LaTeX $$ markup that integrates seamlessly into posts
* The ability to embed Computer Modern fonts
* Powerful support for code snippets

Essentially, I wanted a blog that looks like a mathematics paper with the occasional appearance of some lines of code. A dream blog, if you will, for a mathematician, theoretical physicist, or data scientist.

Enter [Jekyll][jekyll-gh], a worthy alternative to WordPress.

When Donald Knuth's wonderful Computer Modern font is used, we get a blog with the potential to show mathematics properly. When equipped with [MathJax][mathjax], Jekyll beautifully renders both inline mathematics like $$ I_p(x) = -\log p(x)$$ and display-style mathematics:

$$ H(p,q) = \sum_{x\in X} p(x)\, I_q(x). $$   

Code snippets also look great:

{% highlight python %}
class BCELoss(WeightedLoss):
  __constants__ = ['reduction', 'weight']
  def __init__(self, weight=None, size_average=None, reduce=None, reduction='mean'):
    super(BCELoss, self).__init__(weight, size_average, reduce, reduction)
  @weak_script_method
  def forward(self, input, target):
    return F.binary_cross_entropy(input, target, weight=self.weight, reduction=self.reduction)
{% endhighlight %}
As expected, the setup was non-trivial, and there are challenges to typesetting mathematics with Jekyll that may be unfamiliar to those who have experience with LaTeX. I'll review some of these issues here.

## Jekyll

Jekyll is a Ruby gem (i.e. a library written in the Ruby language). Provided that you already have Ruby, RubyGems (Ruby's standard package manager), GCC, and Make installed, then installing Jekyll is straightforward:
{% highlight shell %}
$ gem install bundler jekyll
$ jekyll new my-awesome-site
$ cd my-awesome-site
/my-awesome-site $ bundle exec jekyll serve
{% endhighlight %}
The first command uses RubyGems to install both Jekyll and [Bundler](https://bundler.io/), the latter of which is a package manager that, among other things, checks whether all gem dependencies are available for a given application. The second command creates a new local directory for a Jekyll website. After changing to that website's directory, we can serve the website locally with the last command. Local serving gives us the opportunity to develop and debug our website.

The next step is to create an `index.html` to verify the installation:
{% highlight html %}
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Home</title>
  </head>
  <body>
    <h1>Hello World!</h1>
  </body>
</html>
{% endhighlight %}
After we see our Hello World page, we can do one of two things. Either we can get a handle on general Jekyll website design by reading the amazing [step-by-step tutorial][tutorial], or we can `git clone` a pre-designed theme and then repurpose it for our needs. I chose the latter, picking Jekyll's [Minima][minima] theme. After cloning it to a separate directory, I created a new directory called "Math-Minima" and copied the Minima project into Math-Minima.

Minima has the following directory structure, which is typical for a Jekyll theme:

{% highlight shell %}
├── _includes
├── _layouts
├── _posts
├── _sass
│   └── minima
├── assets
│   └── css
└── script
{% endhighlight %}

These directories consist of the following:
* `_includes` : HTML and JavaScript for headers and footers; JavaScript for search engine optimization
* `_layouts` : HTML templates for various types of pages
* `_posts` : Markdown files for blog post content
* `_sass` : Stylesheets for headers, footers, fonts, links, etc.
* `assets` : A CSS directory in which the main stylesheet is built; other accoutrements for the website (images, video, etc.)
* `script` : Shell scripts for building the website

After a Jekyll theme is completed, we build our website in three steps. First, in the theme directory we input to Bundler a `.gemspec` file and get as output a `.gem` file containing all the visual design information for our site. This is done with a command that reads something like `gem build math-minima.gemspec`. Next, we move to the website directory and indicate our website's gem dependencies (including the version of the theme) in the website's `Gemfile`. We then use the command `bundle` to produce a new incarnation of our blog.

## MathJax

MathJax is an open-source JavaScript display engine for LaTeX and other mathematics markup languages. One can find MathJax on content delivery networks all over the web. In particular, many variants of MathJax can be found at [CDNJS](https://cdnjs.com/).

To use MathJax, we place the following code in `_includes/head.html`, immediately before `<\head>`:
{% highlight html %}
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML" async>
</script> 
{% endhighlight %}


## CMU Serif

To incorporate CMU Serif fonts, we work in the `assets` and `_sass` directories. In particular, we place a `fonts` folder, consisting of CMU Serif fonts, inside `assets`. This folder and the files in it will be referenced in a CSS file in the `_sass` directory.

{% highlight shell %}
├── _sass
│   ├── math-minima
│   │   ├── _base.scss
│   │   ├── _fonts.scss
│   │   ├── _layout.scss
│   │   └── _syntax-highlighting.scss
│   └── math-minima.scss
{% endhighlight %}

In `_sass`, we have `math-minima.scss` as our main stylesheet. This stylesheet imports *partial stylesheets* located in `_sass/math-minima`. The original Minima theme comes equipped with `_base.scss`, `_layout.scss`, and `_syntax-highlighting.scss`. The partial `_fonts.scss` is our new addition. It contains `@font-face` rules that upload CMU Serif to our end users.

To maximize compatibility among different browsers, it's good practice to source as many file types as possible of the same font:
{% highlight scss %}
@font-face {
    font-family: "Computer Modern";
    src: 
        url('../fonts/otf/cmunrm.otf') format("opentype"),
        url('../fonts/eot/cmunrm-webfont.eot'),
        url('../fonts/eot/cmunrm-webfont.eot?#iefix') format("embedded-opentype"),
        url('../fonts/svg/cmunrm-webfont.svg') format("svg"),
        url('../fonts/woff/cmunrm-webfont.woff') format("woff"),
        url('../fonts/ttf/cmunrm-webfont.ttf') format("truetype");
}
{% endhighlight %}


## TeXing with Markdown

[Markdown][markdown] is a markup language that is used for the rapid production of rich text documents, such as Jupyter notebooks and Jekyll blog posts. Typesetting mathematics with Markdown is relatively straightforward, though there are some quirks that merit attention.

Inline mathematics is produced with *two* dollar signs instead of one. For example, `$$\tilde G = \mathbb{R}^2\ltimes O(2)$$` gives us $$\tilde G = \mathbb{R}^2\ltimes O(2)$$. Alternatively, one could also use `\\(\tilde G = \mathbb{R}^2\ltimes O(2)\\)`. Note the *double* backslash. The first backslash is used to escape the second for the proper syntax.

Display mathematics may also be produced with two dollar signs, provided that a carriage return precedes the first pair of dollar signs and succeeds the second pair. Another option is to use double backslashes. For instance, \\[ H(p,q) = -\int_X p(x)\, \log q(x)\, d\mu(x)\\] is typeset via `\\[ H(p,q) = -\int_X p(x)\, \log q(x)\, d\mu(x)\\]`, which mimics LaTeX's shorthand for a display equation.

For aligned equations, we have blackslash explosion. For example, to produce  
\\[
\begin{align\*}
r &= \begin{pmatrix} R & 0 \\\\ 0 & 1  \end{pmatrix} \\\\\
t &= \begin{pmatrix} I & T \\\\ 0 & 1  \end{pmatrix}
\end{align\*}
\\]
we write
```latex
\\[
\begin{split}
r &= \begin{pmatrix} R & 0 \\\\ 0 & 1 \end{pmatrix} \\\\\\\\
t &= \begin{pmatrix} I & T \\\\ 0 & 1 \end{pmatrix}
\end{split}
\\]
```
Aside from these minor deviations from standard LaTeX, I haven't run into any issues. These keyboard gymnastics seem like a small price to pay for the display quality.

## Deployment

There are many ways to fire up a Jekyll website, and Jekyll's [deployment page][deploy] addresses options for both manual deployment and automated deployment.

Once built from a Jekyll theme, a Jekyll website has the following directory structure:

{% highlight bash %}
├── _posts
│   └── _site
└── _site
    ├── about
    ├── assets
    │   ├── css
    └── jekyll
        └── update
{% endhighlight %}

A simple Jekyll website development workflow is as follows. We can run `jekyll serve` in our website's root directory, open the website by typing `localhost:4000` in our browser, edit our Markdown files, then view our changes by refreshing the website. The Markdown files for blog posts are found in `_posts`. Each time we save a change to a Markdown file, the Jekyll
server will "regenerate" the website:

{% highlight bash %}
Regenerating: 1 file(s) changed at 2019-04-07 18:27:58
              _posts/2019-04-01-jekyll-mathjax-cmuserif-mlblog.md
 Jekyll Feed: Generating feed for posts
              ...done in 0.235377 seconds.
{% endhighlight %}

Here, regeneration just means that the newly saved Markdown files are used to produce blog content as HTML files. These files are saved in `_posts/_site`. The website content is stored in `_site`. (Note: *not* `_posts/_site`.)

After blog content is written and vetted, we need to put it up for other people to see. There is no shortage of methods for doing this, and the choice of method depends upon a developer's needs.

In my case, I have a web server that was configured for use with Wordpress. When I first purchased it, I didn't worry about server hardware, server software, root access, and so forth. Thus I ran into a buzzsaw of complications when I looked into deployment options.

Perhaps the simplest method in my case is to copy the contents of `_site` to `~/public_html` on my web server. This, of course, is a boring, manual solution. Moreover, I was condemned to use FileZilla in order to get `_site` up to the server, which didn't appeal to me.

I decided to use Git to automate the deployment. I set up a bare Git repository on my web server. Next, I set up a Git repository in my local website's root folder. I added the bare repo to my local repo as a remote, then pushed from the local repo to the bare remote.

It turns out that it's not best practice to push to remote repositories that are not bare. In fact, Git will impede us from doing so! Assume that we've initialized a non-bare repository on a remote server, which is then added as a remote, called `origin`, to a local repository. If we `git push origin master`, then Git responds with:

{% highlight bash %}
remote: error: refusing to update checked out branch: refs/heads/master
remote: error: By default, updating the current branch in a non-bare repository
remote: error: is denied, because it will make the index and work tree inconsistent
remote: error: with what you pushed, and will require 'git reset --hard' to match
remote: error: the work tree to HEAD.
remote: error:
remote: error: You can set 'receive.denyCurrentBranch' configuration variable to
remote: error: 'ignore' or 'warn' in the remote repository to allow pushing into
remote: error: its current branch; however, this is not recommended unless you
remote: error: arranged to update its work tree to match what you pushed in some
remote: error: other way.
remote: error:
remote: error: To squelch this message and still keep the default behaviour, set
remote: error: 'receive.denyCurrentBranch' configuration variable to 'refuse'.
To (remote-repo)
 ! [remote rejected] master -> master (branch is currently checked out) 
{% endhighlight %}

The lesson here is that Git prefers that we not push into a non-bare repository whose checked out branch matches the checked out branch of the local repository. However, if the checked out branch of the remote is not the branch that corresponds to the content that we want to show, then we can't deploy our content. On the other hand, if our remote repository is bare, then it won't contain our website information! In particular, we won't be able to access `_site` from our remote repository.

One technique for dealing with this issue is to set up a *second* repository on the web server. This second repo will be non-bare. The repo can `git pull` from the bare repository each time new content is pushed from the local repo to the bare remote repo. Git's [hooks][hooks] can automate these pulls.

In this case, we need a *post-receive hook*. This will be a Bash script that indicates the location of the work tree in the non-bare repo, then issues the pull command for the non-bare repo:

{% highlight bash %}
#!/bin/sh
GIT_WORK_TREE=~/blog/blog-bare GIT_DIR=~/blog/blog-bare/.git git pull origin master
exit
{% endhighlight %}

The final piece in this puzzle is to [symbolically link][symlink] `public_html` to `_site`, which in effect moves the contents of `_site` to `public_html`. At last, we can develop locally, then publish our blog content with a simple `git push origin master` command.

## Further Comments

A more sophisticated workflow employs a post-receive hook to automate the Jekyll build process. This is covered in Jekyll's [Automated Deployment page][autodeploy]. In my case this doesn't work, because my server has outdated versions of Ruby and RubyGems, and I lack the root access needed to install newer versions. It's also not necessary for me to build on the server.

Another option is to deploy directly from GitHub by using GitHub Pages, but I'm not sure how to incorporate CMU Serif using this method. I also like the idea of having my name in my URL, and GitHub Pages doesn't permit this.

The most sophisticated and flexible deployment method seems to use a continuous integration tool such as [Jenkins][jenkins], [Travis CI][travis], or [CircleCI][circleci]. These tools, however, are meant for scalable deployment and seem to be overkill for my purposes.

I really enjoyed this exploration, as it gave me a chance to dig a little bit into front-end development. I'm definitely looking forward to blogging about mathematics with a proper font and a proper typesetting framework!

[jenkins]: https://jenkins.io/
[travis]: https://travis-ci.org/
[circleci]: https://circleci.com/
[symlink]: https://en.wikipedia.org/wiki/Symbolic_link
[autodeploy]: https://jekyllrb.com/docs/deployment/automated/
[hooks]: https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks
[minima]: https://github.com/jekyll/minima
[tutorial]: https://jekyllrb.com/docs/step-by-step/02-liquid/
[deploy]: https://jekyllrb.com/docs/deployment/
[mathjax]: https://www.mathjax.org/
[jekyll-gh]:   https://github.com/jekyll/jekyll
[markdown]: https://en.wikipedia.org/wiki/Markdown