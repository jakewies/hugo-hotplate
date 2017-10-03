# Hugo hotplate

### What is this?

A pre-configured development setup for creating static websites using [Hugo](https://gohugo.io/). It is **not** a boilerplate for building a hugo theme. 

### How do I use it?

First you are going to want to clone the project locally and install dependencies using `npm`.

```
git clone git@github.com:jakewies/hugo-hotplate.git

cd hugo-hotplate

npm install
```

Then just run `npm run dev` and Hugo will start a local development server listening on http://localhost:1313.

### Project purpose

Hugo is very user friendly and makes most things a breeze to do, but it lacks a proper asset pipeline. This is where `Hugo hotplate` steps in, providing minimal configuration and a standard way to handle such assets. It melds great with Hugo's already simplified directory structure. 

### What's inside?

Currently, `Hugo Hotplate` uses:

- Gulp 
- Stylus
- Babel

## Directory Structure

There's not much different to this project when compared with a normal Hugo project. What is different is the inclusion of a `src` directory, where all `js` and `css` is written. These files are built and fed into the `static` directory using `Gulp`, making them available in development and production.

### Layouts

The `layouts` directory is one of, if not _the_ most important directory in any Hugo project. It contains templates that are used to process specific pieces of content. There are many strategies to consider when developing a template hierarchy, and Hugo has a certain way of deciding which template to use to render content. More info [here](https://gohugo.io/templates/lookup-order/). 

`Hugo hotplate` utilizes the `baseof.html` file located in `layouts/_default/` that **all** pages (with 1 exception) use to render content. The only exception to the last statement is the homepage, which uses `layouts/index.html`, and it is the only page to do so.

Inside of the `layouts` directory you will also find two subdirectories named `posts` and `work`. These directories contain 2 template files, `list.html` and `single.html`. The `list.html` file will render a list of items contained inside of `content/posts/` and `content/work/` respectively, while the `single.html` file will render individual content items. For more information on content management in Hugo, check out their [docs](https://gohugo.io/content-management/organization/).

### Content

The `content/` directory contains subdirectories of `markdown` files that represent the content of your site. These files are fed into specific templates in the `layouts/` directory before being rendered in the browser. This directory is pretty straight forward and follows the standard Hugo approach. No _magic_ here. 

Currently, their are 2 subdirectories: `posts` and `work`. These are just default directories and can be changed at any time. Note that changing these directories means you must change their respective template directories `layouts/posts/` and `layouts/work` if you expect to use the templates inside of them. Hugo will not be able to resolve the template if you don't, and will end up using the `list.html` and `single.html` templates inside of `layouts/_default`, which is currently empty when cloning this project. 

As mentioned before, this is just a jumpstart. Feel free to change the way you render content to your heart's content ❤️.

The last thing to note about the `content/` directory is the `_index.md` file in the root. This is the markdown file for the homepage of the site. It uses `layouts/index.html` as its template.

### Archetypes

Besides the `default` archetype that comes with every newly initialized Hugo project, there is also a `posts` archetype that can be used when creating a blog post, if that's your sort of thing. Just type:

```
hugo new posts/<post-title-here.md>
```

And a new `.md` file will be created inside of `content/posts/` with the defined front matter inside of `archetypes/posts/posts.md`. Feel free to add to this if you please. 

### Styles

Styles are written using the pre-processor [Stylus](http://stylus-lang.com/).

All Stylus source files are located inside `src/styles`. The directory structure is as follows:

```
src/
    styles/
      pages/
        home.styl
        posts.styl
        post.styl
        work.styl
        ...
      partials/
        _reset.styl
        ...
      vendor/
        typebase.styl
        ...
      main.styl
```

The methodology for css in `Hugo hotplate` is like this:

1. `main.styl` includes common css rules that exist on all pages throughout the site.

2. Partial files live inside `styles/base/`, and can be imported into any other files using `@import`. Partial files are a good way to keep related styles together, and to keep styles maintainable. 

3. The `styles/pages/` directory contains files for individual static pages, such as the homepage, the posts page, or a single post page

4. Each static page will have **1** css file. They will consist of:

- an imported `main.styl`
- partials relevant to the page that haven't been imported into `main.styl`.
- unique styles relevant to the page

#### Developing with this methodology

Simple really. Gulp watches for changes in the `styles` directory, then takes the `.styl` files inside of `styles/pages/` and outputs them to `static/css`. Only the actual page files are being moved to the static directory. They will contain the necessary styles for each page. Once the styles are here they can be used on the site.

#### Example

```
/* home.styl */

@import '../main'

.container 
  background: red



/* work.styl */

@import '../main'

.container
  background: blue
```

Both `home.styl` and `work.styl` will contain the common css rules necessary for each page by importing `main.styl`, but they also have the ability to style elements unique to the page itslef. In this case, the `.container` element will have a red background on the homepage, but a blue background on the work page. This keeps things as DRY as possible while preventing styles from loading on a page that doesn't require them. 

The only tricky part that still needs to be hashed out to improve the dev experience is this:

In order to have Gulp file watching and Hugo server running, `Hugo hotplate` needs 2 separate terminals running both of these commands:

```
npm run dev 

npm run gulp
```

The former is just an alias for `hugo server -D`, while the latter runs gulp and initializes file watching. A better solution is necessary for the future of this project.

#### Declaring a CSS file for each page

Hugo's [front matter](https://gohugo.io/content-management/front-matter/) feature gives us the flexibility to declare which stylesheets should load for any one content page. A special parameter, `stylesheet`, should be included in a page's content front matter, with a value equal to the stylesheet required. 

```
/* content/_index.md */

+++
stylesheet = "home.css"
+++


/* content/posts/some-fancy-post.md */

+++
stylesheet = "post.css"
+++
```

This parameter is then accessible to templates in the `layouts/` directory, namely the base layout `layouts/_default/baseof.html`. It can be accessed using the `.Params` variable:

```html
<!-- layouts/_default/baseof.html -->

<link rel="stylesheet" href="/css/{{ printf .Params.stylesheet }}" />
```

This will append the stylesheet name to the `href` attribute of the stylesheet link in the page's head. Pretty neat, huh?

### JavaScript

Not much to say here. Gulp watches for changes in `src/js/`, runs said file through `babel` (yay for `babel-present-env`), and outputs the file into `static/js`. There's a `.babelrc` file at the root of the project declaring the use of `babel-preset-env`, which gives you some helpful JS dev'ing goodies. More info [here](https://github.com/babel/babel-preset-env).

## Contributing

Feel free to create a PR if you feel something can be added to the boilerplate to make it more useful. I will continue to be adding to this project as time goes on. I really enjoy Hugo and think its a great tool for projects that don't require dynamic functionality.

