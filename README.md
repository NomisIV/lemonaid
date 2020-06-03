# Lemonaid - The friendly lemonbar helper

> Do whatever you want with it, I don't care. Just don't drink it.

## What is it?

Lemonaid is a bash script written to simplify the set up process of lemonbar.
I am aware of other wrappers, like Captain, but they cannot be controlled with a shell script.

## Why is it?

I wanted a wrapper which could load each status item asychronously, while reading the input from stdin instead of configuration files.
This bash script streamlines the process of creating a status bar script.
While it's relatively easy to set up a `while true` loop with a `sleep 1` command, it really does not allow for a lot of options.
What makes the setup difficult is to set up asynchonous loading of each status bar section (e.g. clock updates every second, while date only updates every minute)

## How to use it?

`lemonaid` runs the script located at `~/.config/lemonbar/lemonbarrc`, and compiles and prints the statusbar.
When using lemonaid, think of it as a dictionary (named array or whatever) of status bar values, with the syntax `key:value`.
It does not matter which `key`s are used, except for `format` wich holds the format of the status bar.
The other status bar items can be accesed in `format` by their respective `key` wrapped in square brackets.
Like this: `format:%{c}[key]`
The status bar will update after any value has been modified (yes, even `format`)

### Example lemonbarrc

```shell
#!/bin/sh
echo "format:%{r}[time]"
while true
do
  echo "time:$(date +'%T')"
  sleep 1
done
```

## How does it work?

Dark bash magic.

Just kidding.
Lemonbar uses a named pipe (fifo), into which the output of `lemonbarrc` is directed.
Everytime the fifo recives input, it is loaded into a cache (the key/value pairs).
Then the format is printed except that all the `[key]`s are substituted with their `value`

Sounds simple, but was decievingly hard to make.
