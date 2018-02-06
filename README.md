# mod_xsendfile for Apache 2 #

## Overview ##
mod_xsendfile is a small Apache2 module that processes X-SENDFILE headers output handlers might have registered.

If it encounters the presence of such header it will discard all output and send the file specified by that header instead using Apache internals including all optimizations like caching-headers and sendfile or mmap if configured.

It is useful for processing script-output of e.g. php, perl, python or any *cgi.

## Useful? ##
Yep, it is useful.

* Some applications require checking for special privileges.
* Other have to lookup values first (e.g.. from a DB) in order to correctly process a download request.
* Or store values (download-counters come into mind).

### Benefits ###
* Uses apache internals
* Optimal delivery through sendfile and mmap (if available).
* Sets correct cache headers such as Etag and If-Modified-Since as if the file was statically served.
* Processes cache headers such as If-None-Match or If-Modified-Since.
* Support for ranges.

## Installation ##
1. Grab the source.
2. Compile and install
    * In general:
apxs -cia mod_xsendfile.c
Debian/Ubuntu uses apxs2:
apxs2 -cia mod_xsendfile.c
Mac users might want to build fat binaries:
apxs -cia -Wc,"-arch i386 -arch x86_64" -Wl,"-arch i386 -arch x86_64" mod_xsendfile.c
Restart apache
That's all.
