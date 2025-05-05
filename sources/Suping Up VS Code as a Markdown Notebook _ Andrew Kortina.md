---
title: Suping Up VS Code as a Markdown Notebook — Andrew Kortina
---

# Suping Up VS Code as a Markdown Notebook — Andrew Kortina

url:: https://kortina.nyc/essays/suping-up-vs-code-as-a-markdown-notebook/#outline-view
up: [[sources]]

#source

For many years, I have been using Google Docs was the primary repository for all of my notes, drafts, and journals.

Over the past few months, I’ve (1) heard lots of great reviews of new note-taking apps and (2) I have personally been using [VS Code](https://code.visualstudio.com/) a lot more for writing code.

I recently evaluated all of the alternatives and landed on using a *suped up* VS Code as my primary notebook. This post details the how / why.

###### tldr; pros

* Vim keybindings with [VSCodeVim](https://github.com/VSCodeVim/Vim)
* Flat markdown file storage on disk in a non-proprietary format, backed up and synced across devices with [Google Backup and Sync](https://www.google.com/drive/download/), searchable with [ripgrep](https://github.com/BurntSushi/ripgrep), scriptable with bash, python, etc
* Extremely high level of customization possible via VS Code `settings.json`, `keybindings.json`, and [Extension API](https://code.visualstudio.com/api) (with TypeScript *not* Vimscript). I wrote an extension – [VS Code Markdown Notes](https://marketplace.visualstudio.com/items?itemName=kortina.vscode-markdown-notes) – to add some features I wanted
* VS Code is free, open source, and has an awesome community of users

###### tldr; cons

* No mobile app (see more on why I didn’t really care about this below)
* No inline image previews / rich formatting of markdown (but there is a live preview side pane)

###### Outline

* [Suping Up VS Code as a Markdown notebook](#suping-up-vs-code-as-a-markdown-notebook)
  * [VSCode Vim](#vscode-vim-extension)
  * [How I Organize Notes](#how-i-organize-notes)
  * [Outline View](#outline-view)
  * [Search Headings Across All files](#search-headings-across-all-files)
  * [Global Hotkey to Open Notebook with Alfred](#global-hotkey-to-open-notebook-with-alfred)
  * [Note Navigation with `cmd+p` and keyword search](#note-navigation-with-cmdp-and-keyword-search)
  * [Turn Off enablePreviewFromQuickOpen](#turn-off-enablepreviewfromquickopen)
  * [Note Navigation with `[[wiki-links]]` and Tags Using my VS Code Markdown Notes Extension](#note-navigation-with-wiki-links-and-tags-using-my-vs-code-markdown-notes-extension)
    * [Screenshots of the Features of My Extension](#screenshots-of-the-features-of-my-extension)
      * [Intellisense Completion for Wiki Links, `uniqueFilenames`](#intellisense-completion-for-wiki-links-uniquefilenames)
      * [Intellisense Completion for Wiki Links, `relativePaths`](#intellisense-completion-for-wiki-links-relativepaths)
      * [Syntax Highlighting for Tags and Wiki Links](#syntax-highlighting-for-tags-and-wiki-links)
      * [Peek and Go to Definition for Wiki Links](#peek-and-go-to-definition-for-wiki-links)
      * [`cmd+shift+f` to Search Workspace for Notes with #tag](#cmdshiftf-to-search-workspace-for-notes-with-tag)
  * [Command and Keyboard Shortcut to Quickly Create New Notes](#command-and-keyboard-shortcut-to-quickly-create-new-notes)
  * [Markdown All in One Extension](#markdown-all-in-one-extension)
  * [Code Spell Checker Extension](#code-spell-checker-extension)
  * [Image Preview Extension](#image-preview-extension)
  * [Customize Colors of Block Quotes, Punctuation, Code, etc.](#customize-colors-of-block-quotes-punctuation-code-etc)
  * [Manually Setting Modified Dates](#manually-setting-modified-dates)
  * [Notes on Mobile](#notes-on-mobile)
  * [Alternatives](#alternatives)
    * [Google Docs](#google-docs)
    * [FSNotes](#fsnotes)
    * [Bear](#bear)
    * [Roam Research](#roam-research)
    * [Notational Velocity, nvalt](#notational-velocity-nvalt)
    * [Other VS Code Extensions](#other-vs-code-extensions)

## Suping Up VS Code as a Markdown notebook

Before I do a comparison of some alternative note-taking apps and talk about how I was using Google Docs for years, I’ll talk through the details of how I customized VS Code.

### VSCode Vim Extension

One of the main features I wanted with my notebook was vim keybindings. The [VSCodeVim](https://github.com/VSCodeVim/Vim) Extension provides these.

### How I Organize Notes

I basically organize notes into 2 types of files (similar to what I did on Goolge Docs), (1) a journal file for each year, where I record snippets, quotes, and transient thoughts and (2) dedicated topic / post / draft / note files, when there is a particular theme I want to collect a deeper set of organized thoughts around.

All of my notes are in a single workspace at `$HOME/Google-Drive/_notebook` and I don’t really use subdirectories to group them.

In the workspace settings (`$HOME/Google-Drive/_notebook/.vscode/settings.json`) I have turned on autoSave:  

      "files.autoSave": "afterDelay",
      "files.autoSaveDelay": 1000
(1) Journals are in `journal-YYYY.md` files, one per year, with headings for each date I make an entry.

![vscode-journal-2020.png](https://kortina.s3.amazonaws.com/_/vscode-markdown-notes/vscode-journal-2020.png) I have a snippet defined in `settings.json` with VSCodeVim normalMode keybinding:  

    "vim.normalModeKeyBindingsNonRecursive": [
      {
        "before": [
          "<leader>",
          "d"
        ],
        "after": [],
        "commands": [
          {
            "command": "editor.action.insertSnippet",
            "args": {
              "snippet": "\n# $CURRENT_YEAR-$CURRENT_MONTH-$CURRENT_DATE\n\n"
            }
          }
        ]
      },
(2) Longer notes and topics are in notes named `topic-name.md` in the main workspace folder (which you can see in the above screenshot in the File Explorer in the left Panel).

In my workspace settings (`$HOME/Google-Drive/_notebook/.vscode/settings.json`) I set the following so the most recently edited notes sort to the top of the File Explorer in the left Panel of VS Code:  

      "explorer.sortOrder": "modified",

### Outline View

I also find it extremely helpful to have [Outline view](https://code.visualstudio.com/docs/languages/markdown#_outline-view) open in the File Explorer. This will show all the headings as a tree for easy navigation. By default, for some reason it is not sorted top to bottom, so I set the `Sort By: Position` setting:

![outline-view.png](https://kortina.s3.amazonaws.com/_/vscode-markdown-notes/outline-view.png)

### Search Headings Across All files

Another feature I accidentally discovered was the ability to search for headings across all notes with the Command Pallette (`cmd+p`) and starting a query with the `#` character:

![search-all-file-headings.png](https://kortina.s3.amazonaws.com/_/vscode-markdown-notes/search-all-file-headings.png)

### Global Hotkey to Open Notebook with Alfred

I want to be able to quickly open my notebook from any context, so I have a global hotkey (`cmd+shift+o`) bound to run the following bash command:  

    cd "$HOME/Google-Drive/_notebook"
    /usr/local/bin/code .
### Note Navigation with `cmd+p` and keyword search

I mentioned I keep all of my notes in the top level of my notebook workspace rather than organizing into subdirectories.

This is because the main way I open notes is through the `workbench.action.quickOpen` VS Code keybinding (`cmd+p`):

![cmd-p-zucman.gif](https://kortina.s3.amazonaws.com/_/vscode-markdown-notes/cmd-p-zucman.gif)

Or with the `workbench.view.search` (`cmd+shift+f`), which is VERY fast:

### Turn Off enablePreviewFromQuickOpen

To improve the functionality of the VSCodeVim jumplist (`ctrl+o`, `ctrl+i`) in `settings.json` I turned this off:  

    "workbench.editor.enablePreviewFromQuickOpen": false,

### Note Navigation with `[[wiki-links]]` and Tags Using my VS Code Markdown Notes Extension

A popular feature in [Roam Research](https://roamresearch.com/) and [Bear](https://bear.app/) is the ability to quickly reference other notes using “Cross-Note Links” in the `[[wiki-link]]` style.

I wrote an extension – [VS Code Markdown Notes](https://marketplace.visualstudio.com/items?itemName=kortina.vscode-markdown-notes) which adds support for navigation with `[[wiki-links]]` and `#tags`.

This gets to my point about the extensibility of VS Code – the Extension API is really powerful and because all my notes are in flat files, it was a matter of a evening coding sessions to roll support for this stuff from scratch. I was even able to take advantage of the fact that VS Code is an IDE with support for things like function/keyword definitions to add bits of ‘flair’ like Peeking linked notes inline.

#### Screenshots of the Features of My Extension

##### Intellisense Completion for Wiki Links, `uniqueFilenames`

![completion-unique-filenames](https://github.com/kortina/vscode-markdown-notes/raw/master/demo/completion-unique-filenames.gif)

##### Intellisense Completion for Wiki Links, `relativePaths`

![completion-relative-paths](https://github.com/kortina/vscode-markdown-notes/raw/master/demo/completion-relative-paths.gif)

##### Syntax Highlighting for Tags and Wiki Links

![syntax-highlighting](https://github.com/kortina/vscode-markdown-notes/raw/master/demo/syntax-highlighting.png)

##### Peek and Go to Definition for Wiki Links

![peek-and-to-to-definition](https://github.com/kortina/vscode-markdown-notes/raw/master/demo/peek-and-go-to-definition.gif)

##### `cmd+shift+f` to Search Workspace for Notes with #tag

![tag-search](https://github.com/kortina/vscode-markdown-notes/raw/master/demo/tag-search.gif)

NB: There was a similar [VS Code Wiki](https://marketplace.visualstudio.com/items?itemName=Yunseok.vs-code-wiki) that I tried out before writing this extension, but I didn’t love the way it still required the link destination in the `(parens)` after the link title and handle a few other details.

My next thought before building my own extension was I could just use the `gf` command from VSCodeVim to navigate to these files, which did work, but didn’t help with the autocompletion, syntax highlighting, previews, or tags.

### Command and Keyboard Shortcut to Quickly Create New Notes

One of the features that people rave about in apps like ([Notational Velocity](http://notational.net/), [nvalt](https://brettterpstra.com/projects/nvalt/), [Bear](https://bear.app/), [FSNotes](https://fsnot.es/)) is the “Search or Create” bar, which basically lands you into a search but allows you to quickly create a note if you don’t already have one.

This is sweet and I have not 100% replicated it yet.

I tried the [VSNotes](https://marketplace.visualstudio.com/items?itemName=patricklee.vsnotes) extension, but didn’t love the nesting of input boxes in the Command Pallette and the way it used frontmatter for tags (but it is certainly pretty thoughtful, offers a bunch of nice features, and is worth checking out – I think it was just a style thing for me).
OLD: I created a task and bash script to do this, which was OK.

````
What I have ended up doing for now (and I plan to just add this as a feature to my VS Code Markdown Notes extension when I have some time) is a workspace task that lets me hit `alt+n`

![alt-n-new-note.gif](https://kortina.s3.amazonaws.com/_/vscode-markdown-notes/alt-n-new-note.gif)

In
`$HOME/Google-Drive/_notebook./vscode/tasks.json`:

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "New Note",
      "command": "./.vscode/new-note.sh",
      "args": ["${input:noteName}"],
      "problemMatcher": []
    }
  ],
  "inputs": [
    {
      "id": "noteName",
      "description": "Enter a 'Title Case Name' to create `title-case-name.md` with '# Title Case Name' at the top.",
      "type": "promptString"
    }
  ]
}
```

In my main
`keybindings.json`:

```json
// in my main keybindings.json
    {
        "key": "alt+n",
        "command": "workbench.action.tasks.runTask",
        "args": "New Note"
    },
```

In
`$HOME/Google-Drive/_notebook./vscode/new-note.sh`:

```bash
#!/usr/bin/env bash

if [ -z "$1" ]; then
    echo "Missing required argument, note name:"
    echo "./new-note.sh \"Your Note Name\""
    exit 1
fi

name="$1"
# replace all newlines, spaces, non-alphanumeric with '-' characters and trim the end
f=`echo "$name" | tr '\n' '-' | sed -e 's/[^[:alnum:]]/-/g' | tr -s '-' | tr A-Z a-z | sed 's/[- ]*$//g'`
f="$f.md"

if [[ -e "$f" ]]; then
    echo "\`$f\` already exists.";
else
cat > $f <<- EOM
# $name

EOM
fi

# open file in existing window and goto line 3
code -r $f -g 3
```

````

UPDATE: I added a command to the [VS Code Markdown Notes](https://marketplace.visualstudio.com/items?itemName=kortina.vscode-markdown-notes) Extension that duplicates the functionality I had initially implemented as a workspace task.

I have it setup with a shortcut in my `keybindings.json` to open a prompt for a “Title Case Note Name” that will create `title-case-note-name.md` and add a heading:  

        {
            "key": "alt+n",
            "command": "vscodeMarkdownNotes.newNote",
        },
![new-note-command.gif](https://kortina.s3.amazonaws.com/_/vscode-markdown-notes/new-note-command.gif)

### Markdown All in One Extension

[Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) is an extension I installed from the Marketplace that adds some nice conveniences like automatically starting newlines with a `-` character when you are adding to a list, shortcuts for creating a table of contents, additional support for reference links and Math functions, etc.

### Code Spell Checker Extension

I tried a few different spell check extensions. [Spell Right](https://marketplace.visualstudio.com/items?itemName=ban.spellright) seemed to cause a ton of typing lag so I immediately uninstalled it.

Next I tried [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker) and it has been awesome.

I bound `z=` to open the Quick Fix to mirror the shortcut in Vim:  

    "vim.normalModeKeyBindingsNonRecursive": [
        {
          "after": [],
          "before": [
            "z",
            "="
          ],
          "commands": [
            {
              "args": [],
              "command": "editor.action.quickFix"
            }
          ]
        },
### Image Preview Extension

Many apps like Bear and FSNotes have a better inline image preview render in the editor itself. With VS Code, while you can open the rendered Markdown Preview in a tab to the side, you cannot render images inline in the editor.

I don’t always love having the preview open, so I did a little searching and found the [Image Preview](https://marketplace.visualstudio.com/items?itemName=kisstkondoros.vscode-gutter-preview) extension which allows you to view a hover preview with `cmd+k cmd+i`:

![image-preview.png](https://kortina.s3.amazonaws.com/_/vscode-markdown-notes/image-preview.png)

I wanted the image to render as large as possible in the preview without scrolling and found this to be the max I could get working in `settings.json`:  

      "gutterpreview.imagePreviewMaxHeight": 200,

### Customize Colors of Block Quotes, Punctuation, Code, etc.

I use the [Tomorrow Night](https://marketplace.visualstudio.com/items?itemName=ms-vscode.Theme-TomorrowKit) Theme for VS Code, but wanted to mute the color of block quotes and some punctuation. I found some tips for how to really tweak markdown styles in VS Code [here](https://stackoverflow.com/questions/53772087/customize-block-quote-color-in-vscode-theme) on StackOverflow.

Here are the mods I made:

![theme-color-mods.png](https://kortina.s3.amazonaws.com/_/vscode-markdown-notes/theme-color-mods.png)  

      "workbench.colorTheme": "Tomorrow Night",
      "editor.tokenColorCustomizations": {
        "[Tomorrow Night]": {
          "textMateRules": [
            {
              "scope": "markup.quote.markdown",
              "settings": {
                "foreground": "#666"
              }
            },
            {
              "scope": "markup.inline.raw",
              "settings": {
                "foreground": "#f19999"
              }
            },
            {
              "scope": "markup.italic.markdown",
              "settings": {
                "fontStyle": "italic",
              }
            },
            {
              "scope": "punctuation.definition.italic.markdown",
              "settings": {
                "foreground": "#555"
              }
            },
            {
              "scope": "markup.bold.markdown",
              "settings": {
                "fontStyle": "bold",
                "foreground": "#efefef"
              }
            },
            {
              "scope": "punctuation.definition.bold.markdown",
              "settings": {
                "foreground": "#555"
              }
            },
          ]
        }
      },
### Manually Setting Modified Dates

After porting all of my journals from Google Docs, I wanted to backdate the modified dates. This was a good example of something I could easily do with bash since these are all flat files:  

     touch -t "200612310000" journal-2006.md
     touch -t "200712310000" journal-2007.md
     touch -t "200812310000" journal-2008.md
     touch -t "200912310000" journal-2009.md
     touch -t "201212310000" journal-2012.md
     touch -t "201312310000" journal-2013.md
     touch -t "201412310000" journal-2014.md
     touch -t "201512310000" journal-2015.md
     touch -t "201612310000" journal-2016.md
     touch -t "201712310000" journal-2017.md
     touch -t "201812310000" journal-2018.md
     touch -t "201912310000" journal-2019.md
### Notes on Mobile

For years, I was looking around for a notebook that had all the features I wanted on Desktop as well as a way to edit notes on my phone. I never landed on a notes solution on my phone that I liked, and (even when I was using Google Docs, which does have a decent mobile editor) I ultimately always ended up reverting to just emailing myself notes on the go (typing as little as possible) and then fleshing out these ideas when at a full keyboard. The two apps I use to email myself quick notes from my phone are [Drafts](https://getdrafts.com/) and [Captio](http://captio.co/).

Drafts is a little more customizable, Captio has better support when offline / in airplane mode, so I kind of use both.

![drafts-and-captio.png](https://kortina.s3.amazonaws.com/_/vscode-markdown-notes/drafts-and-captio.png)

### Alternatives

#### Google Docs

I was using Google Docs for all my notes for a long time. It has the best / most clear sync (very obvious when something doesn’t sync to cloud), a good mobile editor, good rich text / inline image support.

The killer feature is really the sharing + commenting and I will still use this to share a draft of a blog post to get comments and feedback before publishing.

It is kind of annoying that it’s not markdown, however, (since I use markdown for blog posts – my flow for translating a draft to a blog post was to copy everything from the Google Doc and then use [Paste to Markdown](https://euangoddard.github.io/clipboard2markdown/) to convert it.).

Also, importantly, Google Docs does not support Vim keybindings.

Also, I have a lot of other stuff in Docs that is not personal notes, so the search index was a little cluttered (but search in Docs is obviously great because it’s Google).

#### FSNotes

Of all the dedicated notes apps I tried, my favorite was [FSNotes](https://fsnot.es/). It self describes as:
> File System notes (FSNotes): is modern notational velocity (nvALT) on steroids.

It has a kind of ‘coder ethos’ which I like. Everything is stored in plaintext, you can use [textbundle](http://textbundle.org/) if you want, it is backed by a git repo so you have version history, it is open source, and there is a mobile app.

By default it will use iCloud to sync and is pretty reliable (but you could also use Dropbox or Google Drive to sync as it is filesystem based).

There’s no inline image support (uses a side preview instead) and no vim keybindings.

It’s free.

#### Bear

[Bear](https://bear.app/) is pretty similar to FSNotes with slightly more visual design polish – for example the inline images look great, there is a web clipper, support for cross note linking and tags.

My main gripe with it was that it was backed by a sqlite database rather than flat files, which makes it a little harder to script.

Also, no vim keybindings.

There is a free version and paid version.

#### Roam Research

I have some friends that really love [Roam](https://roamresearch.com/). It’s very optimized for chunking notes as concepts and linking between them.

It feels very much more like a mind mapping tool / memory extension, designed for bullets and snippets, not as well suited to fleshing out these bits into longer form posts.

It is a web based app, not backed by the local filesystem.

#### Notational Velocity, nvalt

[Notational Velocity](http://notational.net/) and [nvalt](https://brettterpstra.com/projects/nvalt/) are some of the classic dedicated note taking apps.

They both have ways to very quickly find or create notes.

My main issue: no Vim keybindings.

Both are free.

#### Other VS Code Extensions

I looked at some of these but ultimately ended up not using:

* [VSCodeNotebook](https://github.com/aviaryan/VSCodeNotebook) - port of the popular SublimeNotebook project.
* [VSNotes](https://marketplace.visualstudio.com/items?itemName=patricklee.vsnotes) - adds some commands for quickly creating notes.

Instead I made the [VS Code Markdown Notes](https://marketplace.visualstudio.com/items?itemName=kortina.vscode-markdown-notes) extension.

It’s [on github](https://github.com/kortina/vscode-markdown-notes) if you want to submit a PR or feature request.
