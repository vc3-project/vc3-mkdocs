# MK Docs Cheatsheet


## Configure Pages and Navigation

The page configuration may be added/edited in the `mkdocs.yml` file, which will
then appear on the nav-bar.

A simple pages configuration looks like this:

```no-highlight
pages:
- 'index.md'
- 'user-guide-1.md'
- 'glossary.md'
```

This example will build two pages at the top level nav-bar, and will have
their titles automatically inferred from their respective filenames. In order to
provide custom names for these pages, they may be added before their filenames:

```no-highlight
pages:
- Home: index.md
- User Guide: user-guide-1.md
```


## Multilevel Documentation

Subsections can be created by listing related pages together under a section
title. For example:

```no-highlight
pages:
- Home: 'index.md'
- User Guide:
    - 'VC3 User Guide': 'userguide/vc3-user-guide.md'
    - 'VC3 Developers Guide': 'userguide/vc3-dev-guide.md'
```

With the above configuration we have two top level sections: Home and User
Guide. Then under User Guide we have two sub pages, VC3 User Guide and
VC3 Developers Guide.


## Project layout

    mkdocs.yml      # The configuration file.
    docs/
        css/
                    # The custom css file to overwrite original MK Docs styling
        img/        # The folder for all relevant img files
        userguide/  # VC3 User Guide
        index.md    # The documentation homepage.
        glossary.md # The documentation glossary
        ...         # Other markdown pages, images and other files.


## Links
  Create simple links by wrapping square brackets around the link text and
  round brackets around the URL:

    [VC3](http://virtualclusters.org/)

  If you want to give your readers an extra about the link that they're about to follow you can set a link title:

    [VC3](http://virtualclusters.org/ "Virtual Clusters for Community Computation")

  Titles usually appear as a tooltip when you hover over the link, and help search engines work out what a page is about.

## Images

Save all relevant images within the `img` directory located at
`vc3-mkdocs/docs/img/`. If you want to add an image to any doc, you may simply
call the image file:

    ![image-name-here](../img/image-file-name.png)

## Notes

You may add notes or warnings to docs:

```no-highlight
!!! note
    This portion of VC3‘s documentation is a highlighted note

!!! warning
    This portion of VC3‘s documentation is a highlighted warning
```
...will produce:

!!! note
    This portion of VC3‘s documentation is a highlighted note

!!! warning
    This portion of VC3‘s documentation is a highlighted note


## Commands

You should be able to edit specific pages directly by clicking on the Edit
Github link on the top right of the page and submitting PRs. However, if you
decide to clone the entire documentation project, here are the basic commands
required for quickly building the static page.

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs help` - Print this help message.
