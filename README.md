# AssRender-Vapoursynth

AssRender-Vapoursynth is an Vapoursynth plugin that renders ASS/SSA and SRT (without the HTML-like markup) subtitles, based on the source of pinterf's fork. It uses libass to render the subtitles, which makes it the fastest and most correct ASS renderer for Vapoursynth.

This also means that it is much more picky about script syntax than VSFilter and friends, so keep that in mind before blaming the filter. Yes, people have reported a lot of errors that were actually the script author’s fault.

## Usage

assrender.TextSub(clip clip, string file, [string vfr, int hinting, float scale, float line_spacing, float dar, float sar, int top, int bottom, int left, int right, string charset, int debuglevel, string fontdir, string srt_font, string colorspace])

`string file`

Your subtitle file. May be ASS, SSA or SRT.
	
`string vfr`

Specify timecodes v1 or v2 file when working with VFRaC.
	
`int hinting`

Font hinting mode. Choose between none (0, default), light (1), normal (2) and Freetype native (3) autohinting.
	
`float scale`

Font scale. Defaults to 1.0.
	
`float line_spacing`

Line spacing in pixels. Defaults to 1.0 and won’t be scaled with frame size.
	
`float dar`, `float sar`

Aspect ratio. Of course you need to set both parameters.
	
`int top`, `int bottom`, `int left`, `int right`

Margins. They will be added to the frame size and may be negative.
	
`string charset`

Character set to use, in GNU iconv or enca format. Defaults to detect the BOM and fallback to UTF-8 if BOM not found.
Example enca format: `enca:pl:cp1250`
(guess the encoding for Polish, fall back on `cp1250`)
		
`int debuglevel`

How much crap assrender is supposed to spam to stderr.
	
`string fontdir`

Additional font directory.
Useful if you are lazy but want to keep your system fonts clean.
Default value: `""`

`string srt_font`

Font to use for SRT subtitles.
Defaults to whatever Fontconfig chooses for “sans-serif”.
	
`string colorspace`

The color space of your (YUV) video. Possible values:
 - Rec2020, BT.2020
 - Rec709, BT.709
 - Rec601, BT.601
 - PC.709
 - PC.601
 - TV.fcc, PC.fcc
 - TV.240m, PC.240m
 - none, guess
 
Default is to use the ASS script's "YCbCr Matrix" or "Video Colorspace" property.

Recognized .ASS properties: "TV.601" "TV.709", "PC.601" "PC.709" "TV.240m" "PC.240m" "TV.fcc" "PC.fcc" and "none". 

"none" and "guess" decides upon on video resolution: width > 1280 or height > 576 → BT.709, else → BT.601.
When no hint found in ASS script and 'colorspace' parameter is empty then the default is BT.601.

## Build instructions

### Windows Visual Studio 2019

* Prequisite: vsyasm integration
  - get VSYASM from https://github.com/ShiftMediaProject/VSYASM
  - run install_script.bat

* Clone repo

  Clone https://github.com/Masaiki/assrender from VS IDE or 

      git clone https://github.com/Masaiki/assrender
      git submodule update --init --recursive --remote

* Build:
  Open solution file from IDE

