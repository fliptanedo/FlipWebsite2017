# FlipWebsite2017
My Personal Website based on the Hugo Academic theme

As of 3 September 2017, the site is hosted at [http://physics.ucr.edu/~flip/](http://physics.ucr.edu/~flip/).

**Note:** I am not sure how dilligent I will be with keeping this repository updated as I update my content. I also have not cleaned this up into a modular variant of Cushen's Academic theme. Eventually this may be a nice side project to propose features for Cushen's theme. However, I may not have time to get around to this in the near future.

Please refer to George Cushen's [Hugo Academic theme](https://github.com/gcushen/hugo-academic), which is what this site is heavily based upon.

# Flip's 2017 Website Redesign
25 August 2017; rewritten 2 September

**Note**: these are internal notes to myself as I familiarize myself with Hugo. Your mileage my vary. *If, however, you find it useful*, I would be happy to hear from you at flip.tanedo@ucr.edu.


I am redesigning my website using [Hugo](https://gohugo.io/getting-started/quick-start/), a **static website generator**. The reasons for this are:

1. Content is written as pure markdown making updates easy. One may even automate updates with a script.
2. Removes redundant coding. For example: menu bars are uniformly updated across all pages.
4. The [Hugo Academic Theme](https://themes.gohugo.io/academic/) is a nice fit.
3. This type of page can be scaled up to a group or department. [Here's an example](https://biaslab.github.io) with [source](https://github.com/biaslab/hugo-academic-group).

Here are my rough instructions starting from The [Hugo Academic Theme](https://themes.gohugo.io/academic/). I assume that you have set up Hugo using their quick start guide. Read the Hugo quick start guide and content organization philosophy. The file structure within a project translates into pages and assumed paths for templates, so you'll have to do this carefully. Now that I have my site set up, it's simple enough (for me at least) to see how this works by reverse-engineering.


## Create Site

~~~~
hugo new site www2017
cd www2017
git clone https://github.com/gcushen/hugo-academic.git themes/academic
cp -av themes/academic/exampleSite/* .
hugo server --watch
~~~~

This creates the new site, applies the Academic theme, and copies the theme's example template. It then launches the Hugo server so that the site is updated in real time and is viewable at [http://localhost:1313/](http://localhost:1313/). This address is updated when you change the `baseurl` variable in `config.toml`, so just grab the link from the **command line interface** (CLI).


### Edit data

Now edit `config.toml` to match the data that I'd like to share. A few things I went ahead and changed:

~~~~
title = "Flip Tanedo @ UC Riverside"
copyright = "&copy; 2017 Flip Tanedo"
~~~~

I placed a profile picture in `./static/img/Profile/`.

~~~~
[params]
  name = "Flip Tanedo"
  role = "Assistant Professor of Physics & Astronomy"
  organization = "UC Riverside Particle Theory"
  organization_url = "http://theory.ucr.edu"
  gravatar = false
  avatar = "./Profile/Flip600_sq.jpg"  # (in `static/img/` folder)
  email = "flip.tanedo@ucr.edu"
  address = "Physics Building, Room 3054, University of California, 900 University Ave., Riverside, CA 92521, USA"
  office_hours = "Monday 1:30 to 2:30pm"
  skype = "flip.tanedo"
  telegram = ""
~~~~

## Updating the Hugo Academic theme

See [instructions on GitHub](https://github.com/gcushen/hugo-academic). It's pretty painless, but don't forget to
1. Backup everything!
2. Check if you made your own copies of theme files (*not* overwriting the `/theme/` folder, of course!) that need to be changed accordingly.


## The Hugo philosophy

At this point you have an idea for how to modify the template file. At this point, it's useful to review the philosophy for using a static site generator like Hugo, and Hugo's particular implementation. For now, let's just start with a single page site (it secretly has other pages) like the Academic theme default.

* Your content is stored in markdown (`md`) files in subdirectories of `\content`. The directory structure mirrors your site structure. If you have a folder `\content\classes`, then you have just created a new page.

* Content pages are separated into a **header** and into **content**. The header is enclosed between a pair of `+++` lines. Here you can define parameters for that content. These include things like the `title`, but also parameter lists that can be cycled through and user-defined parameters that can be called.

* The `\layouts` directory contains `html` files that format your content. These are typically `html` snippets that can be called by other templates. The directory structure of `\layout` is also significant: subdirectories are **types** and the `html` contents of those subdirectories are **layouts**.  These, in turn, can be specified in the **header** of a content file to specify how that content should be formatted.

* The layout files are `html` snippets with Go script. You can recognize the Go script because it shows up with double curly braces: `{{ GoScriptContent }}`. The scripts access data in the content file and the `config.toml` file and return code that is directly inserted into the layout file in place of the `{{ ... }}`.

* So now you see that the connection between content and layout boils down to linking the right layout file to each content file. Hugo is designed to be modular: you can apply themes like skins, or tweak these themes in the spirit of cascading style sheets. One of the first places Hugo looks for layouts is the `\themes\<theme name>\layouts` directory. However, Hugo has a specific **look up order** (search the Hugo documentation) to look for layouts. In particular, anything in the `\layouts` folder will be given priority over the analogous file in the `\themes\<theme name>\layouts` folder.

* All this is to say: when you want to tweak a theme, *do not edit the `themes` directory by hand*! Just copy the relevant file from the `\themes\<theme name>` directory (or create a new file if it doesn't exist) into `\layouts` and modify it there. It doesn't matter that an unmodified copy lives in the `\themes\<theme name>` folder---Hugo will use the first file on its prioritized list. Thus you never have to modify the theme directly! (Conversely: if you *update* the theme, you never have to worry about losing your modifications.)

* If a content file does not have a parameter that the layout file requires, then it is assumed empty, so be sure to check your parameter names if content is missing.

* To publish: close the Hugo local server, and run `hugo` at the command line. (No `server` option.) This will publish the site to the `\public` subdirectory. Copy this to your server.


## About Me + CV Sections instead of Biography

The current  `./content/home/about.md` widget contains an introductory paragraph *and* a brief academic history. I think the academic history fits better on a separate mini-CV widtet. So let's make new `aboutme.md` and `cv.md` sections. The Academic theme has a nice [guide for modifying new widgets](https://themes.gohugo.io/theme/academic/post/widgets/).

1. Copy the `./themes/academic/layouts/partials/widgets/custom.html` file to `./layouts/partials/widgets/CV.html`. This currently set up to be a section about teaching.

2. Create a `./content/home/CV.md` that is a copy of `about.md`. We'll modify the main content (stuff below `+++`) later, but you can make sure that your `interests` and `education` are updated. Update the `widget = "CV"` so that you use the new CV widget we made. Update the `weight` to something like `1.5` to make sure it shows up right after the `about` section.

3. Copy the following html/go snippet from `./themes/academic/layouts/partials/widgets/about.html` into `./layouts/partials/widgets/CV.html`. This will provide the "interests" and "education" panels.

~~~~
<div class="row">

      {{ with $page.Params.interests }}
      <div class="col-sm-5">
        <h3>{{ i18n "interests" | markdownify }}</h3>
        <ul class="ul-interests">
          {{ range .interests }}
          <li>{{ . }}</li>
          {{ end }}
        </ul>
      </div>
      {{ end }}

      {{ with $page.Params.education }}
      <div class="col-sm-7">
        <h3>{{ i18n "education" | markdownify }}</h3>
        <ul class="ul-edu fa-ul">
          {{ range .courses }}
          <li>
            <i class="fa-li fa fa-graduation-cap"></i>
            <div class="description">
              <p class="course">{{ .course }}{{ with .year }}, {{ . }}{{ end }}</p>
              <p class="institution">{{ .institution }}</p>
            </div>
          </li>
          {{ end }}
        </ul>
      </div>
      {{ end }}

    </div>
~~~~

Is that clear? Now we do the same thing, and make an `aboutme.html` template that does not have the extraneous information. Just comment out the piece of code that we copied into `CV.html`.

Go ahead and delete `about.md`. Don't worry, it's just a template that lives inside `./themes/academic/exampleSite...`. Then we'll go ahead and delete the rest of the unwanted stuff on the front page.


## Fixing the menu

Go to `config.toml` to make sure the menu items that I want are organized properly. You can also add links to external pages.

## Creating a a new page with subpages

I'd like to have a repository of all of my course webpages. Here's how I do it:

1. Create a content subfolder: `\content\teaching` and create markdown files `_index.md`, `ClassName1.md`, `ClassName2.md`.

2. The existence of the subfolder creates a subfolder in your website with its own `index.html`. The file `_index.md` is an optional place to put in introductory content. Something like "Here are some courses I've taught." Then it will include a list to the separate `ClassName1.html` pages. In this way, the subfolder's `index.html` page is **automatically** a list to the folder's contents.

3. The `index.html` page and the subpages are formatted according to templates. The layout of this page will default to `layouts\_default\list.html`. If you want to modify it, you can create a layout file in `layouts\sections\teaching.html`. Note that the name of the `html` file needs to match the name of the subfolder of the `\content` folder.

4. To format the class webpages, you can create a layout at `layouts\teaching\class.html` and make sure that `ClassName1.md` contains in its header:
~~~~
type = "teaching"
layout = "class"
~~~~

## Adding CSS

We follow the Hugo Academic Theme instructions for changing the theme color using the *green theme*.

>You can link custom CSS assets (relative to your root static/css) from your `config.toml` using `custom_css = ["custom.css"]`.

>For example, lets make a green theme. First, define `custom_css = ["green.css"]` in `config.toml`. Then we can download the example green theme and save it as `static/css/green.css`, relative to your website root (i.e. not in the themes directory).

I went ahead and made a `static/css/flip.css` for modifications to the font. Here are some thoughts on the default Hugo Academic style file:

* This is where you change the font, but you have to be sure the corresponding html header contains links to any additional fonts. Thus we need to go ahead and modify the `header` partial. In fact, the Academic theme has an empty partial `head_custom.html` that can be copied to `/layouts/partials/head_custom.html` for any HTML that should be in the `<head>`.

So, how to add a new font (using Google Fonts):

1. In `/layouts/partials/head_custom.html`, insert the line:
~~~~
<link href="https://fonts.googleapis.com/css?family=Raleway" rel="stylesheet">
~~~~

2. In `flip.css`, insert:
~~~~
.comment{
  font-family: 'Raleway', sans-serif;
}
~~~~

Actually, I decided to make the main font Raleway. I just like the way that it looks.

Some miscellaneous CSS notes: `#` is an **id selector** for a specific unique element. `.` is a **class selector** used to multiple elements.

* In the `html` tag: `font-family: 'Merriweather', serif;` with `font-size: 16px;`. But for large screens:

~~~~
@media screen and (min-width: 58em) {
  html {
    font-size: 20px;
  }
}
~~~~




## Adding a watermark

I'm very proud of my upper-left watermark. Here's what I did to make it work. In `flip.css`:

~~~~
/* to make "white bg" transparent */
.home-section {
  background-color: transparent;
  z-index: 1;
}

#watermark{
	/*background-image:url('http://physics.ucr.edu/~flip/images/BG_Bundle2.jpg');*/
	background-size: 700px 350px;
	background-repeat:no-repeat;
	background-position:left top;
	margin: 0;
	padding: 0;
	position: absolute;
	top: 30px;
	left: 0px;
  pointer-events: none;
	/*z-index: 0;*/
	height: 100%;
	width: 100%;
	text-align: right;
	opacity: 0.1;
}
~~~~
I left my comments in there.

1. The hard-wired image URL is there because it's useful for testing. (You know that any errors are not because the link is broken.)

2. One problem with the method (in particular, the steps below) is that the transparent layer can prevent clicking on items below it. There's a `pointer-events: none;` is a simple way to avoid this, see [this reference](https://stackoverflow.com/questions/3538489/html-css-make-a-div-invisible-to-clicks).

Next go to `layouts\partials\header.html` (copy it from the theme folder) and append this line under `<body...>`:
~~~~
<div id="watermark" style="background-image:url('{{ $.Site.BaseURL }}/img/{{ .Site.Params.watermark }}');"></div>
~~~~
**What this does**: adds a background layer with background image according to the `config.toml` file. We've chosen that the parameter is an address relative to the `static/img` folder. That's not so important. What *is* important is that the file is relative to `{{ $.Site.BaseURL }}` rather than simply `.`. This is important because files in subfolders (e.g. `mySite/teaching/myCourse.html`) may get lost looking for something like `mySite/teaching/img/image.jpg` versus `mySite/img/image.jpg`.

Defining a `watermark` parameter in `config.toml`:
~~~~
watermark = "background/Bundle3.jpg" # relative to `static/img/` folder)
~~~~
Note that `Bundle3.jpg` is a file that is black on white, thus the opacity brings it down to a reasonable watermark. If you use something that's already faint, the 0.15 opacity will make it practically invisible. And now we're good.





## Including Images

I'd like to include images of my funding agencies on the front page. It's simple enough to include a single image:

~~~~
![This is an image](/img/boards.jpg)
~~~~

Where the path is relative to `./static/`. You can also put images in the `content` folder if you want to keep images close to their content files.

For multiple *responsive* images on a line, you'll need **shortcode** that does this. **Shortcode** is a way to insert html directly into the content files. Here's an [example from Yoshi Yamashita](http://yoshiharuyamashita.com/post/hugo-shortcode-to-show-multiple-images/).


## Notes on Academic Theme

* Grid based on **Bootstrap** See [docs](http://getbootstrap.com/2.3.2/index.html). The theme also inherits all of the useful responsive design elements.



## CV button

See [bootstrap button info](https://getbootstrap.com/docs/4.0/components/buttons/). Actually, here's what I put into my `CV.html` template:
~~~~
<a class="btn btn-primary btn-outline btn-xs" href="{{ $page.Params.cv_pdf }}">
      Download Complete CV
    </a>
~~~~
With the following in my `CV.md`:
~~~~
cv_pdf = "./files/Tanedo.pdf"
~~~~



## Favicon Update

The **favicon** is weird. One suggestion is to put a `favico.ico` file into the root directory. This is *not* recommended by the W3C and is not compatible for all browsers. There are also funny rules for mobile devices. Here's a recommendation from [Stack Overflow](https://stackoverflow.com/questions/25952907/favicon-ico-vs-link-rel-shortcut-icon). You can use [favicon-generator](http://www.favicon-generator.org) to take care of the `.ico` file.

A quick way to solve this:

1. place `favicon.ico` in the `content` directory
2. Replace the following files: `img/icon.png` and `img/apple=touch-icon.png` with the 32x32 and 180x180 pngs appropriately. They care called here in the `header` partial:

~~~~
<link rel="icon" type="image/png" href="{{ "/img/icon.png" | relURL }}">
<link rel="apple-touch-icon" type="image/png" href="{{ "/img/apple-touch-icon.png" | relURL }}">
~~~~

## Shortcodes for formatting

[Hugo documentation on Shortcodes](https://gohugo.io/templates/shortcode-templates/)

Place shortcode in `/layouts/shortcodes/<SHORTCODE>.html`. Here's one that I have for inserting a Twitter feed:
~~~~
<a class="twitter-timeline" data-height="300" href="https://twitter.com/{{ index .Params 0 }}">
  Tweets by FlipTanedo
</a> <script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
~~~~


## Footer bar

I have a pretty cool footer bar that is a gray gutter with a Feynman diagram. This is a little tricky to do because the spacing of everything changes according to the responsive design. In my css:

~~~~
footer {
  font-family: 'Raleway', sans-serif;
  background-color:#333333;
}
~~~~

This sets a bar at bottom of the page to be dark gray. (It also changes the font family. I included Raleway in the header using the standard Google Fonts include statement.)

~~~~
#botbar1{
	background-color:#C1BBAB;
	margin: 0;
	padding: 0;
  position:relative;
	top: 75px;
	left: 0px;
	z-index: 9;
	height: 7px;
	width: 100%;
}
/* Why is this exactly 927? I do not know! Trial-and-error. */
@media screen and (max-width: 927px) {
  #botbar1{
    top: 65px;
  }
}
~~~~
This is the horizontal sandstone-colored bar. The 927px width is determined empirically as the width at which the screen elements reshuffle. Note that we use `@media screen` to style responsively.

~~~~
#feynmanfoot{
	/*background-image:url('http://physics.ucr.edu/~flip/images/feynmanfooter.png');*/
	background-size: 250px 200px;
	background-repeat:no-repeat;
	background-position:left top;
	margin: 0;
	padding: 0;
	position: absolute;
  position: relative;
	bottom: 97px;
	/*left: 700px;*/
  left: 75vw;
  right:25vw;
	z-index: 200;
	height: 200px;
	width: 250px;
	text-align: right;
}
@media screen and (max-width: 980px) {
  #feynmanfoot{
    left: 68vw;
  }
}
@media screen and (max-width: 767px) {
  #feynmanfoot{
    left: 50vw;
  }
}
@media screen and (max-width: 480px) {
  #feynmanfoot{
    left: 10vw;
  }
}
~~~~

In `config.toml` include:
~~~~
footmark = "background/feynmanfooter.png"
~~~~

Ok. So far so good. But if you *over-scroll* (when you tug past the bottom of the page), then the dark gray bar reveals the white background. So we should have a dark gray background. Here's how I did that. In the css file:

~~~~
body{
  background-color:#333333;
}

#THECONTENT{
  background-color: #ffffff;
}
~~~~

`#THECONTENT` is a kludge to make a layer that is white. In `layouts/partials/navbar.html`, append to the end:
~~~~
<div id="THECONTENT"> <!-- closed in footer_container.html; see flip2017.css -->
~~~~
And at the end of `layouts/partials/footer_container.html`, append to the end:
~~~~
</div> <!--  ends id=#THECONTENT from navbar.html; see flip2017.css -->
~~~~
This is just after `</footer>`.


## Transparent, dark gray nav bar
Now wouldn't it be great if the nav bar matched the footer? In the css file:
~~~~
.navbar-default{
    background-color:rgba(0, 0, 0, 0.75);
}

.navbar-default .navbar-brand {
  color: white;
}

.navbar-default .navbar-nav li a{
    color: white;
}

.navbar-default .navbar-nav li a:hover{
    background-color: #333333;
}
~~~~

## List of students

In css:
~~~~
.ul-students{
  font-family: 'Raleway', sans-serif;
  color: #AAAAAA;
}

.ul-students:after {
  content: attr(title);
  display: block;
  text-align: center;
}

.ul-students ul {
  text-align: center;
  padding: 0;
}

.ul-students li {
  width: 25%;
  display: inline-block;
  vertical-align: bottom;
  font-size: .75rem;
}

.ul-students li img {
  max-width: 80%;
  height: auto;
}

/* STUDENT photos on index.html */
.studentpic{
  border-radius: 10px;
  text-align: center;
  margin: 0 auto;
  max-width: 100%;
}
~~~~

In `layouts/partials/widgets.research.html`:
~~~~
<!-- FLIP: STUDENTS -->
<h3>{{ $page.Params.student_title }}</h3>
    <div class="row">
          {{ with $page.Params.mygroup }}
            <ul class="ul-students">
              {{ range .students }}
              <li class="ul-students" title="{{ .name }}, {{ .position }}">
                <a href="{{ .website }}"><img class="studentpic" src="{{ .photo }}"></a>
              </li>
              {{ end }}
            </ul>
          {{ end }}
      </div>
      <!-- <p class="comment"> --><p>
      {{ $page.Params.recruit_blurb }}
      </p>
<!--  END: STUDENTS -->
~~~~

In `content/home/research.md`:
~~~~
# my Students
student_title = "Students"

[[mygroup.students]]
  name = "Ian Chaffey"
  position = "Grad"
  photo = "./img/identity/UCRHEP_03.png"
  website = "http://theory.ucr.edu/group.html"

[[mygroup.students]]
  name = "Adam Green"
  position = "UG"
  photo = "http://theory.ucr.edu/images/group/template_agreen.jpg"
  website = "http://sps.ucr.edu/mentor/adam-green"

[[mygroup.students]]
  name = "Syris Norelli"
  position = "UG"
  photo = "http://theory.ucr.edu/images/group/template_syris.jpg"
  website = "https://github.com/OrderFromChaos"
~~~~
