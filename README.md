# CSBridge Course Website - Turkey 21
This repository is the course website for the online CSBridge Turkey summer 2021 course.

## Adding Translation Changes
If you are helping with translation of the course website, here are the steps you should follow:

1. Make a fork of this repository
1. Install all necessary tools by running `pip3 install -r requirements.txt` and then after that running `python3 -m pip install -U watchdog[watchmedo]`
1. Edit any necessary files in the `templates/` folder according to what you are translating.
1. Run `./runLocal.sh`, which compiles the website into the `docs/` folder and lets you preview it in a web browser at `localhost:8000` so that you can see any changes.  You can leave that script running as you work; every time you make a change, the script will recompile the website so you can preview it again.
1. When you are done making changes, commit them (`git commit`) and push to GitHub (`git push`)
1. Create a pull request on the original repository with your changes to be reviewed - you can find information about how to do this [here](https://docs.github.com/en/github/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request).  You should also link this pull request to the GitHub issue you are working on so that we know what part is being updated.
1. An instructor will review and confirm the changes.

Thank you very much for your help!

## Adding Lecture Materials
If you want to post new lecture slides or code, here are the steps you should follow:

1. Clone this repository
1. Install all necessary tools by running `pip3 install -r requirements.txt` and then after that running `python3 -m pip install -U watchdog[watchmedo]`
1. Add your slides PDF to `docs/slides`
1. Add your sample code (if any) to `docs/starter/lectures`
1. Edit `templates/lectures.json` [as specified here](#updating-lecture-materials) to include information about your lecture materials.  The file already has entries for all lectures - you just need to update the fields in that entry as needed (e.g. when it becomes visible)
1. Run `./runLocal.sh`, which compiles the website into the `docs/` folder and lets you preview it in a web browser at `localhost:8000` so that you can see any changes.  You can leave that script running as you work; every time you make a change, the script will recompile the website so you can preview it again.
1. When you are done making changes, commit them (`git commit`) and push to GitHub (`git push`)

## Website Setup

+ Enable GitHub pages:
    1. Go to setting on the GitHub page for your repo.
    1. Change the 'Source' section of the GitHub pages to point to ```master branch/docs folder```.
	1. Check ```Enforce HTTPS.```
	1. Change the custom URL

This site uses GitHub pages.  Specifically, all files in the `docs` folder of the master branch are published.

Make sure to install all necessary tools before working on this website:

```
pip3 install -r requirements.txt
python3 -m pip install -U watchdog[watchmedo]
```

**Make sure `config/config.ini` is updated with the current course info.**  This file contains site information that is referenced throughout the website, so you only have to input it once (e.g. link to Ed discussion forum).

## Website Updates

1. Change any file templates in ```templates/```

1. ```python3 compile.py``` creates full html pages underneath the ```docs/``` folder.  Or use `./runLocal.sh` to compile and host a copy of the site at `localhost:8000`.

1. Any slides or starter code changed in the top directory are symlinked to change the actual files in ```docs/```.

1. After pushing to GitHub, it may take a few minutes for the changes to appear.

This website uses [bottle 0.12](https://bottlepy.org/docs/0.12/index.html) to render templates and split up code.  Within the `templates/` folder, anything ending in `.html` or `.mdown` will be rendered to the website with the same structure (e.g. if an HTML file `test.html` is within `templates/en/projects/` it will be rendered to the site and viewable at `[ROOTURL]/en/projects/test.html`.

Note: there is a special `{{pathToRoot}}` placeholder that you can use in all templates that will be replaced with the path to the root of the site.  This is useful for importing files from other locations, such as images.  For example:

```
<img src="{{pathToRoot}}img/projects/newspaper/newspaper.png">	
```

You can also access any variables within the `config/config.ini` file using the following syntax:

```
The course homepage is at {{config['course_url']}}
```

Replace `course_url` above with the name of the config variable you wish to reference.

### Local Development
Run `./runLocal.sh` to compile and run a local copy of the website at `localhost:8000`.  The script is long-running, monitoring any changes to the `templates` folder and automatically recompiling whenever a file changes so that you can see modifications you make while you are working.  It only recompiles files that have changed since the last compile.  Enter `Ctl-c` to terminate the script.

The website relies on several CSS classes (see [Timed Release](#timed-release)) to make certain content appear at certain times.  To help with testing, when running _locally only_ (e.g. viewing via `localhost:` or `file://`), the site will show a bar at the top that allows you to preview what the site looks like at another time of day.  When running hosted somewhere, this bar does not show.

### Templates Structure

+ `announcements`: helper files relating to showing timed announcements on the main page of the website
+ `en`: for English translations
+ `parts`: partials (partial HTML files included in other files, but not rendered on their own)
+ `programs`: helper files relating to the programs (projects students complete, and provided examples) rendered on the site.  This folder contains the `programs.json` file where all the data is stored about the examples used for students, and that file is ultimately used to display the table of projects on the main page, as well as the contents of the navigation bar for the projects and examples.
+ `teach`: the TA sub-section of the site with TA-specific resources
+ `tr`: for Turkish translations

### Markdown Support
You can write a file to be rendered in either HTML or Markdown.  If you write in HTML, that HTML file will be rendered via Bottle SimpleTemplate and posted on the site.  If you write in Markdown, by default the file will be converted to HTML and posted on the site.  

If you'd like, you can "rebase" the Markdown content just like you would with HTML content in Bottle.  Instead of adding a `rebase()` python call at the top of your template, however, include an _attribute_ at the top of the file to specify the template within which it should be rendered, such as:

```
template: templates/en/projects/projectTemplate.ptl
```

Attributes are key/value pairs you can include at the top of your file.  If you include attributes, you should leave a blank line between the last attribute line and the first line of your actual Markdown content.

If you include the `template` attribute, your Markdown will be converted to HTML and embedded within the template you specify as via a `rebase` call - in other words, it will not be escaped, and it will be supplied via the `base` property.  The template path should start from `templates/....`.

You can include other Markdown attributes as well, and these will also be sent to the template you specify and accessible in that template.  Finally, the full path to the markdown file being rendered will also be passed to the template via the `filePath` property.

For an example of a Markdown-written page, check out `templates/en/projects/piglet.mdown`, which is a Markdown file representing a problem.  It uses the template at `templates/en/projects/projectTemplate.ptl` and is rendered at `en/projects/piglet.html`.  That template supports the following attributes:

+ `title` - the title of the problem
+ `credit` (optional) - name of the author(s) of this problem.  If included, in the output it is rendered under the title
+ `solution` (optional) - relative path to the solution file for this problem.  If included, in the output it is rendered in a hidden code area at the bottom that the student can view.

### Updating Projects or Examples
The main project table on the course homepage, the project table on the SL page, and the content of the dropdowns in the navbar for Projects and Examples, are all **generated automatically** based on the contents of the `templates/programs/programs.json` file.  In other words, you should not modify the HTML file that represents the navbar - it is reading from this JSON file to know what to display.  This makes translation and updating easier because all translations and all program information is in one file for the entire site.  This file contains an array of JSON objects, one per day that should be displayed.  Each day's information contains sections (such as "Morning" or "Evening"), each with what projects should be listed for students to complete, and what worked examples should be listed.  See the file itself for an example of its structure.  Below, we specify in more detail the general format the file is expected to have.

The file itself should be an array of JSON objects, in order of days (e.g. day 1, then day 2, etc.).  Here are the fields the object can contain:

+ `title_en` (string): the day's topic (English)
+ `title_tr` (string): the day's topic (Turkish)
+ `visible_after` (string): optional timestamp when the day should become visible
+ `bonus` (boolean): optional, set to false if you do not want to show a banner linking to the bonus page for more projects to work on
+ `sections` (array): a list of section objects for the day, such as one for morning, one for evening

Each element of `sections` is an object specifying the content that should be displayed for that section of the day.  The sections should be in the order that they should be displayed on the website.  Here are the fields a section object can contain:

+ `title_en` (string): the section name (English) - e.g. "Morning"
+ `title_tr` (string): the section name (Turkish)
+ `visible_after` (string): optional timestamp when this content should become visible.  If this is not included, this section will become visible when the entire day becomes visible.
+ `projectURL` (string): starter code URL.  Prepended with domain.   In other words, if the site is `online.csbridge.org`, the starter code would be assumed to have the URL `online.csbridge.org/[URL]`.
+ `examples` (array): optional info about worked examples
+ `projects` (array): optional info about projects

"examples" is a list of worked examples that should be displayed for that day in the navigation bar.  Here is the format each worked example should have (all fields required):

+ `title_en` (string): the worked example title (English)
+ `title_tr` (string): the worked example title (Turkish)
+ `url` (string): the worked example URL.  Prepended with domain and language.  In other words, if the site is `online.csbridge.org`, the english version of this worked example would be assumed to have the URL `online.csbridge.org/en/[URL]`.

"projects" is a list of projects that should be displayed for that day in the main project table and in the navigation bar.  Here is the format each project should have (all fields required):

+ `imageURL` (string): the URL of the image to use in the project table for this problem.  Prepended with domain.  In other words, if the site is `online.csbridge.org`, the image would be assumed to have the URL `online.csbridge.org/[URL]`.
+ `title_en` (string): the project title (English)
+ `title_tr` (string): the project title (Turkish)
+ `topic_en` (string): the project topic (English) - e.g. "While Loops"
+ `topic_tr` (string): the project topic (Turkish)
+ `type` (string): one of "quickstart", "project" or "section" - determines the badge displayed for this project in the project table
+ `url` (string): the project URL.  Prepended with domain and language.  In other words, if the site is `online.csbridge.org`, the english version of this project would be assumed to have the URL `online.csbridge.org/en/[URL]`.
+ `teachURL` (string): an optional URL to the SL guide for this project.  Same format as `url`.

You'll notice that many of these objects have fields ending in `_en` and `_tr`.  These correspond to the language codes used by the site - currently, we use "en" for English and "tr" for Turkish.  Therefore, it is expected that these objects have a version of some of the fields above for each supported language.  If you wanted to add support for other languages, you should add additional fields for those languages alongside the English versions.

### Updating Bonus Projects
The bonus project table on the bonus page is **generated automatically** based on the contents of the `templates/programs/bonusPrograms.json` file.  In other words, you should not modify the HTML file that represents the bonus table - it is reading from this JSON file to know what to display.  This makes translation and updating easier because all translations are in one file.  This file contains an array of JSON objects, one per bonus topic that should be displayed.  Each topic's information contains what projects should be listed for students for that topic, as well as when the problems should become visible to students.  See the file itself for an example of its structure.  Below, we specify in more detail the general format the file is expected to have.

The file itself should be an array of JSON objects, in order of topics (e.g. topic 1, then topic 2, etc.).  Here are the fields the object can contain:

+ `title_en` (string): the topic (English)
+ `title_tr` (string): the topic (Turkish)
+ `visible_after` (string): optional timestamp when the topic should become visible
+ `projects` (array): info about bonus projects
+ `projectURL` (string): starter code URL.  Prepended with domain.   In other words, if the site is `online.csbridge.org`, the starter code would be assumed to have the URL `online.csbridge.org/[URL]`.

"projects" is a list of bonus projects that should be displayed for that topic.  Here is the format each project should have (all fields required):

+ `imageURL` (string): the URL of the image to use in the table for this problem.  Prepended with domain.  In other words, if the site is `online.csbridge.org`, the image would be assumed to have the URL `online.csbridge.org/[URL]`.
+ `title_en` (string): the project title (English)
+ `title_tr` (string): the project title (Turkish)
+ `difficulty` (integer): a difficulty rating, 1 (easy), 2 (medium) or 3 (hard)
+ `url` (string): the project URL.  Prepended with domain and language.  In other words, if the site is `online.csbridge.org`, the english version of this project would be assumed to have the URL `online.csbridge.org/en/[URL]`.
+ `teachURL` (string): an optional URL to the SL guide for this project.  Same format as `url`.

You'll notice that there is a field `titleen` and `title_tr`.  These correspond to the language codes used by the site - currently, we use "en" for English and "tr" for Turkish.  Therefore, it is expected that these objects have a version of the title field for each supported language.  If you wanted to add support for other languages, you should add additional fields for those languages alongside the Turkish and English versions.

### Updating Lecture Materials
The lectures dropdown in the navigation bar is **generated automatically** based on the contents of the `templates/lectures.json` file.  In other words, you should not modify the HTML file that represents the navbar dropdown for lecture materials - it is reading from this JSON file to know what to display.  This makes translation easier because all translations are in one file.  This file contains an array of JSON objects, one per lecture that should be displayed.  Each lecture's information contains the lecture title (including translations) and links to its slides and code (if applicable).  See the file itself for an example of its structure.  Below, we specify in more detail the general format the file is expected to have.

The file itself should be an array of JSON objects, in order of lectures (e.g. lecture 1, then lecture 2, etc.).  Here are the fields the object can contain:

+ `title_en` (string): the lecture title (English)
+ `title_tr` (string): the lecture title (Turkish)
+ `slidesURL` (string): the URL for the slides.  Prepended with domain.  In other words, if the site is `online.csbridge.org`, the slides would be assumed to have the URL `online.csbridge.org/[URL]`.
+ `codeURL` (string): the URL for the code.  Prepended with domain.  In other words, if the site is `online.csbridge.org`, the code would be assumed to have the URL `online.csbridge.org/[URL]`.  This is optional.
+ `visible_after` (string): optional timestamp when this lecture content should become visible.


## Timed Release
This website has the ability to make materials visible at specific dates/times without having to push a new version to GitHub.  To make an HTML element visible after a certain date/time, use the "visible-after" class, and add the timestamp as the value for the "data-visible-after" attribute.  For instance, to hide a div until July 7, 2019 at 5PM PST, you would add:

```
<div class="visible-after" data-visible-after="2019070717">
...
</div>
```

"visible-after" supports timestamps in the format `YYYYMMDDHH`.

You can also make an HTML element visible during a specific range of times.  To do this, use the "visible-during" class, and add the timestamps as data attributes.  For instance, to make a div visible between 7/9/19 5:12pm - 5:13pm, you would add:

```
<div class="visible-during" data-visible-start="20190709 17:12" data-visible-end="20190709 17:13">
	...
</div>
```

"visible-during" supports timestamps in the format "YYYYMMDD HH:mm".

Elements with the class "visible-default" are only visible if no other elements
on the page have class "visible-during".  Useful if you want to have certain elements
appear at certain times, but when no elements are visible, show some default element.

The timestamps are **all assumed to be timestamps in PST**.  The script that handles this hide/show functionality is in `docs/js/time.js`.

## SL/Staff Resources
Staff resources can be found at the `/teach` URL.  In particular, it includes a special version of the navbar and programs table showing all projects and solutions.  It does this by passing an additional parameter to the template that says it is the TA page.

## Translation
If you replicate the `en/` folder structure for other language codes (e.g. `tr`), you can support easily toggling between different languages.  To do this, also uncomment and modify the toggling code in `templates/parts/navBar.ptl`.

## Break Activity Ideas
Break times in the afternoon are a great time to get students out of lab and do fun activities.  Here are some ideas for activities that have been successful in the past, both indoors and outdoors.
- [Where The Wind Blows](https://youthgroupgames.com.au/games/111/where-the-wind-blows/) (using shoes as place markers)
- Code trace scavenger hunt: clues are programs students have to trace, and they print out the name of the next destination.  This was previously done on the 3rd day of the class.  See the CTU repo for example Scavenger Hunt files.
- [Ninja](https://en.wikipedia.org/wiki/Ninja_(playground_game))
- [Rock-paper-scissors](https://en.wikipedia.org/wiki/Rock–paper–scissors) tournament: each lab has students pair up and play a best-of-3 rock-paper-scissors.  The winner finds someone else to challenge, while the loser becomes a cheerleader for the winner and follows them to their next match.  Eventually, there should be just 1 winner per lab, with everyone else in that lab cheering them on.  This continues until their is just one student winner, and all students are cheerleaders for them.  This winner plays an instructor in a best-of-5 match.


## Todos
- [ ] Look into adding Google Timezone API access to front-page alert notifications telling students where they need to be. Needs one API key and Google Timezone API enabled, as well as reading JSON. We probably don't need timezones if we can just get the current Pacific Time. Current alert info is in `templates/en/index.html` and CSV lookup is in `templates/announcements/times.csv`, JavaScript is in `docs/js/time.js`. Using Google API avoids faulty local computer timezone issues.


## Ideas For Future Improvement

- More incoporation of code reviews and pair programming.  Maybe supplying checklists at the start and having students do a self check-in for every project.  Or working with another student to co-develop a program, or have multiple students each work on one part of a program and build on each others' code to see firsthand the value of good style.
- More discussion of style/top-down design
- Some way for teaching team to provide feedback (maybe once?) for some code they submit?
- 2 sections per day?
- theme sections based on TA area of interest?


