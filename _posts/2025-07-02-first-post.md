---
layout: post
title: First Post! And Creating This Website
categories: [Miscellaneous, Code]
---
Hello World!!...       Just kidding.

Jokes aside, welcome to my first-ever website! In this quick article, I want to reflect on my experience creating a website for the first time as someone with amateurish experience in web development. I also want to get into the groove of writing these half-reflective half-educational articles, and I think documenting a simple project like this is a great place to start.

## Research and Development

For context, I've created this website as a living space to document my various projects-- sort of like a blog-portfolio. With this consideration in mind, I began looking at my options.

### Option 1: Website Builders & Content Management Systems (CMS)

<img src="/images/wordpress.png" alt="The Wordpress logo.">

Examples:

- [Wordpress](https://wordpress.com/)
- [Squarespace](https://www.squarespace.com/)
- [Webflow](https://webflow.com/)

While I'm sure a CMS could easily help me achieve my goals, I found their drag & drop style of website building to be a bit boring. I wanted this to be a project where I lightly brush up on valuable skills, so website builders were not the right fit for me.


### Option 2: Frameworks

Examples:

- [React](https://react.dev/)
- [Rails](https://rubyonrails.org/)
- [Angular](https://angular.dev/)

While I'm sure learning to use a Webdev framework would be quite enriching and potentially very useful, I believe they go far beyond my needs for this project. So I kept looking.

### Option 3: Static Site Generators

<img src="/images/jekyll.png" alt="The Jekyll logo.">

Examples:

- [Jekyll](https://jekyllrb.com/)
- [Hugo](https://gohugo.io/)

In searching for the right tool, I found static site generators to be most promising. A balance between the convenience of a CMS and the involvement of learning a framework, they fit my vision perfectly.

Static site generators allow me to organize content files, layouts, and assets that it then compiles into the individual static HTML, CSS, and Javascript files of each webpage. I can then take these files to a hosting service and voilà! My website is born.


## The Meat and Potatoes

I decided to use Jekyll as my static site generator and Github Pages as my host because they are both free and Jekyll has a large collection of free templates I could use. After setting up my Github acccount and installing Jekyll (through Ruby), I was ready to work.

As mentioned earlier, online communities develop and host Jekyll themes as Github repositories. This allows me to fork the repository of a theme I like, then edit the content/layout files to create the website I want. This particular website is a version of [Reverie](https://github.com/amitmerchant1990/reverie), a simple blog-oriented theme. After cloning my repository to my PC, I quickly familiarized myself with the development environment in Notepad++ and adjusted the config file as needed.

<img src="/images/workflow.png" alt="A screenshot of my Notepad++ workspace">

Since static site pages are typically just text files with some basic formatting, they utilize Markdown files (.md) which allow users to denote simple text elements (bullet points, headings, etc.) and a layout that Jekyll can interpret. This means that I can create a new post simply by creating a new Markdown file in the "_posts" directory. Jekyll processes these Markdown files using layout files to apply the intended structure and design for each page. For example, this page utilizes the "post" layout file, indicated by the heading at the top:

```markdown
---
layout: post
title: First Post! And Creating This Website
categories: [Miscellaneous, Code]
---
```

Meanwhile, the "post" layout file contains all the information needed to format each post in HTML.

Since Jekyll runs entirely from local files, I can use it to compile my site in a local environment. While this local server is running, Jekyll watches for file changes and automatically updates the site in my browser — letting me preview edits live as I make them.