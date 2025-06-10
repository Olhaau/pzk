---
title: connections function - RDocumentation
---

# connections function - RDocumentation

url:: https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/connections
up: [[sources]]

#source

# connections: Functions to Manipulate Connections (Files, URLs, ...)

## Description

Functions to create, open and close connections, i.e., “generalized files”, such as possibly compressed files, URLs, pipes, etc.

## Usage

```
file(description = "", open = "", blocking = TRUE,
     encoding = getOption("encoding"), raw = FALSE,
     method = getOption("url.method", "default"))
url(description, open = "", blocking = TRUE,
    encoding = getOption("encoding"),
    method = getOption("url.method", "default"),
    headers = NULL)

gzfile(description, open = "", encoding = getOption("encoding"),
       compression = 6)

bzfile(description, open = "", encoding = getOption("encoding"),
       compression = 9)

xzfile(description, open = "", encoding = getOption("encoding"),
       compression = 6)

unz(description, filename, open = "", encoding = getOption("encoding"))

pipe(description, open = "", encoding = getOption("encoding"))

fifo(description, open = "", blocking = FALSE,
     encoding = getOption("encoding"))

socketConnection(host = "localhost", port, server = FALSE,
                 blocking = FALSE, open = "a+",
                 encoding = getOption("encoding"),
                 timeout = getOption("timeout"))

open(con, …)
# S3 method for connection
open(con, open = "r", blocking = TRUE, …)

close(con, …)
# S3 method for connection
close(con, type = "rw", …)

flush(con)

isOpen(con, rw = "")
isIncomplete(con)
```

## Arguments

