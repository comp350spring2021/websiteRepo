# WebsiteRepo
This is a git repository for a [Hugo](https://gohugo.io/) static site generator. Hugo makes building static websites easy and quick which is why it was chosen for the purpose of documentation. With hugo you can easily, pick a theme for your website, import some markdown files that will be the content of your website, and generate the static html,css and javascript files.

Using a GitHub action we can automatically deploy the site files, to a GitHub pages repository. More information on this below.

**To see the website that this Hugo project generates go here:** https://comp350spring2021.github.io/

For more information on Hugo and its documentation click [here](https://gohugo.io/documentation/).

I will give a brief overview of this repository and it's contents, for a detailed writeup of Hugo and how to make changes to the documentation website checkout the [Hugo Tutorial](HugoTutorial.md)

## Config File
The `config.toml` file is a configuration file for your Hugo project. In this file you can adjust settings to how you want your website to look and act. Also you will specify a theme for your website. To take a look at the themes click [here](https://themes.gohugo.io/). If you like the look of the website the way it is now, then you probably won't need to change this very much.

[This project uses the "Geekdoc" theme](https://themes.gohugo.io/hugo-geekdoc/)


## Content Directory
The content directory is where you put all of the markdown files that you want your website to display. Subdirectories become the names of the links on the left hand menu bar. For example, if you create a directory within the `content` directory called `FrontEnd` you will get a menu header. Inside that directory you can put articles that you want under that header. Each `.md` file will be it's own separate page on the resulting website.

## Static Directory
The static directory is where all of the static files are stored for your website. For this project the static folder only really holds pictures that need to be displayed around the website. To display a picture from your markdown file you can use the following line:   
`[text](<subdirectoryName>/<pictureName>)`  
Where the subdirectory anme is the name of a directory within your `static` directory.

## Building the Website

Checkout my [Hugo tutorial here](HugoTutorial.md) for an in depth view of how to use this repository.

In order to view how the website will look once generated, you can use the command `hugo server -D`. This will make the webserver availabe on the local host  on port 1313 by default. If you are satisfied with the way that your website looks, you can use command `hugo` to generate all of the HTML, CSS, and Javascript for your website, All of these files are placed within the `public/` directory.

The public directory, is not present within this repository because it was added to the `.gitignore` file.

Once the website files are generated they can be deployed to a website hosting service. We use Github pages, in order to host our website (linked above). Rather than manually pushing all of the  files in the `public/` directory, into the GitHub pages website everytime we want to update the website, we have utilized github actions to automatically deploy the website when there is a push into the master branch of this repository.

## Github Action

To learn how I got the GitHub Action to work click [here](GitHubActionDoc.md).

This is our github action:
```
name: hugo CI

on:
  push:
    branches: [ master ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true 
          fetch-depth: 1   

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'

      - name: Build
        run: hugo

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.ACTIONS_DEPLOY_KEY }}
          external_repository: comp350spring2021/comp350spring2021.github.io
          publish_branch: master
          publish_dir: ./public
```
Whenever a push is made into the master branch of this repository (websiteRepo), this action runs. Essentially what is happening is GitHub waits for a push, then spins up an ubuntu machine and creates the Hugo project. Then it pushes the contents of the public directory into our GitHub pages repo (Comp350spring2021.github.io).

More information about the actions used with this action at these links:

[actions/checkout@v2](https://github.com/actions/checkout)  
[peaceiris/actions-hugo@v2](https://github.com/peaceiris/actions-hugo)  
[peaceiris/actions-gh-pages@v3](https://github.com/peaceiris/actions-gh-pages)

[For documentation on how to create a GitHub pages site for yourself click here](https://pages.github.com/)


