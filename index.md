## Alternatives to Sublime Text 3's Lack of Support for Console Input

You're building a project. It requires user input. You proceed to check your progress, and build from within ST3. It asks for your input. And... What? It's not working? What am I doing wrong?...

Sublime Text 3 does not support user input in this way. Actually, if you press CTRL + \`, you can bring up the _actual_ console, and enter `print(sys.version)`, revealing a Python 3.3.6 interpreter. What gives?

When you 'build' your project in ST3, you are actually seeing the output/results of your code in the _build panel_. It's not a console, ergo, no input. There are plenty of quality reading materials already explaining the mmechanics behind how ST3 opens a pipe, etc... I'm not covering that here. I'd just explain it poorly. So, what can we do to satisfy our need to use ST3, which will allow input? Well, there are several ways we cna achieve this.

### Code in ST3, build from IDLE (or any other editor in general)

Just, no.

### Plugins

There are some really great plugins that simulate an IDE, but in ST3. The rub is, that it works from a tab. So, when you build, it'll open a new tab. I find this solution less than optimal, but it is a perfectly valid and impressive alternative. Some noteable plugins:

- SublimeREPL
- Terminus

### Console

We could use a console to run the project we're writing in the editor. Oh, we're in the wrong folder. Oh, I forgot to save. Oh, just no.

### Build Systems and Variants

Well, the console would have been okay, but even if we were in the correct folder, and didn't run into any hiccups, we still have to invoke python and our filename. What was our filename again? `somethingitypedwithouthtinkingbecauseimrunningoutofideasfornames.py` Oh, did you misspell that?

As ridiculous as that is (both the exaggeration and the actual idea of it), we could actually use ST3's _build systems_ to get around having to enter anything in the console at all (except when we _want_ to, with user input, which is our goal, here). There are actually two different ways we can achieve the same thing. I highly recommend this first method, as it eliminate the chance for user error.

#### Duplicate the Build System

Depending on what lanugage you are using, this may or may not work out for you. I know that it _does_ work for Python, but it may not for something else. 

Firstly, we need a package from Package Control, called PackageResourceViewer. Once downloaded, open the command palette again, and start typing `extract`. You should see `PackageResourceViewer - Extract Package`. Click on it. Find your package within this list. We'll be using Python, and so that's what I'll be referring to from now on. Extract it. Once finished, go to `Preferences -> Browse Packages`. Find `Python`. Enter that folder. Find `Python.sublime-build`. Either right click and open with ST3, or click and drag it into an open ST3 editor.

As a precaution, let's do a quick `Save As...` (CTRL + SHIFT + S), give it a different name, and save it under your `User` packages folder. You may have to go up into the parent folder to find it. I'll save mine as `SuperPython.sublime-build`. _Now_ let's do our editing, since we can't accidentally overwrite the default one.

The _only_ things we need to do here, is add a build "variant". There's already one here, which we'll use as a template. Here's what my build system looks like (DON'T change yours if it doesn't look the same. YOUR build system will ALREADY be correct for YOUR system. MINE is correct for MY system. I hope I've made that sufficiently clear.) 

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

See where it says "variants?" That's where we will be adding ours. We'll copy and paste theirs, and add a comma after theirs...

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
            "name": "Syntax Check",
            "shell_cmd": "python -m py_compile \"${file}\"",
        },
    ]
}
```

Change the name to something we'll be using later, and follow the next step, according to your OS:

Windows:
- Add to the second line (it may say, "cmd", "shell_cmd", or some other thing, possibly), right after the third parentheses: `start cmd /k`

Linux:
- Coming soon.

MacOS:
- Coming soon

Since I'm on Windows, I've done this:

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
            "shell_cmd": "start cmd /k python -m py_compile \"${file}\"",
        }
    ]
}
```

See how we've only adjust the VALUES of the KEYS? (it is a dict, after all)

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
If you have already set some other key bindings, they will all be BETWEEN those two square brackets., and you should already know this. If this will be your first addition to your key bindings, and it's empty, c/p it as-is. It's universal, since it's specifically for ST3. Let's take a break for a moment to very briefly explain brackets.

- PARENTHESIS: ( )
- SQUARE BRACKETS: [ ]
- CURLY BRACKETS: { }
~~- I'LL BE ANYTHING I DAMN WANT TO BE, LESS THAN AND GREATER THAN, ARROWS, EYES FOR FACES (>_<), ...: < >~~
- ANGLE BRACKETS: < >

< and > are not formally considered grouping symbols, as they have other official designations, such as math operators, or whatever you call it on unix where you do `cat from.txt >> to.txt`, yet here we are.

Anyway, you can save your key bindings settings now, and close it. You're all good to go from here. Just go to `Tools -> Build System -> SuperPython`, and test it. Open a new file, enter `print('Hellurr?')`, save it, and use your new key binding (F6 for me), and it should pop up in a new console. And it stays open, even if the build crashes, so you can see the traceback now.

If a new console did not open up after building, Try building from the menu, `Tools -> Build`. It should ask you what to build with. Select the top one (or whichever one just says Python), and it should run NORMALLY in ST3. NOW try your key binding again. It should definitely work this time.

That's all. I'll finish updating the rest of the guide shortly, but aside from that, Thanks for Watching.


<!-- [Link](url) and ![Image](src) -->
