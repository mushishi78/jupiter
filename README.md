# Jupiter

Jupiter is an implementation of the *static*, static-site generator concept, first implemented with the [Jr.](https://github.com/Xeoncross/jr) project.

Static-site generators, such as [Jekyll](https://github.com/jekyll/jekyll), take markup and template files and generate the files for a static website. When the site is changed, the generator is run again to generate a new set of files.

A static, static-site generator avoids the step of having to run a generator by delivering the markup files straight to the client and using javascript to run the templates on the fly.

[See it in action](http://mushishi78.github.io/jupiter).

## Getting Started

1. [Download Jupiter](http://github.com/mushishi78/jupiter/archive/master.zip) or clone git repository:

  ```
  git clone http://github.com/mushishi78/jupiter my-project
  ```

2. Edit files. (If you don't have a text editor, try [Sublime](http://www.sublimetext.com/)).

3. To serve files in development use a small web server such as [http-server](https://github.com/indexzero/http-server), (otherwise your browser won't be able to follow relative links).

4. Upload to pretty much any website host: [GitHub pages](https://pages.github.com/), [Amazon S3](https://aws.amazon.com/s3/), shared hosting...etc

## Writing Content

### Markdown with YAML FrontMatter

With Jupiter, content is written in [Markdown](http://agea.github.io/tutorial.md/) and metadata is written in [YAML](http://yaml.org/). The latter being placed at the top of the file between two `---` lines, this is known as the FrontMatter. For example:

``` markdown
---
template: article
title: My Funky Article
---

This is some Markdown, specifically a paragraph of text.

* This is a list.
* Unordered list to be precise.

If you need help, try this [Markdown tutorial](http://agea.github.io/tutorial.md/).
```

This would be used to generate a page with the `article` template. The `title` variable, and any other metadata in the FrontMatter, would then be available to the template that generates the page.

### File Type

In order for the file to be read by the browser, they need to use the `.html` file extension, rather the `.md` file extension that a markdown file typically has.

### Including Jupiter

At the end of each content file, the Jupiter javascipt file must be included:

``` html
<script src='/jupiter.js'></script>
```

## Writing Templates

### Hogan & Mustache

Jupiter uses the [Hogan](http://twitter.github.io/hogan.js/) compiler for the [Mustache](https://mustache.github.io/) templating language. This allows the metadata contained in a file's FrontMatter to be used for variables, sections, lambdas...etc

For example, to include a page's heading, you could write:

``` html
<h1>{{ heading }}</h1>
```

Or to iterate over an array of links:

``` html
{{# links }}
  <a href='{{ href }}'>{{ text }}</a>
{{/ links }}
```

### jupiter.js

To save on http requests, templates are in the `jupiter.js` file itself, in the `templates` property of the `Jupiter` object in the lower half of the file.

To avoid repetitive string concatenations/continuations, each template is placed in a multiline comment using [Multiline](https://github.com/sindresorhus/multiline).

Here is an example of what a `jupiter.js` file might look like:

``` javascript
/* ... Some minified dependencies ... */

var Jupiter = {
  /* ... Some parsing and intializing code ... */

  templates: {

    layout: function() {/*
      <head>
        <title>My Site | {{ title }}</title>
        <link rel='stylesheet' type='text/css' href='/app.css' />
      </head>
      <body>
        <main role='main'>

          {{{ yield }}}

        </main>
      </body>
    */},

    post: function() {/*
      <header>
        <h1>{{ heading }}</h1>
        <span class='author'>{{ author }}</span>
      </header>

      {{{ content }}}

    */}
  }
}
```

### Template

Every markdown file needs to specify which template to use by setting the `template` variable in it's FrontMatter. The compiled markdown will then be available to the template via the `content` variable.

### Layout

As it's common to use the same basic layout for all the pages on a site, Jupiter assumes the presence of a `layout` template, which will be wrapped around all page templates. The rendered page template is available to the layout via the `yield` variable.

### Escaping

As both the compiled markdown in `content` and the rendered template in `yield` will contain HTML elements, they need to be inserted into the template using the unescaped mustache syntax

Either:

```
{{{ content }}}
```

Or:

```
{{& content }}
```

## Tips and Tricks

### Degrading Gracefully

For browsers that do not run javascript, the markdown file will be displayed with the whitespace removed maing it difficult to read. If you'd like it maintain the original whitespace you can open a `pre` tag at the begining of the file like so:

``` markdown
<pre>
---
template: article
title: My Funky Article
---

This is some Markdown......
```

### Hiding from the Browser

Sometimes when writing code examples, the content will be read by the browser before Jupiter parses it and elicit unwanted behaviour. To hide text from the browser, enclose it in the following:

```
[//]: # (<script type='text/ignore-this'>)

... Some dangerous text ...

[//]: # (</script>)
```

This works because any script type that a browser doesn't recognise its contents is ignored. Then when being parsed as markdown the `script` tags are treated as comments and ignored.

However, if you're code examples involve writing `script` tags, be wary that a closing `script` tag will close the surrounding tag and the subsquent text will no-longer be hidden.

As such, in some circumstances it may be better to escape the code:

```
<code>&lt;script src='/do-not-run-me.js'&gt;&lt;/script&gt;</code>
```

## Contributing

1. [Fork it](https://github.com/mushishi78/jupiter/fork)
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request
