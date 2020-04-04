# News

***4-4-2020***
    Changed the formatting, made some corrections, modified the instructions. They're more complete now, but still let me know if you find an error.

# Before we get started...

To skip straight to the instructions, [click here](https://github.com/H4CKY54CK/h4cky54ck.github.io/blob/master/index.md#L58).

Depending on what lanugage you are using, this may or may not work out for you. I know that it _does_ work for Python, but it may not for another language. I know that it can be done for C/C++, but it requires further modification than this guide provides. If a few people who also rely on this method of running code in a console via a build system were to share their secrets, we could maybe compile a large collection of language specific instructions for how to edit FROM ST3 but build in an EXTERNAL console seamlessly.

#### Alternatives to Sublime Text 3's Lack of Support for Console Input

You're building a project. It requires user input. You proceed to check your progress, and build from within ST3. It asks for your input. And... What? It's not working? What am I doing wrong?...

Sublime Text 3 does not support user input in this way. Actually, if you press CTRL + \`, you can bring up the _actual_ console, and enter `print(sys.version)`, revealing a Python 3.3.6 interpreter. What gives?

When you 'build' your project in ST3, you are actually seeing the output/results of your code in the _build panel_. It's not a console, ergo, no input. There are plenty of quality reading materials already explaining the mechanics behind how ST3 opens a pipe, passes data to a console, streams the results back to ST3, etc... I'm not covering that here. I'd just explain it poorly. So, what can we do to satisfy our need to use ST3, which will allow input? Well, there are several ways we can achieve this.

#### Edit in ST3, build from IDLE

Just, no.

Common suggestion. A valid one, even. I just found this to be excessively aggravating, having to open IDLE, and then search for your file.

#### Plugins

There are some really great plugins that simulate an IDE, but in ST3. The rub is, that it works from a tab. So, when you build, it'll open a new tab. I find this solution less than optimal, but it is a perfectly valid and impressive alternative. Some noteable plugins:

- SublimeREPL
- Terminus

#### Console

We could use a console to run the project we're writing in the editor. Oh, we're in the wrong folder. Oh, I forgot to save. Oh, just no.

#### Build Systems and Variants

Well, the console would have been okay, but even if we were in the correct folder, and didn't run into any hiccups, we still have to invoke python and our filename. What was our filename again? `somethingitypedwithouthtinkingbecauseimrunningoutofideasfornames.py` Oh, did you misspell that?

As ridiculous as that is (both the exaggeration and the actual idea of it), we could actually use ST3's _build systems_ to accomplish our nitpicky needs.

#### Goals

[x] Minimal amount of steps from the editor to the testing.
[x] Don't have to invoke the script manually
[x] User input

#### Bonus Goals

[x] Build with one keypress
[x] Run your script in an environment where end-users of your script will most likely be anyway.

There are obviously going to be times when you actually have to run your script from the command line, and type it all out, such as with a command line interface you might be building. But in that case, that's exactly what you want to be doing anyway, because you want to test your arguments for your CLI.

Conveniently, regardless of those two situations, you'll end up being in a console anyway. And if you aren't familiar with a console, this is a great way to ease into it. 

Some people are not comfortable with downloading extra packages to accomplish a task that can be done without them. But if you would like to take the more challenging path, then I won't stop you. I'll even help you. But first, I'll explain the easiest method, by far. Skip this section if you are in the "I like to make my life more difficult because I, myself, am difficult" category.

# Instructions

Firstly, we need a package from Sublime's Package Control, called PackageResourceViewer. Once installed, open the command palette, and start typing `extract`. You should see `PackageResourceViewer - Extract Package`. Click on it. Find your package within this list. We'll be using `Python`/`Python 3`, and so that's what I'll be referring to from now on (instead of another language). Extract it. Once finished, go to `Preferences -> Browse Packages`. Find `Python` or `Python 3`. Enter that folder. Now find the file `Python.sublime-build` (or `Python 3.sublime-build` I suppose, if you're on linux). Either right click and open with ST3, or click and drag it into an open ST3 editor.

As a precaution, let's do a quick `Save As...` (CTRL + SHIFT + S), give it a different name, and save it under your `User` packages folder. You may have to go up into the parent folder to find it. I'll save mine as `SuperPython.sublime-build`. _Now_ let's do our editing, seeing as we can no longer accidentally overwrite the default one.

The _only_ things we need to do here, is add a build "variant". Here's what my build system looks like (DON'T change yours if it doesn't look the same. YOUR build system will ALREADY be correct for YOUR system. MINE is correct for MY system. I hope I've made that sufficiently clear.)

**(Windows)**

```python
{
    "shell_cmd": "python -u \"$file\"",
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
    "selector": "source.python",

    "env": {"PYTHONIOENCODING": "utf-8"},

    "variants":
    [
        {
            "name": "Syntax Check",
            "shell_cmd": "python -m py_compile \"${file}\"",
        }
    ]
}
```

See where it says "variants?" That's where we will be adding ours. Add a comma after the closing curly bracket on the 3rd-to-last line, then depending on your OS, fill in the two fields, similarly to how it's been done for `Syntax Check` so that it looks like this:

**(Windows)**

```python
{
    "shell_cmd": "python -u \"$file\"",
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
    "selector": "source.python",

    "env": {"PYTHONIOENCODING": "utf-8"},

    "variants":
    [
        {
            "name": "Syntax Check",
            "shell_cmd": "python -m py_compile \"${file}\"",
        },
        {
            "name": "SuperConsole",
            "shell_cmd": "start cmd /k python -u \"${file}\"",
        },
    ]
}
```

**(Linux (may not work on all linux, but this is what I've done for Ubuntu))**
(It may not look _exactly_ like this, but it will be close.)
(You'll also need to do a quick `sudo apt install xterm`. I'm not a linux expert, so please let me know if there's a more native solution.)


```python
{
    "shell_cmd": "python3 -00 -u \"$file\"",
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
    "selector": "source.python.3",
}
```

Note how I don't have a variant field for some reason. Whatever. We'll add one.

```python
{
    "shell_cmd": "python3 -00 -u \"$file\"",
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
    "selector": "source.python.3",
    "variants":
    [
        {
            "name": "SuperConsole",
            "shell_cmd": "xterm -hold -e python -00 -u \"${file}\"",
        },
    ]
}
```

**(MacOS)**
(Apparently the same thing you'd do in Windows works on MacOS. I don't have a Mac, so I can't verify this.)

**I'm not comfortable putting a build system here that doesn't work, so this will have to be completed later.**

If you use the menu to launch your builds, you're done. Just change your build system in `Preferences -> Build System -> SuperPython`, and build. I use a key binding, so let's wrap up with that. Let's go to `Preferences -> Key Bindings` and find a key binding that we don't mind altering.

```python
    { "keys": ["f6"], "command": "toggle_setting", "args": {"setting": "spell_check"} },
```

Nobody likes spellcheck, so we'll use this one. Copy it from the default settings, over to the user settings tab. (You should be seeing a split window. If not, you're probably in the wrong thing). We want to change the `"command"` to `"build"`, and the `"args"` to `"variant": "SuperConsole"`

```python
[
    { "keys": ["f6"], "command": "build", "args": {"variant": "SuperConsole"} },
]
```
If you have already set some other key bindings, they will all be BETWEEN those two square brackets., and you should already know this. If this will be your first addition to your key bindings, and it's empty, c/p it as-is. It's universal, since it's specifically for ST3. You can save your key binding settings now, and close it. You're all good to go from here. Just go to `Tools -> Build System -> SuperPython`, and test it. Open a new file, enter `print('Hellurr?')`, save it, and use your new key binding (`F6` for me), and it should pop up in a new console. And it stays open, even if the build crashes, so you can see the traceback, too.

If a new console did not open up after building, Try building from the menu, `Tools -> Build`. It should ask you what to build with. Select the top one (or whichever one just says Python), and it should run NORMALLY in ST3. NOW try your key binding again. It should definitely work this time.

That's all. I'll finish updating the rest of the guide shortly, but aside from that, Thanks for Watching.


<!-- [Link](url) and ![Image](src) -->
