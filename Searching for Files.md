## I/O Redirection

Through the command __shell__ we can __redirect__ the three standard filestreams so that we can get input from either a file or another command instead of from our keyboard, and we can write output and errors to files or send them as input for subsequent commands.

For example, if we have a program called __do_something__ that reads from __stdin__ and writes to __stdout__ and __stderr__, we can change its input source by using the less-than sign ( < ) followed by the name of the file to be consumed for input data:

`$ do_something < input-file`

If you want to send the output to a file, use the greater-than sign (>) as in:

`$ do_something > output-file`

Because __stderr__ is not the same as __stdout__, error messages will still be seen on the terminal windows in the above example.

In Linux, all open files are represented internally by what are called __file descriptors__. Simply put, these are represented by numbers starting at zero. __stdin__ is file descriptor __0__, __stdout__ is file descriptor __1__, and __stderr__ is file descriptor __2__.

If you want to redirect __stderr__ to a separate file, you use __stderr__’s file descriptor number (2), the greater-than sign (>), followed by the name of the file you want to hold everything the running command writes to __stderr__:

`$ do_something 2> error-file`

A special shorthand notation can be used to put anything written to file descriptor 2 (__stderr__) in the same place as file descriptor 1 (__stdout__): 2>&1

`$ do_something > all-output-file 2>&1`

__bash__ permits an easier syntax for the above:

`$ do_something >& all-output-file`

## Searching for Files

### Using locate

The __locate__ utility program performs a search through a previously constructed _database of files and directories_ on your system, matching all entries that contain a specified character string. This can sometimes result in a very long list.

`$ locate zip | grep bin`

will list all files and directories with both "zip" and "bin" in their name.

__locate__ utilizes the database created by another program, __updatedb__. Most Linux systems run this automatically once a day. However, you can update it at any time by just running __updatedb__ from the command line as the root user.


### Using find

When no arguments are given, __find__ lists all files in the current directory and all of its subdirectories. Commonly used options to shorten the list include _-name_, _-iname_ (also ignore the case of file names), and _-type_ (which will restrict the results to files of a certain specified type, such as __d__ for directory, __l__ for symbolic link or __f__ for a regular file, etc). 

Searching for files and directories named "gcc":

`$ find /usr -name gcc`

Searching only for directories named "gcc":

`$ find /usr -type d -name gcc`

Searching only for regular files named "test1":

`$ find /usr -type f -name test1`

##### Using Advanced find Options

Another good use of __find__ is being able to run commands on the files that match your search criteria. The _-exec_ option is used for this purpose.

To find and remove all files that end with .swp:

`$ find -name "*.swp" -exec rm {} ’;’`

The _{ }_ (squiggly brackets) is a place holder that will be filled with all the file names that result from the __find__ expression, and the preceding command will be run on each one individually.

One can also use the _-ok_ option which behaves the same as _-exec_ except that __find__ will prompt you for permission before executing the command. 

##### Finding Files Based on Time and Size

Finding based on time:

`$ find / -ctime 3`

Here, _-ctime_ is when the inode meta-data (i.e., file ownership, permissions, etc) last changed; it is often, but not necessarily when the file was first created. You can also search for accessed/last read (_-atime_) or modified/last written (_-mtime_) times. The number is the number of days and can be expressed as either a number (n) that means exactly that value, +n which means greater than that number, or -n which means less than that number. There are similar options for times in minutes (as in _-cmin_, _-amin_, and _-mmin_).

Finding based on sizes:

`$ find / -size 0`

Note the size here is in 512-byte blocks, by default; you can also specify bytes (__c__), kilobytes (__k__), megabytes (__M__), gigabytes (__G__), etc. As with the time numbers above, file sizes can also be exact numbers (n), +n or -n.

For example, to find files greater than 10 MB in size and running a command on those files:

`$ find / -size +10M -exec command {} ’;’`
