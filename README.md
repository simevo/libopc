# libopc - Open Packaging Convention (OPC) C++ library

The Open Packaging Convention (OPC) is part of the OfficeOpenXML standard — the standard behind the `.docx`, `.xslx` and `.pptx` Office formats.

The OPC defines a container format which can be used to store any kind of data and it is not only suited for Office format. E.g. the `XMLPaperSpecification` (XPS) also uses OPC as the packaging layer.

In many ways OPC can be seen as a successor of OLE containers used by the proprietary `.doc`, `.xls` and `.ppt` formats. Unlike OLE containers — which are modeled according to the FAT format — OLE containers are valid `.zip` archived plus some extra metadata.

## Building

### On Linux / macOS

**untested!**

```
./configure
make
```

### On Linux with system libs

Install dependencies with (tested on a pristine Debian Linux 12 Bookworm install with `docker run --rm -it -v "$PWD:/src" -w /src debian:bookworm bash`):

```
sudo apt-get install libxml2-dev pkg-config zlib1g-dev python3 g++ make dpkg-dev debhelper lintian
```

then:

```
./configure --with-zlib=yes --with-zlib-ldflags="`pkg-config zlib --libs`" --with-libxml=yes --with-libxml-cppflags="`pkg-config libxml-2.0 --cflags`" --with-libxml-ldflags="`pkg-config libxml-2.0 --libs`" --with-python=/usr/bin/python3
make
```

### On Windows

**untested!**

* Optionally generate project files: c:\Python26\python.exe generate.py --include Makefile.xml win32-*-msvc-*

* Open win32\solution.sln in Visual Studio.

## Testing

Test the build with:

```
python3 test.py --target=static
```

## Releasing

### In a ZIP file (win32)

**untested!**

```
c:\Python26\python.exe generate.py --include Makefile.xml --package libopc-sdk-0.0.1.zip win32-release-msvc 
```
### In a ZIP file (macOS)

**untested!**

```
python generate.py --include Makefile.xml --package libopc-sdk-0.0.1.zip --config-dir=build/config  darwin-release-gcc
```

### Linux package

```
./configure --with-zlib=yes --with-zlib-ldflags="`pkg-config zlib --libs`" --with-libxml=yes --with-libxml-cppflags="`pkg-config libxml-2.0 --cflags`" --with-libxml-ldflags="`pkg-config libxml-2.0 --libs`" --with-python=/usr/bin/python3 --arch=i686
make
python3 ./generate.py --with-zlib=yes --with-zlib-ldflags="`pkg-config zlib --libs`" --with-libxml=yes --with-libxml-cppflags="`pkg-config libxml-2.0 --cflags`" --with-libxml-ldflags="`pkg-config libxml-2.0 --libs`" --package usr.zip --config-dir=build --include Makefile.xml linux-debug-gcc-i686
```

### Debian Linux package

**untested!**

1. Adjust version in `config/libopc.pc`

2. Adjust `debian/changelog`

3. Build the library:

     ./configure --with-zlib=yes --with-zlib-ldflags="`pkg-config zlib --libs`" --with-libxml=yes --with-libxml-cppflags="`pkg-config libxml-2.0 --cflags`" --with-libxml-ldflags="`pkg-config libxml-2.0 --libs`" --arch=any
     DESTDIR=`pwd`/build/linux-debug-gcc-any/install
     make install

4. Build the package: `dpkg-buildpackage` or `dpkg-buildpackage -B`

5. QC the package: `lintian -i -I --show-overrides ../libopc_0.0.2_i386.changes  | less`

## References

- http://www.ericwhite.com/blog/libopc-open-packaging-convention/

- [http://msdn.microsoft.com/en-us/library/ee361919(v=office.11).aspx](http://msdn.microsoft.com/en-us/library/ee361919\(v=office.11\).aspx)

- https://en.wikipedia.org/wiki/Open_Packaging_Conventions

- http://www.debian.org/doc/manuals/maint-guide/index.en.html
