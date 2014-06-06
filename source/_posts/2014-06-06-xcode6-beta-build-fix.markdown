---
layout: post
title: "xcode6_beta build fix: Undefined symbols _fwrite$UNIX2003"
date: 2014-06-06 10:23:34 +0800
comments: true
categories: 
---
使用xcode6 编译cocos2dx会有以下错误

	
	Undefined symbols for architecture i386:
	  "_fwrite$UNIX2003", referenced from:
	      _unixErrorHandler in libcocos2dx iOS.a(tif_unix.o)
	      _unixWarningHandler in libcocos2dx iOS.a(tif_unix.o)
	      _empty_output_buffer in libcocos2dx iOS.a(jdatadst.o)
	      _term_destination in libcocos2dx iOS.a(jdatadst.o)
	      _Fax3PrintDir in libcocos2dx iOS.a(tif_fax3.o)
	      _PredictorPrintDir in libcocos2dx iOS.a(tif_predict.o)
	  "_strerror$UNIX2003", referenced from:
	      _TIFFOpen in libcocos2dx iOS.a(tif_unix.o)
	ld: symbol(s) not found for architecture i386
	clang: error: linker command failed with exit code 1 (use -v to see invocation)
	

google后发现, [http://stackoverflow.com/questions/8732393/code-coverage-with-xcode-4-2-missing-files](http://stackoverflow.com/questions/8732393/code-coverage-with-xcode-4-2-missing-files) ,针对2dx的只需要在`AppDelegate.cpp`的最后面添加:

``` c++
	extern "C"{
	    size_t fwrite$UNIX2003( const void *a, size_t b, size_t c, FILE *d )
	    {
	        return fwrite(a, b, c, d);
	    }
	    char* strerror$UNIX2003( int errnum )
	    {
	        return strerror(errnum);
	    }
	}
```

即可
