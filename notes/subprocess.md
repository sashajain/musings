OS helps schedule what `processes` (programs) can use the Central `Processing` Unit (and other resources). The CPU can only deal with a few processes at a time but operates much faster than processes do (at the nanosecond timescale).


`subprocess.run()`
- blocking
- creates child process
- expects list of tokens (`shlex` module can help)
- returns CompletedProcess object onece the child process has ended
- there are many redundant ways of achieving a similar thin in the `subprocess` package
- the underlying class for the whole subprocess module is the `Popen` class and the `Popen()` constructor
  - note: can use `Popen` for non-blocking calls, but not explored here

Simple example:

``` python
try:
    result = subprocess.run(
        ["python", "timer.py", "5"],
        timeout=10,
        check=True,
        capture_output=True,
        encoding = 'utf-8'
    )
except FileNotFoundError as exc:
    print(f"Process failed because the executable could not be found.\n{exc}")
except subprocess.CalledProcessError as exc:
    print(
        f"Process failed because did not return a successful return code. "
        f"Returned {exc.returncode}\n{exc}"
    )
except subprocess.TimeoutExpired as exc:
    print(f"Process timed out.\n{exc}")

result.returncode
# facilitated by capture_output=True
# is string rather than byte object due to encoding 
result.stdout 
```

## Running shell commands or direct programs

There are actually two separate processes that make up the typical command-line experience:

   1.  The interpreter, which is typically thought of as the whole CLI. Common interpreters are Bash on Linux, Zsh on macOS, or PowerShell on Windows. In this tutorial, the interpreter will be referred to as the shell.
   2.  The interface, which displays the output of the interpreter in a window and sends user keystrokes to the interpreter. The interface is a separate process from the shell, sometimes called a terminal emulator.

It’s common to think that calling run() is somehow the same as typing a command in a terminal interface, but there are important differences. Many text-based programs can operate independently from the shell, in these cases you can use subprocess directly with the text-based programs, rather than via shell.

Examples for calling a program via Shell (not needed for standalone text programs)-
subprocess.run(["bash", "-c", "ls /usr/bin | grep pycode"])
subprocess.run(["ls /usr/bin | grep pycode"], shell=True)

## Communication With Processes

### The Standard I/O Streams

Child process inherits these streams from the parent. The stdout of the Python interpreter is inherited by the subprocess.

### Pipes

A pipe, or pipeline, is a special stream that, instead of having one file handle as most files do, has two. One handle is read-only, and the other is write-only. The name is very descriptive—a pipe serves to pipe a byte stream from one process to another. It’s also buffered, so a process can write to it, and it’ll hold onto those bytes until it’s read, like a dispenser.

## When not to use subprocess

It might be tempting to think that subprocess can be used for concurrency, and in simple cases, it can be. But, in line with the sloppy Python philosophy, it’s probably only going to be to hack something together quickly. If you want something more robust, then you’ll probably want to start looking at the multiprocessing module.

Depending on the task that you’re attempting, you may be able to accomplish it with the asyncio or threading modules. If everything is written in Python, then these modules are likely your best bet.

There are many problems that you might initially reach for subprocess to solve, but then you’ll find a specific module or library that solves it for you. This tends to be a theme with subprocess since it is quite a low-level utility.

An example of something that you might want to do with subprocess is to open a web browser to a specific page. However, for that, it’s probably best to use the Python module webbrowser. The webbrowser module uses subprocess under the hood but handles all the finicky cross-platform and browser differences that you might encounter.
