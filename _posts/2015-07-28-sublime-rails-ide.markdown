---
layout: post
title:  "Power up your Sublime Text as Ruby/Rails IDE"
date:   2015-07-28
categories: ruby rails
tags: ruby rails how-to
excerpt: Here I list the plugins you should install, the tweaks you should do and the tricks you should know for Sublime Text to function like an IDE.
---

> “Manners maketh man”

and

> “Tools maketh developer”

The most basic tool of every programmer is the editor that he uses to write code. The oldest software [rivalry](http://www.slate.com/articles/technology/bitwise/2014/05/oldest_software_rivalry_emacs_and_vi_two_text_editors_used_by_programmers.html) happens to be between two text editors.

After my failed attempts with Vim, I decided to stick with Sublime Text and make it so powerful that I don’t have to think about any other editor anymore. As a regular user of Sublime Text, you would already be using plugins like **Package Control**, **SideBarEnhancements**, **SublimeLinter**, **AllAutoComplete** and few themes.

Here I list the plugins you should install, the tweaks you should do and the tricks you should know for Sublime Text to function like an IDE.

### Plugins

1. [**BeautifyRuby**](https://github.com/CraigWilliams/BeautifyRuby)

    Code that is aesthetically pleasing is easy to read and therefore easy to understand.
    BeautifyRuby makes sure your code is well formatted. It maps the `beautify_ruby` command to `cmd+ctrl+k` by default. If you want to customise it, put the following entry in user key bindings. I use `cmd-shift-k`.

        {
          "keys": ["super+shift+k"],
          "command": "beautify_ruby"
        }

2. [**BracketHighlighter**](https://github.com/facelessuser/BracketHighlighter)

    When your code is having more than 3 levels of indentation, it is chaos. This plugin highlights the enclosing/respective `if..end`, `do..end`, `def..end`, `class..end`, thus making your navigation easier.

3. [**ProductiveSnippetsRuby**](https://github.com/janlelis/productive-sublime-snippets-ruby), [**Ruby On Rails Snippets**](https://github.com/tadast/sublime-rails-snippets)

    Though Sublime Text provides basic code snippets, this plugin take them to the next level. My favorite snippets are `‘b-p’` for binding.pry, `‘d-o-zero’` for no argument blocks and `‘d-o-tab’` for blocks that take a argument.
 
    Creating snippets for sublime is really easy and this plugins’s [repo](https://github.com/janlelis/productive-sublime-snippets-ruby/) is a great place to get started.
    Here is a sample,

          // ft for 'focus: true'
          <snippet>
           <content><![CDATA[focus: true]]></content>
           <tabTrigger>ft</tabTrigger>
           <scope>source.ruby</scope>
           <description>focus: true</description>
          </snippet>

4. [**RSpec**](https://github.com/SublimeText/RSpec)

    This plugin provides rspec language syntax, rspec snippets `des, con, eql, neql` and the best of all you can run your specs from the editor itself with `cmd-b`(build).

5. [**SublimeLinter**](https://github.com/SublimeLinter)

    Linting helps in finding out potential errors, so have at-least one linter plugin. I use SublimeLinter. There are others like [SublimeLinter-rubocop](https://github.com/SublimeLinter/SublimeLinter-rubocop) and [SublimeLinter-ruby](https://github.com/SublimeLinter/SublimeLinter-ruby).

6. [**SublimeREPL**](https://github.com/wuub/SublimeREPL)

    With this plugin, you can spin up a pry/rails console in a new editor tab. It doesn’t work out of the box if you are using rbenv. Some plugin files have to be edited to make it work. Follow [this](https://gist.github.com/SudhagarS/cade2b918f7a16c4b0c5) link to get it working smooth. Also it’s super aggressive auto complete can be irritating at times. I sometimes have rails console running in bottom right screen when I am on gird layout in bigger screens. This way I don’t have to go to terminal just to evaluate an expression.

7. [**Glue**](http://gluedocs.readthedocs.org/en/latest/)

    Glue makes it possible to run terminal commands from editor. Though it is not as powerful as terminal, I use it to quickly run git status and to copy/paste/delete files.

8. [**Github Tools**](https://github.com/temochka/sublime-text-2-github-tools)

    With this plugin you can open from the editor the following things on Github,

    1. Repository home page
    2. Pull requests page
    3. Issues page
    4. Current file
    5. Blame page(i.e. commits history)

9. [**GitGutter**](https://github.com/jisaacks/GitGutter)

    Gutter is the area in your editor where line numbers are typically displayed. This plugin shows git diff by placing an icon in the gutter area indicating whether a line has been inserted, modified or deleted.

10. Other plugins you can install based on your needs.

    1. [Bootstrap](https://github.com/JasonMortonNZ/bs3-sublime-plugin)
    2. [Emmet](https://github.com/sergeche/emmet-sublime)
    3. [HTML-CSS-JS prettify](https://github.com/victorporof/Sublime-HTMLPrettify)
    4. [Javascript snippets](https://github.com/jprichardson/sublime-js-snippets)
    5. [SASS](https://github.com/nathos/sass-textmate-bundle)
    6. [SCSS](https://packagecontrol.io/packages/SCSS)
    7. [Ruby slim](https://github.com/slim-template/ruby-slim.tmbundle)

### Shortcuts

Anything you can imagine, sublime has a shortcut for that. Stop using touch pad when you are on the editor, you don’t need it.

1. **Go to Definition**

    In IDEs like eclipse and Intellij, *cmd+click* on a method name at the place where it is called, takes you to its definition. This is very useful as you are juggling between your classes while debugging.
    Sublime has commands goto_definition and jump_back which you can map to the keys you want. Here is how I have mapped them.

        // Default (OSX).sublime-keymap
        { “keys”: [“super+down”], “command”: “goto_definition” },
        { “keys”: [“super+up”], “command”: “jump_back” }

    I do cmd+down on a method call to see its implementation and cmd+up to go back again to the method call. Pretty neat, huh?

2. **Variable Renaming**

    If you want to change a variable name, keep the cursor on the variable name and keep hitting **cmd+d*** *until it selects all the occurrences you want to edit. After you are done selecting, you can edit them all at once.

3. **Swap lines**

    `cmd+ctrl+(up|down)` swaps a line with the above or below line.

4. **Navigate between views**

    Use `cmd+shift+[` and `cmd+shift+]` to navigate back and forth between files.
    Shortcuts `cmd+alt+left` and `cmd+alt+right` also do the same thing. So pick which ever your fingers prefer.

5. **Paste from history**

    Doing `cmd+k`, `cmd+v` opens a panel with everything you have copied so far and you can select which one to paste.

6. **Go to line**

    You can go to a line number by doing `ctrl+g` and typing the line number followed by a semi colon.
    You can map it to the key you want, by putting this entry in user key bindings.

        { 
          "keys": ["cmd+l"], 
          "command": "show_overlay", 
          "args": {"overlay": "goto", "text": ":"} 
        }

    I use `cmd+l` which by default is mapped to `extend_selection` command which I don’t think I would ever use.

    So that’s all I got. If all these seem overwhelming to you in the beginning, give it a week and you will notice a increased efficiency in how write ruby code in Sublime Text.
    So that’s all I got. If all these seem overwhelming to you in the beginning, give it a week and you will notice a positive change in how write ruby code in Sublime Text. 

    I am [@shotwhat](https://twitter.com/shotwhat) on Twitter. Let me know how this helped you.

    This was originally posted on tech [blog](http://tech.crowdfireapp.com/post/125257835004/power-up-your-sublime-text-as-rubyrails-ide) of crowdfire.

