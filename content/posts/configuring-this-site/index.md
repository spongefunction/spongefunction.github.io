---
title: "Configuring This Site"
date: 2023-08-16T03:21:37-04:00
draft: false
categories:
- site
- hands-on
tags: 
- hugo
summary: "What I did right and wrong in booting up this site with tons of theme docs."
---
Configuring this site took at least 3 days. No joking. Hugo is such a big project and it involves such a big community and so many choices. Just making decision between themes would take days to explore, and of course, start over.

But finally I settle for this `blowfish` theme.

Here I record some of the boot up steps and ~~useful~~ useless experience.

```
brew install hugo
hugo new site hugo-demo
hugo new posts/my-first-post.md
hugo server --debug
hugo serve
hugo
```

# Placing resouces for customization
Hugo use two folders in project root folder to store user's resources: `assets` and `static`.

`static` is only for those resources that does not need to be processed by hugo, like favicons. These files will be copied into `public` folder and referenced.

`assets` contains more complicated things, like custom css, fonts and images.
Whichever the resource file should be, when configuring in `hugo.toml`, paths name should start after assets or static. That means, you cannot know which folder to place by just looking at the sample config file. When rendering pages, which folder to check is decided by the hugo or theme program. What's under assets or static folder is what we can decide by modifying the config file.


# Modify the default markdown frontmatter
in `archetypes/default.md`, modify the content so that next time when creating posts with `hugo new` command, more items could be in the auto-generated markdown file.

It's intersting that the default hugo config file is in .toml while posts use yaml as front matter.

```yaml
---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: false
# slug: your-url-slug
categories:
- defaultCat01
- defaultCat02
tags:
- defaultTag
summary: "better to have some summary"
---

```

# Get the right size for featured pictures

Accroding to me inspection, the display size of featured image in list is 300 * 180 pixels. In card view the size is 310 * 200.

So next time prepare images in this size to avoid weird auto-sizing!

# Put the whole folder onto github repo
It's best to put `public` folder to the repository main branch, and put the root folder to the same repo `source` branch.

I have configured the public folder to be in main branch. Here is how I set the project root folder as source branch:
```bash
# initialize the local git repo
cd /en-spongefunction
git init

# add remote repo
git remote add origin https://github.com/spongefunction/spongefunction.github.io.git

# create and switch to source branch
git checkout -b source

# add all files to repo
git add .
git commit -m "Initial commit for source branch"

# push to remote source
git push -u origin source

# deal with the public folder submodule
git submodule add -b main https://github.com/spongefunction/spongefunction.github.io.git public
git commit -m "Add public as submodule"
git push origin source

```
I did not use submodule though it's used by a lot of blogs. I added public folder to `.gitignore` to make git work separately. It works most of the time, and breaks sometimes.