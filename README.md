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
    * In general: `apxs -cia mod_xsendfile.c`
    * Debian/Ubuntu uses apxs2: `apxs2 -cia mod_xsendfile.c`
    * Mac users might want to build fat binaries: `apxs -cia -Wc,"-arch i386 -arch x86_64" -Wl,"-arch i386 -arch x86_64" mod_xsendfile.c`
3. Restart apache
4. That's all.

## Configuration ##
### Headers ###
* `X-SENDFILE` - Send the file referenced by this headers instead of the current response body
* `X-SENDFILE-TEMPORARY` - Like `X-SENDFILE`, but the file will be deleted afterwards. The file must originate from a path that has the `AllowFileDelete` flag set.

### XSendFile ###
__Description__   Enables or disables header processing  
__Syntax__        XSendFile on | off  
__Default__       XSendFile off  
__Context__       Server config, virtual host, directory, .htaccess  

Setting `XSendFile on` will enable processing

The file specified in `X-SENDFILE` header will be sent instead of the handler output.

The value (file name) given by the header is assmumed to be url-encoded, i.e. unescaping/url-decoding will be performed. See XSendFileUnescape. If you happen to store files using already url-encoded file names, you must "double" encode the names... %20 -> %2520

If the response lacks the `X-SENDFILE` header the module will not perform any processing.

### XSendFileIgnoreEtag ###
__Description__   Ignore script provided Etag headers  
__Syntax__        XSendFileIgnoreEtag on | off  
__Default__       XSendFileIgnoreEtag off  
__Context__       Server config, virtual host, directory, .htaccess  

Setting `XSendFileIgnoreEtag on` will ignore all ETag headers the original output handler may have set. This is helpful for applications that will generate such headers even for empty content.

### XSendFileIgnoreLastModified ###
__Description__   Ignore script provided LastModified headers  
__Syntax__        XSendFileIgnoreLastModified on | off  
__Default__       XSendFileIgnoreLastModified off  
__Context__       Server config, virtual host, directory, .htaccess  

Setting `XSendFileIgnoreLastModified on` will ignore all Last-Modified headers the original output handler may have set. This is helpful for applications that will generate such headers even for empty content.

### XSendFileUnescape ###