* Note:
  if you are using libass from https://github.com/Masaiki/libass/tree/SMP-for-assrender (ShiftMediaProject/libass with latest commit of libass/libass and [directwrite: request font on demand](https://github.com/libass/libass/pull/477))
  
  install [VSNASM](https://github.com/ShiftMediaProject/VSNASM) instead to avoid asm compile error

### Windows GCC (mingw installed by msys2)

* Clone repo

      git clone https://github.com/Masaiki/assrender
        
  This environment is not using the git submodules, we need libass as a package.
  There is no need for submodule update.

* Prequisite: libass package

  - List libass versions

        $ pacman -Ss libass

    Output:

	     mingw32/mingw-w64-i686-libass 0.15.0-1
	     A portable library for SSA/ASS subtitles rendering (mingw-w64)
	     mingw64/mingw-w64-x86_64-libass 0.15.0-1
	     A portable library for SSA/ASS subtitles rendering (mingw-w64)

  - Get package

    Example for x64 version:
  
        $ pacman -S mingw64/mingw-w64-x86_64-libass

    Output:

          resolving dependencies...
          looking for conflicting packages...
          warning: dependency cycle detected:
          warning: mingw-w64-x86_64-harfbuzz will be installed before its mingw-w64-x86_64-freetype dependency

          Packages (10) mingw-w64-x86_64-fontconfig-2.13.93-1
                      mingw-w64-x86_64-freetype-2.10.4-1
                      mingw-w64-x86_64-fribidi-1.0.10-2
                      mingw-w64-x86_64-glib2-2.66.4-1
                      mingw-w64-x86_64-graphite2-1.3.14-2
                      mingw-w64-x86_64-harfbuzz-2.7.4-1
                      mingw-w64-x86_64-libpng-1.6.37-3  mingw-w64-x86_64-pcre-8.44-2
                      mingw-w64-x86_64-wineditline-2.205-3
                      mingw-w64-x86_64-libass-0.15.0-1

          Total Download Size:    6.92 MiB
          Total Installed Size:  42.31 MiB

          :: Proceed with installation? [Y/n]

     Choose Y and wait

* Build
  from the 'build' folder under project root:

      del ..\CMakeCache.txt
      cmake .. -G "MinGW Makefiles"
      cmake --build . --config Release --clean-first 

### Linux
* Clone repo

      git clone https://github.com/Masaiki/assrender
      cd assrender
      cmake -B build -S .
      cmake --build build --clean-first
  
  Remark: submodules are not needed, libass is used as a package.

* Find binaries at
    
      build/assrender/libassrender.so

* Install binaries

      cd build
      sudo make install

## Licenses
  For all modules: see msvc/licenses

## Links
* Doom9 forum: https://forum.doom9.org/showthread.php?t=148926
* Avisynth wiki: http://avisynth.nl/index.php/AssRender
* libass original: https://github.com/libass/libass
* libass submodule used for msvc https://github.com/ShiftMediaProject/libass
* Aegisub: https://github.com/Aegisub/Aegisub

## Change log of pinterf's fork
### 0.35 (20210304)
* Windows MSVC: Update to libass v0.15
  (git submodule update --init --recursive --remote)
  For changes since v0.14 see https://github.com/libass/libass/blob/master/Changelog
* don't guess base on video resolution (realfinder)
  if .ass file has no Matrix info then it should be treated as it "Rec601" to maintain compatibility
* Parameter 'colorspace' default value is no longer "guess"
* Add more color options: PC.709, PC.601, TV.fcc, PC.fcc, TV.240m, PC.240m, none.
  "none" and "guess" implies "guess-by-resolution".
* Fix: possible crash on initializing phase (buffer overread, linux crashed, Windows was just lucky)

### 0.34 (20210301)
* Fix the fix: revert matrix change made in 0.33	
* Fix: Check matrix from .ASS file "YCbCr Matrix:" section besides "Video Colorspace:"
  Recognized values are "tv.601" and "tv.709"

### 0.33 (20210228)
* Fix: wrong Bt.709 matrix (it wasn't :) )

### 0.32 (20210227)
* Fix: treat I420 variant as YV12 instead of unsupported color space

### 0.31 (20210218)
* Fix colors for planar RGB
* code: hidden ifdef FOR_AVISYNTH_26_ONLY for Avisynth 2.6-only build

### 0.30 (20210217)
* From now assrender does not works with classic Aviysnth: high-bitdepth helper function calls
* 10-16 bit support (including RGB48 and RGB64)
* YV411, Planar RGB support

### 0.29 (20210216 - pinterf)
* project moved to https://github.com/pinterf/assrender from https://github.com/vadosnaprimer/assrender
* Move to Visual Studio 2019 - v142 platform toolset
* Add .def module definition file for Avisynth 2.6 std compatibility (function name mangling)
* Update Avisynth C headers
* Check Linux and gcc-MinGW CMake build
* Add build instructions and change log to README

### no version (20190114 - vadosnaprimer)
* https://github.com/vadosnaprimer/assrender/
* add batch that lets not to change deps sdk and vs version copied from SMP libass
* update SMP submodules

### no version (20161018 - Blitzker)
* https://github.com/Blitzker/assrender
* Visual Studio 2015 support

### 0.28 (20120226 - pingplug)
* small changes and update version to 0.28

### 0.27 (20150202 - pingplug)
* https://github.com/pingplug/assrender
* add a simple .rc file :-)
* cache the last img to rend faster if img not changed 
* add YUY2 and YV16 support

### 0.25 (20120420 - lachs0r)
* moved to github
* code restructured
* added support for the BT.709 color space and the 'Video Colorspace' property that has been introduced with recent versions of Aegisub.
* binary:
  - updated everything, switched to MinGW-w64 (same toolchain as mplayer2 now)

### 0.24.1 (20110922 - lachs0r)
* binary changes only
* binary:
  - updated libass to current git HEAD
  - switched Harfbuzz to libass’ compatibility branch  
  - compiled Harfbuzz without Uniscribe backend
* fixes lots of crashes and misbehavior

### 0.24 (20110729)
* fixing the performance regression

### 0.23 (20110728)
* disabled font hinting by default
* binary:
 - updated libass to current git HEAD and included Harfbuzz:
 - added support for bidirectional text, Arabic shaping etc.
 - added proper support for @fonts (vertical writing)
 - slight performance regression (glyph cache not hooked up with Harfbuzz yet)
 - updated FreeType to current git HEAD:
 - fixed outline stroker for some broken fonts

### 0.22 (20110618 - lachs0r) 
* fixed that annoying hang on vector clips

### 0.21 (20110608 - lachs0r) 
* fixed YV12 subsampling so it no longer looks horrible, which should be rather good news for most users.
* temporarily removed YV16 support
* renamed parameter verbosity → debuglevel
* code cleanups
* binary:
  - reverted to GCC 4.5.2 (4.6 miscompiles MinGW)

### 0.20 (20110601)
* fixed RGB32 support (it’s actually usable with BlankClip(pixel_type="RGB32") now).
* fixed the masksub stuff
* properly output debug messages to stderr instead of stdout
* reformatted source and corrected/removed some comments
* modified CMakeLists.txt to strip the binary by default
* binary:
  - now built with GCC 4.6 instead of 4.5.2
  - included enca again
  - patched fontconfig:
    - prettier debug output
    - use the correct location for its cache

### 0.19 (2011-02-01 - lachs0r)
This is a bugfix/cleanup release.
* fixed possible buffer overflows in timecodesv1 and SRT parsing
* fixed random crashes on unloading
* probably fixed compilation with MSVC (patch by TheFluff)
* very slightly improved performance with GCC
* various code cleanups

### 0.16 (2011-01-25 - lachs0r)
* improved YV12 support (should be somewhat usable now)
* added support for RGB24, YV24, YV16 and Y8 (YUY2 coming soon)
* added SRT subtitle format support, additional parameter: srt_font (font to use for srt subs)
* exposed some libass parameters:
  - line_spacing (line spacing)
  - dar, sar (aspect ratio)
  - top, bottom, left, right (margins)
  - fontdir (additional font directory)
* masksub equivalent if used on a blankclip
  (still buggy - read source for details)
* no more global variables

### 0.16 (2011-01-17 - lachs0r)
* added rudimentary YV12 support (chroma subsampling still needs work)
* binary: Previously, I linked against a very old avisynth_c.lib 
  now you shouldn’t get any error messages about "avisynth_c.dll"
* tidied up the RGB32 blitter a bit

### 0.16 (2011-01-16)
* implemented VFRaC support via timecodes files (v1 and v2 supported)

### 0.15 (2011-01-16 - lachs0r)
* reimplemented as AviSynth C plugin - this fixed several crashes and
  got rid of the major pain in the ass that is MSVC
* binary: built with patched Fontconfig (no longer needs fonts.conf)

### 0.11 TheFluff
* Source code (under MIT license, binaries are under GPL for obvious reasons): assrender_0.11-src.7z
