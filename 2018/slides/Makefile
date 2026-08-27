# -*- tab-width: 4 -*- ;; Emacs
# vi: set ts=4 sw=4 noet :: Vi/ViM
############################################################ IDENT(1)
#
# $Title: Makefile for compiling `.latex' presentation into `.pdf' $
#
############################################################ CONFIGURATION

#NOCLEAN = yes

NAME = bsdcan2018
DEPS = dot/committers-ports-dteske.dot \
       dot/committers-src-dteske.dot \
       dot/dtrace_probes.dot \
       dot/dtrace_probes_by_prov.dot \
       dot/dtrace_words.dot \
       dot/dwatch_base_profiles.fdp \
       dot/dwatch_io_profiles.circo \
       dot/dwatch_networking_profiles.fdp \
       dot/dwatch_ports_profiles.fdp \
       dot/dwatch_proc_profiles.twopi \
       dot/dwatch_sched_profiles.fdp \
       dot/dwatch_syscall_profiles.sfdp \
       dot/dwatch_vfs_profiles.sfdp \
       graphics/bsdconfig.png \
       graphics/bsdinstall-distextract.png \
       graphics/bsdinstall-zfsboot.png \
       graphics/dtrace_probe_venn1.png \
       graphics/dtrace_probe_venn2.png \
       graphics/dtrace_probe_venn3.png \
       graphics/dtrace_probe_venn4.png \
       graphics/dtracepony.png \
       graphics/flamegraph.png \
       graphics/freebsd-boot-dteske.png \
       graphics/freebsd9-diehard.png \
       graphics/gandi-boot.png \
       graphics/gwatch_syscall.png \
       graphics/jvjasD7v_400x400.jpg \
       graphics/logo.pdf \
       graphics/mouse_trap.jpg \
       graphics/peaches-11.svg \
       graphs/dtrace_timeline.pdf

############################################################ GLOBALS

JOBNAME  = pdflatex
PDFLATEX = pdflatex -halt-on-error -jobname $(JOBNAME)

############################################################ FUNCTIONS

EVAL2 = eval2() { [ $$\# -eq 0 ] && return; echo "$$@"; eval "$$@"; }
EVAL3 = eval3() { [ $$\# -eq 0 ] && return; echo "$$@" >&3; eval "$$@"; }

CONVERT2PDF = @$(EVAL2); $(EVAL3); convert2pdf() { \
	local OPTIND=1 OPTARG flag depend= target=; \
	while getopts d:t: flag; do \
		case "$$flag" in \
		d) depend="$$OPTARG" ;; \
		t) target="$$OPTARG" ;; \
		*) return 1; \
		esac; \
	done; \
	shift $$(( $$OPTIND - 1 )); \
	local file remake=; \
	if [ "$$target" -a -e "$$target" ]; then \
		for file in "$$@"; do \
			: if even one file is newer than target, remake; \
			[ "$$target" -nt "$$file" ] && continue; \
			remake=1; \
			break; \
		done; \
	elif [ "$$target" ]; then \
		remake=1; \
	fi; \
	if [ "$$depend" ]; then \
		for file in "$$@"; do \
			: if even one dependency is newer than file, remake; \
			[ "$$file" -nt "$$depend" ] && continue; \
			remake=1; \
			break; \
		done; \
	fi; \
	[ ! "$$remake" ] && return; \
	local output touch=; \
	local dep deps=; \
	for dep in $(DEPS); do \
		deps="$$deps $${dep%.*}.pdf"; \
	done; \
	deps="$${deps\# }"; \
	exec 3<&1; \
	for file in "$$@"; do \
		output="$${file%.*}.pdf"; \
		[ -e "$$output" -a "$$output" -nt "$$file" ] && continue; \
		case "$$file" in \
		*.[Pp][Dd][Ff]) continue ;; \
		*.[Ll][Aa][Tt][Ee][Xx]) \
			err=`eval3 $(PDFLATEX) "$$file" $$deps 2>&1` || { \
				echo "$$err" | awk ' \
				function color(code) { return sprintf("%c[%sm", 27, code) } \
				gsub(/Warning/, color("33") "&" color(0)){}; \
				gsub(/Error/, color("31;1") "&" color(0)){}; \
				1; \
				END { exit 1 } \
				'; } || return; \
			eval2 mv -f "$(JOBNAME).pdf" "$$output" ;; \
		*.[Dd][Oo][Tt]) eval2 dot -Tpdf -o "$$output" "$$file" || return ;; \
		*.[Tt][Ww][Oo][Pp][Ii]) \
			eval2 twopi -Tpdf -o "$$output" "$$file" || return ;; \
		*.[Nn][Ee][Aa][Tt][Oo]) \
			eval2 neato -Tpdf -o "$$output" "$$file" || return ;; \
		*.[Cc][Ii][Rr][Cc][Oo]) \
			eval2 circo -Tpdf -o "$$output" "$$file" || return ;; \
		*.[Ff][Dd][Pp]) eval2 fdp -Tpdf -o "$$output" "$$file" || return ;; \
		*.[Ss][Ff][Dd][Pp]) \
			eval2 sfdp -Tpdf -o "$$output" "$$file" || : ;; \
		*) eval2 convert "$$file" "$$output" || return; \
		esac; \
		touch=1; \
	done; \
	[ ! "$$depend" -o ! "$$touch" ] || eval2 touch "$$depend"; \
}; convert2pdf

CLEANJOB = @$(EVAL2); cleanjob() { \
	local name="$$1" ext files=; \
    [ ! "$$name" ] && return; \
	for ext in aux log nav out pdf snm toc vrb; do \
		files="$$files $$name.$$ext"; \
	done; \
	eval2 rm -f $$files; \
}; cleanjob

CLEANDEP = @$(EVAL2); cleandep() { \
	local file files=; \
	for file in "$$@"; do \
		case "$$file" in \
		*.[Pp][Dd][Ff]|*.[Ll][Aa][Tt][Ee][Xx]|*.[Dd][Oo][Tt]) continue ;; \
		*.[Ff][Dd][Pp]|*.[Cc][Ii][Rr][Cc][Oo]|*.[Ss][Ff][Dd][Pp]) continue ;; \
		*.[Tt][Ww][Oo][Pp][Ii]|*.[Nn][Ee][Aa][Tt][Oo]) continue ;; \
		esac; \
		files="$$files $${file%.*}.pdf"; \
	done; \
	[ ! "$$files" ] && return; \
	eval2 rm -f $$files; \
}; cleandep

############################################################ TARGETS

.PHONY: all clean distclean

all: $(NAME).pdf

$(NAME).pdf: Makefile $(DEPS) $(NAME).latex
	$(CONVERT2PDF) -d$(NAME).latex -t$(@) $(DEPS) $(NAME).latex
	$(CLEANJOB); [ "$(NOCLEAN)" ] || cleanjob $(JOBNAME)
	$(CLEANDEP); [ "$(NOCLEAN)" ] || cleandep $(DEPS)

clean: 
	$(CLEANJOB) $(JOBNAME)
	rm -f missfont.log
	$(CLEANDEP) $(DEPS)

distclean: clean
	rm -f $(NAME).pdf

################################################################################
# END
################################################################################
