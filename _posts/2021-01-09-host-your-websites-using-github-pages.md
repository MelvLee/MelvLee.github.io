---
layout: post
tags: [Jekyll, GitHub Pages]
---

# Host your websites using GitHub Pages

If you have a GitHub account and you want to have a website, you can use [GitHub Pages][1] to host the website for you.

GitHub Pages use the files in a GitHub Pages enabled repository as the source for the website. Behind the scenes, GitHub Pages uses [Jekyll][2], a static site generator. Jekyll provides the following features:

- generate pages for your website from the markdown files in your GitHub repository. [Markdown][3] is like HTML a markup language that is easier to learn than HTML
- customize the look and feel of your website by creating your own Jekyll template or reuse one of the many [pre-defined themes][4]

By default, your GitHub Pages hosted website is made available at `https://<your-username>.github.io/<repository>`. However, if you give your repository the name `<your-username>.github.io` then the content of this repository will be made available at `https://<your-username>.github.io`

## Create a repository for a GitHub Pages website

To start using GitHub Pages, determine if you need to create a new repository or if you want to enable it for an existing repository. Take into account that if you have a free GitHub account, that GitHub Pages can only be enabled for public repositories.

## Enable GitHub Pages for a repository

To enable GitHub Pages for a public repository, click on the **Settings** tab to open the **Settings** page of the repository. In the **Settings** page, scroll down until you see the **GitHub Pages** section.

![GitHub Pages settings](/img/github-pages-settings.png)

The screenshot above shows the **GitHub Pages** section of an empty repository. A message is shown that GitHub Pages is currently disabled because the repository is empty. Instead of creating some content first, you can let GitHub Pages do that for you. Click on <kbd>Choosing a theme</kbd> to open the **Team chooser**. On the **Team chooser** page, select a theme and click on <kbd>Select theme</kbd>. The GitHub editor will now be shown with markdown content for the index.md file. For now, click <kbd>Commit changes</kbd> to create the index.md file and enable GitHub Pages for the repository. Go back to the **GitHub Pages** section on the **Settings** page. You will now find a link to your newly published website.

![GitHub Pages enabled settings](/img/github-pages-enabled-settings.png)

Click on the link to navigate to the generated website.

## Use a pre-defined theme for your website

You can use a pre-defined theme to personalize your website. A list of GitHub Pages supported themes can be found [here](https://pages.github.com/themes/). You can choose any of these themes using the **Team chooser** or you can assign the theme name to `theme` property in the **_config.yml** file.  
You can also use a [Jekyll theme hosted on GitHub](https://github.com/topics/jekyll-theme). Many of the GitHub hosted Jekyll themes can be used by replacing the `theme` property in the **_config.yml** file with the `remote_theme` property. The value of the property must be set to the name of the repository in the `<username>\<repositoryname>` format if you want to use the latest version of the theme or `<username>\<repositoryname>@<release>` if you want use a specific release of the theme.  
The table below contains examples of GitHub hosted Jekyll themes and their corresponding `remote_theme` property value.

| theme | remote_theme value | homepage layout |
|---|---| --- |
| [minima](https://github.com/jekyll/minima) | jekyll/minima@v2.5.1 | home |
| [Hydejack](https://github.com/hydecorp/hydejack) | hydecorp/hydejack@v9.0.5 | home |
| [Beautiful Jekyll](https://github.com/daattali/beautiful-jekyll) | daattali/beautiful-jekyll@5.0.0 | home |
| [Sleek](https://github.com/janczizikow/sleek) | janczizikow/sleek@v0.1-alpha | default |
| [Oscailte — A Jekyll theme](https://github.com/coogie/oscailte) | coogie/oscailte@3.0.3 | home |

## Configure your GitHub Pages website for blogging

Jekyll makes it easy to use your GitHub hosted website for blogging. All you have to do is create your post in the `_posts` folder and use the format `yyyy-MM-dd-title.md` for the name of a post file.

[1]: https://pages.github.com
[2]: https://jekyllrb.com/
[3]: https://docs.github.com/en/free-pro-team@latest/github/writing-on-github/basic-writing-and-formatting-syntax
[4]: https://jekyllrb.com/docs/themes/#pick-up-a-theme
[5]: https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages
[6]: https://github.com/jekyll
