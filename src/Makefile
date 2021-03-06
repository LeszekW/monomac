#
# Figure out if I want to use [New] or remove from contract
#
WARNINGS_I_SHOULD_FIX=114,108

#
# CORE_SOURCES are source files that are written by hand
# and are linked with the generator
#

CORE_SOURCES =					\
	Constants.cs					\
	./AppKit/Enums.cs				\
	./CoreImage/Enums.cs				\
	./Foundation/EnumDesktop.cs			\
	./Foundation/NSObject.cs			\
	./ObjCRuntime/Class.cs				\
	./ObjCRuntime/Messaging.cs			\
	./ObjCRuntime/NativeConstructorBuilder.cs	\
	./ObjCRuntime/NativeImplementationBuilder.cs	\
	./ObjCRuntime/NativeMethodBuilder.cs		\
	./ObjCRuntime/MethodDescription.cs		\
	./ObjCRuntime/NSObjectMarshaler.cs		\
	./ObjCRuntime/SelectorMarshaler.cs		\
	./ObjCRuntime/Runtime.cs			\
	./ObjCRuntime/Selector.cs			\
	./PdfKit/Enums.cs				\
	./QTKit/Defs.cs					\
	./QTKit/Enums.cs				\
	./WebKit/Enums.cs

# Sources that are not part of CORE_SOURCES or the generated templates
MONOMAC_SOURCES = \
	./Foundation/NSObjectMac.cs			\
	./AppKit/ActionDispatcher.cs			\
	./AppKit/BeginSheet.cs				\
	./AppKit/DoubleWrapper.cs			\
	./AppKit/Functions.cs				\
	./AppKit/NSActionCell.cs			\
	./AppKit/NSApplication.cs			\
	./AppKit/NSApplicationDelegate.cs		\
	./AppKit/NSBitmapImageRep.cs			\
	./AppKit/NSButton.cs				\
	./AppKit/NSBezierPath.cs			\
	./AppKit/NSColor.cs				\
	./AppKit/NSControl.cs				\
	./AppKit/NSGradient.cs				\
	./AppKit/NSGraphics.cs				\
	./AppKit/NSLevelIndicator.cs			\
	./AppKit/NSMatrix.cs				\
	./AppKit/NSMenu.cs				\
	./AppKit/NSMenuItem.cs				\
	./AppKit/NSOpenGLPixelFormat.cs			\
	./AppKit/NSPopUpButton.cs			\
	./AppKit/NSPopUpButtonCell.cs			\
	./AppKit/NSSegmentedControl.cs			\
	./AppKit/NSTableView.cs				\
	./AppKit/NSWindow.cs				\
	./CoreImage/CIContext.cs			\
	./CoreImage/CISampler.cs			\
	./CoreImage/CIVector.cs				\
	./Growl/Constants.cs				\
	./QTKit/QTCaptureDevice.cs			\
	./QTKit/QTMovie.cs				\
	./WebKit/DomNode.cs				\
	./WebKit/Enumerators.cs				\
	./WebKit/Indexers.cs				\
	./WebKit/WebKit.cs				\
	./WebKit/WebView.cs

GENERATOR_SOURCES = \
	../../maccore/src/generator.cs			\
	../../maccore/src/Options.cs			\
	../../maccore/src/btouch.cs

SOURCES = \
	$(CORE_SOURCES)

DEFINES = -define:NET_2_0

TARGETS = _bmac.exe MonoMac.dll  parse.exe bmac.exe

include Make.shared

SHARED_API = \
	./foundation.cs		\
	./coreanimation.cs	\
	./coredata.cs		\
	./corelocation.cs

SHARED_SOURCES = $(patsubst %,../../maccore/src/%,$(SHARED_SOURCE))
SHARED_CORE_SOURCES = $(patsubst %,../../maccore/src/%,$(SHARED_CORE_SOURCE))
SHARED_APIS = $(patsubst %,../../maccore/src/%,$(SHARED_API))

APIS = \
	appkit.cs		\
	coreimage.cs		\
	corewlan.cs		\
	foundation-desktop.cs 	\
	growl.cs		\
	qtkit.cs		\
	pdfkit.cs		\
	webkit.cs		\
	composer.cs		\
	$(SHARED_APIS)

all: $(TARGETS)

parse.exe: parse.cs
	gmcs parse.cs

core.dll: $(CORE_SOURCES) $(SHARED_CORE_SOURCES) Makefile
	gmcs -target:library -debug -unsafe -out:core.dll -define:MONOMAC,MONOMAC_BOOTSTRAP,COREBUILD $(CORE_SOURCES) $(SHARED_CORE_SOURCES) -r:System.Drawing

_bmac.exe: $(APIS) core.dll $(GENERATOR_SOURCES)
	gmcs -unsafe -out:_bmac.exe -debug $(DEFINES) -define:GENERATOR,MONOMAC,MONOMAC_BOOTSTRAP $(GENERATOR_SOURCES) -r:core.dll

generated_sources: _bmac.exe $(APIS) 
	mono --debug _bmac.exe -a -d=MONOMAC --core --sourceonly=generated_sources -v -tmpdir=. $(APIS) --baselib=core.dll --ns=MonoMac.ObjCRuntime -r=System.Drawing

bmac.exe: MonoMac.dll $(GENERATOR_SOURCES)
	gmcs -unsafe -out:bmac.exe -debug $(DEFINES) -define:GENERATOR,MONOMAC,MONOMAC_BOOTSTRAP $(GENERATOR_SOURCES) -r:MonoMac.dll

count: 
	make pass | wc -l

counts:
	make pass | grep 'could not be' | sed 's/.*`//' | sed "s/'.*//" | sort | uniq -c | sort -n

MonoMac.dll: $(SOURCES) generated_sources $(MONOMAC_SOURCES) $(SHARED_SOURCES) $(SHARED_CORE_SOURCES)
	gmcs -define:OBJECT_REF_TRACKING -nowarn:0414,$(WARNINGS_I_SHOULD_FIX) -debug -unsafe $(SOURCES) @generated_sources $(MONOMAC_SOURCES) $(SHARED_SOURCES) $(SHARED_CORE_SOURCES) -target:library -define:MONOMAC -out:MonoMac.dll -r:System.Drawing

clean:
	rm -f $(TARGETS) core.dll

all: MonoMac.dll

update: MonoMac.dll
	cp monomac.dll ~/.config/MonoDevelop/addins/MonoDevelop.MonoMac.2.4.0.*/MonoMac.dll

push-to-monodevelop:
	cp MonoMac.dll* /cvs/monodevelop/extras/MonoDevelop.MonoMac/MonoDevelop.MonoMac
