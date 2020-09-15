+++
title = "Zap Gremlins"
slug = "zap-gremlins"
[taxonomies]
tags = ["vim"]
+++

When copying text into Vim from Microsoft applications, such as Outlook or Word, oftentimes at the end of every line will be a `^M` character. It looks something like this in my text editor:

```
This is an example of gremlins^M
appearing at the end of every^M
line. Who likes to look at such^M
atrocities? Certainly not me.^M
```

In the Unix world, we end lines with a single newline, but in the Windows world, the end-of-line is marked by a paring of a newline with a carriage return. The carriage return character shows up in Vim as `^M`.

I call them "gremlins." They are mostly harmless but annoying, since they make my text a bit uglier. More seriously, they can also cause issues with other applications.

What to do, especially if we are happily living in the Unix world? Destroy them all with a single command, of course. 

## The command

Here is the Ex command I use to zap gremlins from my document, in all its cryptic, oops-my-cat-typed-this glory:

```vim
maHmb:%s/<C-V><CR>//ge<CR>'bzt'a
```

Don't worry, there's a shortcut for that, which I'll get to later.

The vomit of seemingly random characters looks more horrifying than it actually is. Let's break it down. It's actually a series of individual Ex commands, all run together as one big "command."

## The breakdown

1. `ma` creates a new mark at the cursor's current position, saving it as mark `a`[^marks].
2. `H` moves the cursor to the top of the screen.
3. `mb` creates a new mark at the cursor's current position, saving it as mark `b`. This is so that we can have a reference to the original scroll position[^marks].
4. `:%s/<C-V><CR>//ge<CR>` is a global find-and-replace command, which deletes all `^M` characters in the current buffer (to find the gremlins, we search for `<C-V><CR>`).
5. `'b` moves the cursor to mark `b`, the previous top of screen[^marks].
6. `zt` forces a redraw, to restore the original scroll position.
7. `'a` moves the cursor back to mark `a`, to restore the starting cursor position[^marks].

\#4 is the key command in the sequence; everything else going on is just bookkeeping to maintain a consistent cursor and scroll position afterwards.

## The shortcut

In our Vim configuration, we define a new key binding (shortcut) and paste that gnarly command after it.

```vim
" zap gremlins (the Windows ^M)
nnoremap <Leader>mm maHmb:%s/<C-V><CR>//ge<CR>'bzt'a
```

In my case, my Vim leader key is a comma, which means that simply typing `,mm` while in normal mode will run our compound Ex command to zap all gremlins in the current buffer.

[^marks]: The names of the marks I used (`a` and `b`) are completely arbitrary. You can use whatever letters you wish (a-z within the current buffer).
