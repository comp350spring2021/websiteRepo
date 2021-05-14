# Hugo Tutorial
Here I will show you a little about how hugo works, and how you can use this repository in order to edit the Documentation for the submitter project. First Im going to talk a little bity about what Hugo is and how to use it then, I will go into more detail about how to edit the documentation website.

## What is Hugo?
Hugo is a static site generator. Often when we build websites we need little more than a simple static website, This is especially true when writing documentation. We want something that is effective and easy to use. However we still want something that looks good and is functional.

The main power of Hugo, and why it is so effective is its ability to take a markdown file and make it into a webpage. This allows virtually anyone to write an effective article, with little to no web development expertise. As software developers most of us are very familiar with markdown, allowing most of the devlopers working on the submitter project to submit effective documentation.

### Installing Hugo
 
 If you are on a Mac or a Linux machine then the easiest way to install Hugo is to use homebrew. Use the following command if you have home brew installed already:

 `brew install hugo`

 If you use Windows or dont use Homebrew then refer to the official Hugo documentation for an installation guide.

 [Here is official Hugo documentation for installation](https://gohugo.io/getting-started/installing/)

 ### Hugo Themes

 Hugo has a large number of themes that have been built by other developers. This is the quickest route to building an effective good looking static site. To view the themes click [here](https://themes.gohugo.io/). In this project we used the `GeekDoc` theme, which can be found [here](https://themes.gohugo.io/hugo-geekdoc/). Each theme may have it's own dependencies, and install requirements. The getting started guide for the GeekDoc theme can be found [here](https://geekdocs.de/usage/getting-started/).

 The the information lives in the `themes/` directory. This is where all of the the necessary files for GeekDoc theme are located. There is very little reason to enter or change anything in this directory unless you want to change the theme of the website.

 Each theme will have it's own options that can be toggled on or off. These can be found within the `config.toml` file. For example, if you look at the config file you will find the following line:

 ```
  geekdocSearch = true

 ```
 This line will turn on and off the search function within the website. Each theme will have its own unique options and you will need to look at the themes documentation in order to know how to use certain features. I will give a brief overview of how to create your own content with Hugo next.

 ## How you can edit and add Documentation:

 ### Cloning the repository:

 First we want to get this repository on your local machine, so go ahead and clone the repository using a git clone:

 `git clone https://github.com/comp350spring2021/websiteRepo.git`

 Once you have the repository on your machine and hugo installed properly we should be able to generate the website, to see how it looks.

 navigate into the websiteRepo folder and use the following Command:

 `hugo server -D`

 The -D flag forces pages that are marked as a "draft" to be generated too. There should be any in this directory currently but I will explain more on this later. If every thing has gone smoothly you should get something like this in the terminal:

 ```
                   | EN  
-------------------+-----
  Pages            | 31  
  Paginator pages  |  0  
  Non-page files   |  0  
  Static files     | 99  
  Processed images |  0  
  Aliases          |  0  
  Sitemaps         |  1  
  Cleaned          |  0  

Built in 111 ms
Watching for changes in /home/alex/docs/{archetypes,content,data,layouts,static,themes}
Watching for config changes in /home/alex/docs/config.toml
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
 ```

 Navigate to the local host listed above, and you should be able to see the website that Hugo has generated. I highly recommend that any time you make a chagne to the website, you first test your changes using the command above to make sure you didn't make any mistakes.

 Now lets learn how to edit the website pages.


 ### Hugo Content

 All of the content of your website, aside from pictures and static content lives inside the `content/` directory. This is where all of the markdown files that are the meat of our website is located. Hugo also uses the structure of this directory in order to build the navigation menu of the website.

 For example, if we have the following directory structure:
 ```
 content/
    BackEnd/
        _index.md
    FrontEnd/
        _index.md
    Tutorials/
        _index.md
 ```
  Then we will have a navigation bar with the following headings: "Back End", "Front End", "Tutorials". Keep in mind that Hugo uses "page bundles" so if those subdirectories are empty then the resulting navigation link will NOT show up on your website. The `_index.md` files make these directories page bundles, and that particular file will be the page that is shown when you click on the parent directories resulting menu option. For example, on the documentation website, if you press the "Front End" menu option the page that shows up will be the `_index.md` file within the `FrontEnd` directory.

  A note on naming the directories: Hugo automatically detects camel case as spaces. For example, naming a directory `ThisIsTheBackEnd` would result in the menu displaying: `This Is The Back End`.

  The directories can even be nested to make sub navigation bars.

  ### Adding pictures to your files

  Adding pictures to your website is fairly simple as well. If the picture comes from the internet you can use the typical markdown formatting like the following: `![<text>](<url>)`.

  However If, you want to display static photos you can do something similar. Use the regular markdown notation but follow the directory structure starting from the `static/` directory. For example, take the following directory structure:

  ```
  websiteRepo/
    static/
        infrastructure/
            somePicture.png
            
  ```
  To display `somePicture.png` in your markdown use the following format:

  `![<text>](infrastructure/somePicture.png)`

  Pictures can also be stored directly within the `static/` directory as well, however this would make your static folder incredibly disorganized and hard to handle.

  With Just the previous two steps you are on your way to editing the website. Hugo does however allow for more functionality than the basic markdown capabilities, in the form of short codes.

  ### Hugo Shortcodes

  There are many shortcode capabilitie built into Hugo that allow you to extend the functionality of simple markdown. For example, say you want to embed a youtube video on a page. Markdown has no functionality build in for this, instead you can use the following shortcode:

  Say the youtube video Lives at the following link: `https://www.youtube.com/watch?v=w7Ft2ymGmfc`

  To embed that video into your web page place the following shortcode within your markdown file
  
  `{{< youtube w7Ft2ymGmfc >}}`

  For more on shortcodes click [here](https://gohugo.io/content-management/shortcodes/)

  Some shortcodes will be implemented within the theme. For example, look at [this](https://comp350spring2021.github.io/Infrastructure/) page from our documentation website.

  The table of contents feature at the top of the page was generated by the following shortcode:

  `{{< toc >}}`

  Each theme will have some of its own shortcodes built in, to see more of what the GeekDocs theme is capable of click [here](https://geekdocs.de/). If you are ever browsing the webpage and see a feature that you would like to implement within your own markdown file, click the "Edit this page" button that is at the top of every page on the Documentation website. This will bring you to the markdown file for that page within this repository, look at the raw markdown file to view my implementation.

  ### Front-Matter

  Each page within Hugo can contain something called front matter. Depending on the theme chosen, there are options that enable certain features for that page. For example, on the documentation you will notice that the navigation bars are Collapsible. This was enabled in the front matter like so:

  To make the Back End navigation menu collapsible, the following is placed within the front matter for the that directories `_index.md`.

  Take the following directory structure:
  
  ```
  content/
    FrontEnd/
        _index.md

  ```

  The contents of the `_index.md` file:

  ```
  ---
    GeekDocsCollapsible: true
    weight: 4
  ---

   < some markdown content >
  ```

  The front matter is the section between the twof sets of three dashes. Weight controls the placement of the Front End button within the nav bar. The higher the weight, the lower the placement in the nav bar.

  With this guide, I think you are well on your way to effectively editing the documentation. Keep in mind that when ever you push a commit to the master branch of this Repo you are automatically updating the website via a GitHub action, so make sure any changes made to the website work and are what you meant to do.

  Good Luck! 
  If you find yourself not understanding something, then email me at alexander.sullivan582@myci.csuci.edu




