include $(abs_top_builddir)/Makefile.global

# This file assume the container folder contains the following files:
# README : A file saying (at least) what revision we have captive
# csrc : The actual source we'll contingure and build
# incarcerate : A wrapper script that configures the captive package
#             : NOTE that captive packages must support VPATH builds
#             : or the captive_configure needs to force that.

# This is the main magic.  An "all" build MUST install; otherwise
# successive builds that depend on this captive project will fail.
# But, we install into a fake DESTDIR, and the container for this
# make file will then copy the fake install to the real install
# later when we're asked to install for real.
# This has the downside that every make does a captive install, but
# them's the breaks.

# libvpx doesn't follow gnu configure standards and we
# have to rely on the cross-compile prefix, in particular for windows
# we still allow flags to be overriden though
unexport CC
unexport LD
unexport CPP
unexport CXX
unexport NM
unexport AS
unexport STRIP
unexport RANLIB
unexport AR
unexport DLLTOOL

all-local: incarcerate-package
	@echo "Incarcerating package $(srcdir) to fake DESTDIR=$(vs_captive_prefix)"
	@cd csrc && \
	  ( $(MAKE) -q all || \
	    (echo "Out of date files; re-running make" && $(MAKE) all && \
	     $(MAKE) DESTDIR="$(vs_captive_prefix)" install) \
	)
	@echo "Incarceration complete: $(srcdir)"

dist-hook:
	cp -r $(srcdir)/csrc $(distdir)/csrc
	rm -rf `find $(distdir)/csrc -name '.svn'`
	
# vpx doesn't have a separate exec and data target, so
# we just make exec do both
install-exec-hook: incarcerate-package
	cd csrc && $(MAKE) DESTDIR="$(DESTDIR)" install
	if test -f install.sh; then \
	  DESTDIR="$(DESTDIR)" sh install.sh; \
	fi

uninstall-local: incarcerate-package
	if test -f uninstall.sh; then \
	  DESTDIR="$(DESTDIR)" sh uninstall.sh; \
	fi
	cd csrc && $(MAKE) DESTDIR="$(DESTDIR)" uninstall

clean-local: incarcerate-package
	if test -f uninstall.sh; then \
	  DESTDIR="$(vs_captive_prefix)" sh uninstall.sh; \
	fi
	cd csrc && $(MAKE) clean && $(MAKE) DESTDIR="$(vs_captive_prefix)" uninstall

distclean-local:
	rm -rf csrc