description  
character string. A description of the connection: see ‘Details’.  
open  
character string. A description of how to open the connection (if it should be opened initially). See section ‘Modes’ for possible values.  
blocking  
logical. See the ‘Blocking’ section.  
encoding  
The name of the encoding to be assumed. See the ‘Encoding’ section.  
raw  
logical. If true, a ‘raw’ interface is used which will be more suitable for arguments which are not regular files, e.g.character devices. This suppresses the check for a compressed file when opening for text-mode reading, and asserts that the ‘file’ may not be seekable.  
method  
character string, partially matched to `c("default", "internal", "wininet", "libcurl")`: see ‘Details’.  
headers  
named character vector of HTTP headers to use in HTTP requests. It is ignored for non-HTTP URLs. The `User-Agent` header, coming from the `HTTPUserAgent` option (see [options](https://www.rdocumentation.org/link/options?package=base&version=3.6.2)) is used as the first header, automatically.  
compression  
integer in 0--9. The amount of compression to be applied when writing, from none to maximal available. For `xzfile` can also be negative: see the ‘Compression’ section.  
timeout  
numeric: the timeout (in seconds) to be used for this connection. Beware that some OSes may treat very large values as zero: however the POSIX standard requires values up to 31 days to be supported.  
filename  
a filename within a zip file.  
host  
character string. Host name for the port.  
port  
integer. The TCP port number.  
server  
logical. Should the socket be a client or a server?  
type  
character string. Currently ignored.  
rw  
character string. Empty or `"read"` or `"write"`, partial matches allowed.  
…  
arguments passed to or from other methods.

## Value

`file`, `pipe`, `fifo`, `url`, `gzfile`, `bzfile`, `xzfile`, `unz` and `socketConnection` return a connection object which inherits from class `"connection"` and has a first more specific class.

`open` and `flush` return `NULL`, invisibly.

`close` returns either `NULL` or an integer status, invisibly. The status is from when the connection was last closed and is available only for some types of connections (e.g., pipes, files and fifos): typically zero values indicate success. Negative values will result in a warning; if writing, these may indicate write failures and should not be ignored.

`isOpen` returns a logical value, whether the connection is currently open.

`isIncomplete` returns a logical value, whether the last read attempt was blocked, or for an output text connection whether there is unflushed output.

## URLs

`url` and `file` support URL schemes file://, http://, https:// and ftp://.

`method = "libcurl"` allows more schemes: exactly which schemes is platform-dependent (see [libcurlVersion](https://www.rdocumentation.org/link/libcurlVersion?package=base&version=3.6.2)), but all Unix-alike platforms will support https:// and most platforms will support ftps://.

Most methods do not percent-encode special characters such as spaces in http:// URLs (see [URLencode](https://www.rdocumentation.org/link/URLencode?package=base&version=3.6.2)), but it seems the `"wininet"` method does.

A note on file:// URLs. The most general form (from RFC1738) is file://host/path/to/file, but R only accepts the form with an empty `host` field referring to the local machine.

On a Unix-alike, this is then file:///path/to/file, where path/to/file is relative to `/`. So although the third slash is strictly part of the specification not part of the path, this can be regarded as a way to specify the file `/path/to/file`. It is not possible to specify a relative path using a file URL.

In this form the path is relative to the root of the filesystem, not a Windows concept. The standard form on Windows is file:///d:/R/repos: for compatibility with earlier versions of R and Unix versions, any other form is parsed as R as file:// plus `path_to_file`. Also, backslashes are accepted within the path even though RFC1738 does not allow them.

No attempt is made to decode a percent-encoded file: URL: call [URLdecode](https://www.rdocumentation.org/link/URLdecode?package=base&version=3.6.2) if necessary.

All the methods attempt to follow redirected HTTP URLs, but the `"internal"` method is unable to follow redirections to HTTPS URLs.

Server-side cached data is always accepted.

Function [download.file](https://www.rdocumentation.org/link/download.file?package=base&version=3.6.2) and several contributed packages provide more comprehensive facilities to download from URLs.  

## Modes

Possible values for the argument `open` are


`"r"` or
`"rt"`

:   Open for reading in text mode.


`"w"` or
`"wt"`

:   Open for writing in text mode.


`"a"` or
`"at"`

:   Open for appending in text mode.


`"rb"`

:   Open for reading in binary mode.


`"wb"`

:   Open for writing in binary mode.


`"ab"`

:   Open for appending in binary mode.


`"r+"`,
`"r+b"`

:   Open for reading and writing.


`"w+"`,
`"w+b"`

:   Open for reading and writing, truncating file initially.


`"a+"`,
`"a+b"`

:   Open for reading and appending.

Not all modes are applicable to all connections: for example URLs can only be opened for reading. Only file and socket connections can be opened for both reading and writing. An unsupported mode is usually silently substituted.

If a file or fifo is created on a Unix-alike, its permissions will be the maximal allowed by the current setting of `umask` (see [Sys.umask](https://www.rdocumentation.org/link/Sys.umask?package=base&version=3.6.2)).

For many connections there is little or no difference between text and binary modes. For file-like connections on Windows, translation of line endings (between LF and CRLF) is done in text mode only (but text read operations on connections such as [readLines](https://www.rdocumentation.org/link/readLines?package=base&version=3.6.2), [scan](https://www.rdocumentation.org/link/scan?package=base&version=3.6.2) and [source](https://www.rdocumentation.org/link/source?package=base&version=3.6.2) work for any form of line ending). Various R operations are possible in only one of the modes: for example [pushBack](https://www.rdocumentation.org/link/pushBack?package=base&version=3.6.2) is text-oriented and is only allowed on connections open for reading in text mode, and binary operations such as [readBin](https://www.rdocumentation.org/link/readBin?package=base&version=3.6.2), [load](https://www.rdocumentation.org/link/load?package=base&version=3.6.2) and [save](https://www.rdocumentation.org/link/save?package=base&version=3.6.2) can only be done on binary-mode connections.

The mode of a connection is determined when actually opened, which is deferred if `open = ""` is given (the default for all but socket connections). An explicit call to `open` can specify the mode, but otherwise the mode will be `"r"`. (`gzfile`, `bzfile` and `xzfile` connections are exceptions, as the compressed file always has to be opened in binary mode and no conversion of line-endings is done even on Windows, so the default mode is interpreted as `"rb"`.) Most operations that need write access or text-only or binary-only mode will override the default mode of a non-yet-open connection.

Append modes need to be considered carefully for compressed-file connections. They do **not** produce a single compressed stream on the file, but rather append a new compressed stream to the file. Readers may or may not read beyond end of the first stream: currently R does so for `gzfile`, `bzfile` and `xzfile` connections.  

## Compression

R supports `gzip`, `bzip2` and `xz` compression (also read-only support for its precursor, `lzma` compression).

For reading, the type of compression (if any) can be determined from the first few bytes of the file. Thus for `file(raw = FALSE)` connections, if `open` is `""`, `"r"` or `"rt"` the connection can read any of the compressed file types as well as uncompressed files. (Using `"rb"` will allow compressed files to be read byte-by-byte.) Similarly, `gzfile` connections can read any of the forms of compression and uncompressed files in any read mode.

(The type of compression is determined when the connection is created if `open` is unspecified and a file of that name exists. If the intention is to open the connection to write a file with a *different* form of compression under that name, specify `open = "w"` when the connection is created or [unlink](https://www.rdocumentation.org/link/unlink?package=base&version=3.6.2) the file before creating the connection.)

For write-mode connections, `compress` specifies how hard the compressor works to minimize the file size, and higher values need more CPU time and more working memory (up to ca 800Mb for `xzfile(compress = 9)`). For `xzfile` negative values of `compress` correspond to adding the `xz` argument -e: this takes more time (double?) to compress but may achieve (slightly) better compression. The default (`6`) has good compression and modest (100Mb memory) usage: but if you are using `xz` compression you are probably looking for high compression.

Choosing the type of compression involves tradeoffs: `gzip`, `bzip2` and `xz` are successively less widely supported, need more resources for both compression and decompression, and achieve more compression (although individual files may buck the general trend). Typical experience is that `bzip2` compression is 15% better on text files than `gzip` compression, and `xz` with maximal compression 30% better. The experience with R [save](https://www.rdocumentation.org/link/save?package=base&version=3.6.2) files is similar, but on some large `.rda` files `xz` compression is much better than the other two. With current computers decompression times even with `compress = 9` are typically modest and reading compressed files is usually faster than uncompressed ones because of the reduction in disc activity.  

## Encoding

The encoding of the input/output stream of a connection can be specified by name in the same way as it would be given to [iconv](https://www.rdocumentation.org/link/iconv?package=base&version=3.6.2): see that help page for how to find out what encoding names are recognized on your platform. Additionally, `""` and `"native.enc"` both mean the ‘native’ encoding, that is the internal encoding of the current locale and hence no translation is done.

When writing to a text connection, the connections code always assumes its input is in native encoding, so e.g. [writeLines](https://www.rdocumentation.org/link/writeLines?package=base&version=3.6.2) has to convert text to native encoding. [writeLines](https://www.rdocumentation.org/link/writeLines?package=base&version=3.6.2) does not do the conversion when `useBytes=TRUE` (for expert use only), but the connections code still behaves as if the text was in native encoding, so any attempt to convert encoding (`encoding` argument other than `""` and `"native.enc"`) in connections will produce incorrect results.

When reading from a text connection, the connections code, after re-encoding based on the `encoding` argument, returns text that is assumed to be in native encoding; an encoding mark is only added by functions that read from the connection, so e.g. [readLines](https://www.rdocumentation.org/link/readLines?package=base&version=3.6.2) can be instructed to mark the text as `"UTF-8"` or `"latin1"`, but [readLines](https://www.rdocumentation.org/link/readLines?package=base&version=3.6.2) does no further conversion. To allow reading text in `"UTF-8"` on a system that cannot represent all such characters in native encoding (currently only Windows), a connection can be internally configured to return the read text in UTF-8 even though it is not the native encoding; currently [readLines](https://www.rdocumentation.org/link/readLines?package=base&version=3.6.2) and [scan](https://www.rdocumentation.org/link/scan?package=base&version=3.6.2) use this feature when given a connection that is not yet open and, when using the feature, they unconditionally mark the text as `"UTF-8"`.

Re-encoding only works for connections in text mode: reading from a connection with re-encoding specified in binary mode will read the stream of bytes, but mixing text and binary mode reads (e.g., mixing calls to [readLines](https://www.rdocumentation.org/link/readLines?package=base&version=3.6.2) and [readChar](https://www.rdocumentation.org/link/readChar?package=base&version=3.6.2)) is likely to lead to incorrect results.

The encodings `"UCS-2LE"` and `"UTF-16LE"` are treated specially, as they are appropriate values for Windows ‘Unicode’ text files. If the first two bytes are the Byte Order Mark `0xFEFF` then these are removed as some implementations of [iconv](https://www.rdocumentation.org/link/iconv?package=base&version=3.6.2) do not accept BOMs. Note that whereas most implementations will handle BOMs using encoding `"UCS-2"` and choose the appropriate byte order, some (including earlier versions of `glibc`) will not. There is a subtle distinction between `"UTF-16"` and `"UCS-2"` (see <https://en.wikipedia.org/wiki/UTF-16>): the use of characters in the ‘Supplementary Planes’ which need surrogate pairs is very rare so `"UCS-2LE"` is an appropriate first choice (as it is more widely implemented).

As from R 3.0.0 the encoding `"UTF-8-BOM"` is accepted for reading and will remove a Byte Order Mark if present (which it often is for files and webpages generated by Microsoft applications). If a BOM is required (it is not recommended) when writing it should be written explicitly, e.g.by `writeChar("\ufeff", con, eos = NULL)` or `writeBin(as.raw(c(0xef, 0xbb, 0xbf)), binary_con)`

Encoding names `"utf8"`, `"mac"` and `"macroman"` are not portable, and not supported on all current R platforms. `"UTF-8"` is portable and `"macintosh"` is the official (and most widely supported) name for ‘Mac Roman’. (As from R 3.4.0, R maps `"utf8"` to `"UTF-8"` internally.)

Requesting a conversion that is not supported is an error, reported when the connection is opened. Exactly what happens when the requested translation cannot be done for invalid input is in general undocumented. On output the result is likely to be that up to the error, with a warning. On input, it will most likely be all or some of the input up to the error.

It may be possible to deduce the current native encoding from [Sys.getlocale](https://www.rdocumentation.org/link/Sys.getlocale?package=base&version=3.6.2)`("LC_CTYPE")`, but not all OSes record it.  

## Blocking

Whether or not the connection blocks can be specified for file, url (default yes), fifo and socket connections (default not).

In blocking mode, functions using the connection do not return to the R evaluator until the read/write is complete. In non-blocking mode, operations return as soon as possible, so on input they will return with whatever input is available (possibly none) and for output they will return whether or not the write succeeded.

The function [readLines](https://www.rdocumentation.org/link/readLines?package=base&version=3.6.2) behaves differently in respect of incomplete last lines in the two modes: see its help page.

Even when a connection is in blocking mode, attempts are made to ensure that it does not block the event loop and hence the operation of GUI parts of R. These do not always succeed, and the whole R process will be blocked during a DNS lookup on Unix, for example.

Most blocking operations on HTTP/FTP URLs and on sockets are subject to the timeout set by `options("timeout")`. Note that this is a timeout for no response, not for the whole operation. The timeout is set at the time the connection is opened (more precisely, when the last connection of that type -- http:, ftp: or socket -- was opened).  

## Fifos

Fifos default to non-blocking. That follows S version 4 and is probably most natural, but it does have some implications. In particular, opening a non-blocking fifo connection for writing (only) will fail unless some other process is reading on the fifo.

Opening a fifo for both reading and writing (in any mode: one can only append to fifos) connects both sides of the fifo to the R process, and provides an similar facility to `file()`.  

## Clipboard

`file` can be used with `description = "clipboard"` in modes `"r"` and `"w"` only. in mode `"r"` only. This reads the X11 primary selection (see <http://standards.freedesktop.org/clipboards-spec/clipboards-latest.txt>), which can also be specified as `"X11_primary"` and the secondary selection as `"X11_secondary"`. On most systems the clipboard selection (that used by ‘Copy’ from an ‘Edit’ menu) can be specified as `"X11_clipboard"`.

When a clipboard is opened for reading, the contents are immediately copied to internal storage in the connection.

When writing to the clipboard, the output is copied to the clipboard only when the connection is closed or flushed. There is a 32Kb limit on the text to be written to the clipboard. This can be raised by using e.g.`file("clipboard-128")` to give 128Kb.

The clipboard works in Unicode wide characters, so encodings might not work as one might expect. Unix users wishing to *write* to one of the X11 selections may be able to do so via `xclip` (<http://sourceforge.net/projects/xclip/>) or `xsel` (<http://www.vergenet.net/~conrad/software/xsel/>), for example by `pipe("xclip -i", "w")` for the primary selection.

macOS users can use `pipe("pbpaste")` and `pipe("pbcopy", "w")` to read from and write to that system's clipboard.  

## File paths

In most cases these are translated to the native encoding.

The exceptions are `file` and `pipe` on Windows, where a `description` which is marked as being in UTF-8 is passed to Windows as a ‘wide’ character string. This allows files with names not in the native encoding to be opened on file systems which use Unicode file names (such as NTFS but not FAT32).

## Details

The first nine functions create connections. By default the connection is not opened (except for a `socketConnection`), but may be opened by setting a non-empty value of argument `open`.

For `file` the description is a path to the file to be opened or a complete URL (when it is the same as calling `url`), or `""` (the default) or `"clipboard"` (see the ‘Clipboard’ section). Use `"stdin"` to refer to the C-level ‘standard input’ of the process (which need not be connected to anything in a console or embedded version of R, and is not in `RGui` on Windows). See also [stdin](https://www.rdocumentation.org/link/stdin?package=base&version=3.6.2)`()` for the subtly different R-level concept of `stdin`. See [nullfile](https://www.rdocumentation.org/link/nullfile?package=base&version=3.6.2)`()` for a platform-independent way to get filename of the null device.

For `url` the description is a complete URL including scheme (such as http://, https://, ftp:// or file://). Method `"internal"` is that available since connections were introduced, method `"wininet"` is only available on Windows (it uses the WinINet functions of that OS) and method `"libcurl"` (using the library of that name: <http://curl.haxx.se/libcurl/>) is required on a Unix-alike but optional on Windows. Method `"default"` uses method `"internal"` for file: URLs and `"libcurl"` for `ftps:` URLs. On a Unix-alike it uses `"libcurl"` for http:, https: and ftp: URLs; on Windows `"wininet"` for http:, ftp: and https: URLs. Proxies can be specified: see [download.file](https://www.rdocumentation.org/link/download.file?package=base&version=3.6.2).

For `gzfile` the description is the path to a file compressed by `gzip`: it can also open for reading uncompressed files and those compressed by `bzip2`, `xz` or `lzma`.

For `bzfile` the description is the path to a file compressed by `bzip2`.

For `xzfile` the description is the path to a file compressed by `xz` (<https://en.wikipedia.org/wiki/Xz>) or (for reading only) `lzma` (<https://en.wikipedia.org/wiki/LZMA>).

`unz` reads (only) single files within zip files, in binary mode. The description is the full path to the zip file, with `.zip` extension if required.

For `pipe` the description is the command line to be piped to or from. This is run in a shell, on Windows that specified by the `COMSPEC` environment variable.

For `fifo` the description is the path of the fifo. (Support for `fifo` connections is optional but they are available on most Unix platforms and on Windows.)

The intention is that `file` and `gzfile` can be used generally for text input (from files, http:// and https:// URLs) and binary input respectively.

`open`, `close` and `seek` are generic functions: the following applies to the methods relevant to connections.

`open` opens a connection. In general functions using connections will open them if they are not open, but then close them again, so to leave a connection open call `open` explicitly.

`close` closes and destroys a connection. This will happen automatically in due course (with a warning) if there is no longer an R object referring to the connection.

A maximum of 128 connections can be allocated (not necessarily open) at any one time. Three of these are pre-allocated (see [stdout](https://www.rdocumentation.org/link/stdout?package=base&version=3.6.2)). The OS will impose limits on the numbers of connections of various types, but these are usually larger than 125.

`flush` flushes the output stream of a connection open for write/append (where implemented, currently for file and clipboard connections, [stdout](https://www.rdocumentation.org/link/stdout?package=base&version=3.6.2) and [stderr](https://www.rdocumentation.org/link/stderr?package=base&version=3.6.2)).

If for a `file` or (on most platforms) a `fifo` connection the description is `""`, the file/fifo is immediately opened (in `"w+"` mode unless `open = "w+b"` is specified) and unlinked from the file system. This provides a temporary file/fifo to write to and then read from.

## References

Chambers, J. M. (1998) *Programming with Data. A Guide to the S Language.* Springer.

Ripley, B. D. (2001) Connections. *R News* , **1/1** , 16--7. <https://www.r-project.org/doc/Rnews/Rnews_2001-1.pdf>

## See Also

[textConnection](https://www.rdocumentation.org/link/textConnection?package=base&version=3.6.2), [seek](https://www.rdocumentation.org/link/seek?package=base&version=3.6.2), [showConnections](https://www.rdocumentation.org/link/showConnections?package=base&version=3.6.2), [pushBack](https://www.rdocumentation.org/link/pushBack?package=base&version=3.6.2).

Functions making direct use of connections are (text-mode) [readLines](https://www.rdocumentation.org/link/readLines?package=base&version=3.6.2), [writeLines](https://www.rdocumentation.org/link/writeLines?package=base&version=3.6.2), [cat](https://www.rdocumentation.org/link/cat?package=base&version=3.6.2), [sink](https://www.rdocumentation.org/link/sink?package=base&version=3.6.2), [scan](https://www.rdocumentation.org/link/scan?package=base&version=3.6.2), [parse](https://www.rdocumentation.org/link/parse?package=base&version=3.6.2), [read.dcf](https://www.rdocumentation.org/link/read.dcf?package=base&version=3.6.2), [dput](https://www.rdocumentation.org/link/dput?package=base&version=3.6.2), [dump](https://www.rdocumentation.org/link/dump?package=base&version=3.6.2) and (binary-mode) [readBin](https://www.rdocumentation.org/link/readBin?package=base&version=3.6.2), [readChar](https://www.rdocumentation.org/link/readChar?package=base&version=3.6.2), [writeBin](https://www.rdocumentation.org/link/writeBin?package=base&version=3.6.2), [writeChar](https://www.rdocumentation.org/link/writeChar?package=base&version=3.6.2), [load](https://www.rdocumentation.org/link/load?package=base&version=3.6.2) and [save](https://www.rdocumentation.org/link/save?package=base&version=3.6.2).

[capabilities](https://www.rdocumentation.org/link/capabilities?package=base&version=3.6.2) to see if `fifo` connections are supported by this build of R.

[gzcon](https://www.rdocumentation.org/link/gzcon?package=base&version=3.6.2) to wrap `gzip` (de)compression around a connection.

[options](https://www.rdocumentation.org/link/options?package=base&version=3.6.2) `HTTPUserAgent`, `internet.info` and `timeout` are used by some of the methods for URL connections.

[memCompress](https://www.rdocumentation.org/link/memCompress?package=base&version=3.6.2) for more ways to (de)compress and references on data compression.

[extSoftVersion](https://www.rdocumentation.org/link/extSoftVersion?package=base&version=3.6.2) for the versions of the `zlib` (for `gzfile`), `bzip2` and `xz` libraries in use.

To flush output to the Windows and macOS consoles, see [flush.console](https://www.rdocumentation.org/link/flush.console?package=base&version=3.6.2).

## Examples

[Run this code](https://www.datacamp.com/datalab/preview?_tag=rdocs&rdocsPath=packages%2Fbase%2Fversions%2F3.6.2%2Ftopics%2Fconnections&utm_source=r-docs&utm_medium=docs&utm_term=connections&utm_content=run_example_in_datalab)  

    # NOT RUN {
    zzfil <- tempfile(fileext=".data")
    zz <- file(zzfil, "w")  # open an output file connection
    cat("TITLE extra line", "2 3 5 7", "", "11 13 17", file = zz, sep = "\n")
    cat("One more line\n", file = zz)
    close(zz)

               readLines(zzfil)

               unlink(zzfil)

               
    zzfil <- tempfile(fileext=".gz")
    zz <- gzfile(zzfil, "w")  # compressed file
    cat("TITLE extra line", "2 3 5 7", "", "11 13 17", file = zz, sep = "\n")
    close(zz)

               readLines(zz <- gzfile(zzfil))

               close(zz)

               unlink(zzfil)
    zz # an invalid connection

    zzfil <- tempfile(fileext=".bz2")
    zz <- bzfile(zzfil, "w")  # bzip2-ed file
    cat("TITLE extra line", "2 3 5 7", "", "11 13 17", file = zz, sep = "\n")
    close(zz)
    zz # print() method: invalid connection
    print(readLines(zz <- bzfile(zzfil)))

               close(zz)

               unlink(zzfil)

               
    ## An example of a file open for reading and writing
    Tpath <- tempfile("test")
    Tfile <- file(Tpath, "w+")
    c(isOpen(Tfile, "r"), isOpen(Tfile, "w")) # both TRUE
    cat("abc\ndef\n", file = Tfile)
    readLines(Tfile)
    seek(Tfile, 0, rw = "r") # reset to beginning
    readLines(Tfile)
    cat("ghi\n", file = Tfile)
    readLines(Tfile)
    Tfile # -> print() :  "valid" connection
    close(Tfile)
    Tfile # -> print() :  "invalid" connection
    unlink(Tpath)

               
    ## We can do the same thing with an anonymous file.
    Tfile <- file()
    cat("abc\ndef\n", file = Tfile)
    readLines(Tfile)

               close(Tfile)

               
    # }
    # NOT RUN {
    ## fifo example -- may hang even with OS support for fifos
    if(capabilities("fifo")) {
      zzfil <- tempfile(fileext="-fifo")
      zz <- fifo(zzfil, "w+")
      writeLines("abc", zz)
      print(readLines(zz))

                 close(zz)

                 unlink(zzfil)

               }
    # }
    # NOT RUN {
    ## Unix examples of use of pipes

    # read listing of current directory
    readLines(pipe("ls -1"))

    # remove trailing commas.  Suppose
    oldwd <- setwd(tempdir())
    writeLines(c("450, 390, 467, 654,  30, 542, 334, 432, 421,",
    "357, 497, 493, 550, 549, 467, 575, 578, 342,",
    "446, 547, 534, 495, 979, 479"), "data2_")
    % cat data2_

               450, 390, 467, 654,  30, 542, 334, 432, 421,

               357, 497, 493, 550, 549, 467, 575, 578, 342,

               446, 547, 534, 495, 979, 479

               # Then read this by

               scan(pipe("sed -e s/,$// data2_"), sep = ",")

               unlink("data2_"); setwd(oldwd)

               

               # convert decimal point to comma in output: see also write.table

               # both R strings and (probably) the shell need \ doubled

               zzfil <- tempfile("outfile")

               zz <- pipe(paste("sed s/\\\\./,/ >", zzfil), "w")

               cat(format(round(stats::rnorm(48), 4)), fill = 70, file = zz)

               close(zz)

               file.show(zzfil, delete.file = TRUE)

               # }

               # NOT RUN {

               ## example for a machine running a finger daemon

               

               con <- socketConnection(port = 79, blocking = TRUE)

               writeLines(paste0(system("whoami", intern = TRUE), "\r"), con)

               gsub(" *$", "", readLines(con))

               close(con)

               # }

               # NOT RUN {

               # }

               # NOT RUN {

               ## Two R processes communicating via non-blocking sockets

               # R process 1

               con1 <- socketConnection(port = 6011, server = TRUE)

               writeLines(LETTERS, con1)

               close(con1)

               

               # R process 2

               con2 <- socketConnection(Sys.info()["nodename"], port = 6011)

               # as non-blocking, may need to loop for input

               readLines(con2)

               while(isIncomplete(con2)) {

                  Sys.sleep(1)

                  z <- readLines(con2)

                  if(length(z)) print(z)

               }

               close(con2)

               

               ## examples of use of encodings

               # write a file in UTF-8

               cat(x, file = (con <- file("foo", "w", encoding = "UTF-8"))); close(con)

               # read a 'Windows Unicode' file

               A <- read.table(con <- file("students", encoding = "UCS-2LE")); close(con)

               # }
Run the code above in your browser using [DataLab](https://www.datacamp.com/datalab/preview?_tag=rdocs&rdocsPath=packages%2Fbase%2Fversions%2F3.6.2%2Ftopics%2Fconnections&utm_source=r-docs&utm_medium=docs&utm_term=connections&utm_content=run_example_in_datalab)
