# -*- tab-width: 4 -*- ;; Emacs
# vi: set ts=4 sw=4 noet :: Vi/ViM
############################################################ IDENT(1)
#
# $Title: Makefile for compiling `.dot' into `.pdf' $
#
############################################################ CONFIGURATION

DOTS = committers-ports-dteske.dot \
       committers-ports.dot \
       committers-src-dteske.dot \
       committers-src.dot \
       dtrace_probes.dot \
       dtrace_probes_by_prov.dot \
       dtrace_words.dot \
       dwatch_base_profiles.fdp \
       dwatch_io_profiles.circo \
       dwatch_networking_profiles.fdp \
       dwatch_ports_profiles.fdp \
       dwatch_proc_profiles.twopi \
       dwatch_sched_profiles.fdp \
       dwatch_syscall_profiles.sfdp \
       dwatch_vfs_profiles.sfdp

############################################################ FUNCTIONS

EVAL2 = eval2() { [ $$\# -eq 0 ] && return; echo "$$@"; eval "$$@"; }

DOT2PDF = @$(EVAL2); dot2pdf() { \
	local OPTIND=1 OPTARG flag target=; \
	while getopts t: flag; do \
		case "$$flag" in \
		t) target="$$OPTARG" ;; \
		*) return 1; \
		esac; \
	done; \
	shift $$(( $$OPTIND - 1 )); \
	local file output; \
	for file in "$$@"; do \
		output="$${file%.*}.pdf"; \
		case "$$file" in \
		Makefile) continue ;; \
		*.[Tt][Ww][Oo][Pp][Ii]) prog=twopi ;; \
		*.[Nn][Ee][Aa][Tt][Oo]) prog=neato ;; \
		*.[Cc][Ii][Rr][Cc][Oo]) prog=circo ;; \
		*.[Ff][Dd][Pp]) prog=fdp ;; \
		*.[Ss][Ff][Dd][Pp]) prog=sfdp ;; \
		*) prog=dot; \
		esac; \
		eval2 $$prog -Tpdf -o "$$output" "$$file" || \
			[ "$$prog" = sfdp ] || return; \
	done; \
	[ ! "$$target" ] || eval2 touch "$$target"; \
}; dot2pdf

CLEANPDF = @$(EVAL2); cleanpdf() { \
	local file files=; \
	for file in "$$@"; do \
		files="$$files $${file%.*}.pdf"; \
	done; \
	[ ! "$$files" ] && return; \
	eval2 rm -f $$files; \
}; cleanpdf

############################################################ TARGETS

.PHONY: all clean distclean

all: .all_done

.all_done: Makefile $(DOTS)
	$(DOT2PDF) -t$(@) $(?)

clean: 
	$(CLEANPDF) $(DOTS)
	rm -f .all_done

################################################################################
# END
################################################################################
