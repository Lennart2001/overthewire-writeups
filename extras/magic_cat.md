
# Magic Cat

Let's look at the description of `cat`. 

``` text
NAME
     cat – concatenate and print files

SYNOPSIS
     cat [-belnstuv] [file ...]

DESCRIPTION
     The cat utility reads files sequentially, writing them to the standard output.  The file operands are processed in command-line order.  If file is a
     single dash (‘-’) or absent, cat reads from the standard input.  If file is a UNIX domain socket, cat connects to it and then reads it until EOF.  This
     complements the UNIX domain binding capability available in inetd(8).
```




``` bash
echo -e "aaaaaaaaaaaaaaaaaaaa\xef\xbe\xad\xde"
```

This output is then piped into the `./narnia0` binary:

``` bash
| ./narnia0
```

And you're also using `cat` to keep the shell open:

``` bash
cat;
```

### Understanding the Piping Mechanism

The entire command is enclosed in parentheses, forming a subshell:

``` bash
( echo -e "aaaaaaaaaaaaaaaaaaaa\xef\xbe\xad\xde"; cat; )
```

The output of this subshell is piped into `./narnia0`. The `echo` command's output is sent first, followed by the output of `cat`.

Here's how it works step-by-step:

1. `echo -e "aaaaaaaaaaaaaaaaaaaa\xef\xbe\xad\xde"` sends the buffer overflow payload to the standard input (`stdin`) of `./narnia0`.
2. `./narnia0` reads the payload into its `buf` variable, causing the overflow and changing the value of `val`.
3. The `cat` command, when run without arguments, reads from `stdin`. In the context of a pipeline, it effectively "forwards" what it reads from its `stdin` to its standard output (`stdout`), which is also the `stdin` of `./narnia0`.
4. Because `cat` is forwarding its `stdin` to `./narnia0`, any subsequent input you type into the terminal will be sent directly to `./narnia0`.

### Why doesn't `cat` read files?

The `cat` command, when run without arguments, reads from its `stdin`. It doesn't automatically read files in the current directory; you would need to specify a filename for that.

In the context of the pipeline, `cat` is essentially forwarding anything it reads from its `stdin` to the `stdin` of `./narnia0`. So instead of reading files, it's acting as a conduit for your keyboard input to go to `./narnia0`, which is particularly useful if `./narnia0` gives you a shell (as is the case here when the exploit is successful).








