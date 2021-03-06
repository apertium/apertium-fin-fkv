###############################################################################
## Makefile for fin-fkv
###############################################################################

SUBDIRS=docs

LANG1=fin
LANG2=fkv
PREFIX1=$(LANG1)-$(LANG2)
PREFIX2=$(LANG2)-$(LANG1)

BASENAME=apertium-$(PREFIX1)

TARGETS_COMMON = \
	.deps/punctuation.LR.hfst \
	.deps/punctuation.RL.hfst \
	$(PREFIX1).autobil.bin \
	$(PREFIX1).autogen.hfst \
	$(PREFIX1).autolex.bin \
	$(PREFIX1).automorf.hfst \
	$(PREFIX1).automorf-untrimmed.hfst \
	$(PREFIX1).rlx.bin \
	$(PREFIX1).t1x.bin \
	$(PREFIX1).t2x.bin \
	$(PREFIX1).t3x.bin \
	$(PREFIX2).autobil.bin \
	$(PREFIX2).autogen.hfst \
	$(PREFIX2).autolex.bin \
	$(PREFIX2).automorf.hfst \
	$(PREFIX2).automorf-untrimmed.hfst \
	$(PREFIX2).rlx.bin \
	$(PREFIX2).t1x.bin \
	$(PREFIX2).t2x.bin \
	$(PREFIX2).t3x.bin


# This include defines goals for install-modes, .deps/.d and .mode files:
@ap_include@

.deps/$(PREFIX1).autobil.prefixes: $(PREFIX1).autobil.bin .deps/.d
	lt-print $< | sed 's/ /@_SPACE_@/g' > .deps/$(PREFIX1).autobil.att
	hfst-txt2fst -e ε <  .deps/$(PREFIX1).autobil.att > .deps/$(PREFIX1).autobil.hfst
	hfst-project -p upper .deps/$(PREFIX1).autobil.hfst > .deps/$(PREFIX1).autobil.upper
	echo ' [ ? - %+ ]* ' | hfst-regexp2fst > .deps/any-nonplus.hfst
	hfst-concatenate -1 .deps/$(PREFIX1).autobil.upper -2 .deps/any-nonplus.hfst -o .deps/$(PREFIX1).autobil.nonplussed
	echo ' %+ ' | hfst-regexp2fst > .deps/single-plus.hfst
	hfst-concatenate -1 .deps/single-plus.hfst -2 .deps/$(PREFIX1).autobil.nonplussed -o .deps/$(PREFIX1).autobil.postplus
	hfst-repeat -f0 -t1 -i .deps/$(PREFIX1).autobil.postplus -o .deps/$(PREFIX1).autobil.postplus.0,1
	hfst-concatenate -1 .deps/$(PREFIX1).autobil.nonplussed -2 .deps/$(PREFIX1).autobil.postplus.0,1 -o $@

.deps/$(PREFIX2).autobil.prefixes: $(PREFIX2).autobil.bin .deps/.d
	lt-print $< | sed 's/ /@_SPACE_@/g' > .deps/$(PREFIX2).autobil.att
	hfst-txt2fst -e ε <  .deps/$(PREFIX2).autobil.att > .deps/$(PREFIX2).autobil.hfst
	hfst-project -p upper .deps/$(PREFIX2).autobil.hfst > .deps/$(PREFIX2).autobil.upper
	echo ' [ ? - %+ ]* ' | hfst-regexp2fst > .deps/any-nonplus.hfst
	hfst-concatenate -1 .deps/$(PREFIX2).autobil.upper -2 .deps/any-nonplus.hfst -o .deps/$(PREFIX2).autobil.nonplussed
	echo ' %+ ' | hfst-regexp2fst > .deps/single-plus.hfst
	hfst-concatenate -1 .deps/single-plus.hfst -2 .deps/$(PREFIX2).autobil.nonplussed -o .deps/$(PREFIX2).autobil.postplus
	hfst-repeat -f0 -t1 -i .deps/$(PREFIX2).autobil.postplus -o .deps/$(PREFIX2).autobil.postplus.0,1
	hfst-concatenate -1 .deps/$(PREFIX2).autobil.nonplussed -2 .deps/$(PREFIX2).autobil.postplus.0,1 -o $@

###############################################################################
## Finnish→Kven monolingual transducers
###############################################################################

# punctuation
# This should be removed when punctuation is fixed upstream
.deps/punctuation.LR.hfst: .deps/.d
	hfst-lexc dev/punctuation.lexc | hfst-invert -o $@

.deps/punctuation.RL.hfst: .deps/.d
	hfst-lexc dev/punctuation.lexc -o $@

# fin->fkv mor
# apertium-fin-fkv.fin-fkv.LR.att.gz
.deps/$(LANG1).automorf-missingpunct.hfst: $(AP_SRC1)/apertium-und.$(LANG1)-und.LR.att.gz .deps/.d
	$(ZCAT) $< | hfst-txt2fst > $@

.deps/$(LANG1).automorf.hfst: .deps/$(LANG1).automorf-missingpunct.hfst .deps/punctuation.LR.hfst
	hfst-union -1 $< -2 .deps/punctuation.LR.hfst -o $@

.deps/$(PREFIX1).automorf-trimmed.hfst: .deps/$(LANG1).automorf.hfst .deps/$(PREFIX1).autobil.prefixes
	hfst-compose-intersect -1 $< -2 .deps/$(PREFIX1).autobil.prefixes -o $@

$(PREFIX1).automorf.hfst: .deps/$(PREFIX1).automorf-trimmed.hfst
	hfst-fst2fst -w -i $< -o $@

$(PREFIX1).automorf-untrimmed.hfst: .deps/$(LANG1).automorf.hfst
	hfst-fst2fst -w -i $< -o $@

# fkv->fin mor
# apertium-fin-fkv.fkv-fin.LR.att.gz
# TODO: Make this use .und as well (but giella-fkv doesn't install any .und yet)
#$(PREFIX2).automorf.hfst: $(AP_SRC2)/$(BASENAME).$(LANG1).LR.att.gz .deps/$(PREFIX2).autobil.prefixes .deps/punctuation.LR.hfst .deps/.d
.deps/$(PREFIX2).automorf.untrimmed.hfst: $(AP_SRC2)/apertium-und.fkv-und.LR.att.gz .deps/$(PREFIX2).autobil.prefixes .deps/punctuation.LR.hfst .deps/.d
	$(ZCAT) $(AP_SRC2)/apertium-und.fkv-und.LR.att.gz | hfst-txt2fst | hfst-union -2 .deps/punctuation.LR.hfst -o $@

$(PREFIX2).automorf.hfst: .deps/$(PREFIX2).automorf.untrimmed.hfst
	hfst-compose-intersect -1 .deps/$(PREFIX2).automorf.untrimmed.hfst -2 .deps/$(PREFIX2).autobil.prefixes -o .deps/$(PREFIX2).automorf.hfst
	hfst-fst2fst -O .deps/$(PREFIX2).automorf.hfst -o $@

$(PREFIX2).automorf-untrimmed.hfst: .deps/$(PREFIX2).automorf.untrimmed.hfst
	hfst-fst2fst -w -i $< -o $@

# fin->fkv gen
# apertium-fkv.fkv.RL.att.gz
.deps/$(PREFIX1).autogen.hfst: $(AP_SRC2)/apertium-$(LANG2).$(LANG2).RL.att.gz .deps/punctuation.RL.hfst .deps/.d
	$(ZCAT) $< | hfst-txt2fst | hfst-union -2 .deps/punctuation.RL.hfst -o $@

$(PREFIX1).autogen.hfst: .deps/$(PREFIX1).autogen.hfst
	hfst-fst2fst -O $< -o $@

# fkv->fin gen
# apertium-fin.fin.RL.att.gz
.deps/$(PREFIX2).autogen.hfst: $(AP_SRC1)/apertium-$(LANG1).$(LANG1).RL.att.gz .deps/punctuation.RL.hfst .deps/.d
	$(ZCAT) $< | hfst-txt2fst | hfst-union -2 .deps/punctuation.RL.hfst -o $@

$(PREFIX2).autogen.hfst: .deps/$(PREFIX2).autogen.hfst
	hfst-fst2fst -O $< -o $@


###############################################################################
## Prob files for tagger
###############################################################################

#$(PREFIX1).prob: $(AP_SRC1)/$(LANG1).prob
#	cp $< $@

###############################################################################
## Bilingual dictionaries
###############################################################################

$(PREFIX1).autobil.bin: $(BASENAME).$(PREFIX1).dix
	apertium-validate-dictionary $<
	lt-comp lr $< $@

$(PREFIX2).autobil.bin: $(BASENAME).$(PREFIX1).dix
	apertium-validate-dictionary $<
	lt-comp rl $< $@

###############################################################################
## Disambiguation rules
###############################################################################

$(PREFIX1).rlx.bin: $(AP_SRC1)/$(LANG1).mor.rlx.bin
	cp $< $@

$(PREFIX2).rlx.bin: $(AP_SRC2)/$(LANG2).mor.rlx.bin
	cp $< $@


###############################################################################
## Lexical selection rules
###############################################################################

$(PREFIX1).autolex.bin: $(BASENAME).$(PREFIX1).lrx
	lrx-comp $< $@

$(PREFIX2).autolex.bin: $(BASENAME).$(PREFIX2).lrx
	lrx-comp $< $@

###############################################################################
## Structural transfer rules
###############################################################################

$(PREFIX1).t1x.bin: $(BASENAME).$(PREFIX1).t1x
	apertium-validate-transfer $(BASENAME).$(PREFIX1).t1x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX1).t1x $@

$(PREFIX1).t2x.bin: $(BASENAME).$(PREFIX1).t2x
	apertium-validate-interchunk $(BASENAME).$(PREFIX1).t2x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX1).t2x $@

$(PREFIX1).t3x.bin: $(BASENAME).$(PREFIX1).t3x
	apertium-validate-postchunk $(BASENAME).$(PREFIX1).t3x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX1).t3x $@

$(PREFIX2).t1x.bin: $(BASENAME).$(PREFIX2).t1x
	apertium-validate-transfer $(BASENAME).$(PREFIX2).t1x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX2).t1x $@

$(PREFIX2).t2x.bin: $(BASENAME).$(PREFIX2).t2x
	apertium-validate-interchunk $(BASENAME).$(PREFIX2).t2x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX2).t2x $@

$(PREFIX2).t3x.bin: $(BASENAME).$(PREFIX2).t3x
	apertium-validate-postchunk $(BASENAME).$(PREFIX2).t3x
	apertium-preprocess-transfer $(BASENAME).$(PREFIX2).t3x $@


###############################################################################
## Postgeneration dictionaries
###############################################################################

#$(PREFIX1).autopgen.bin: $(BASENAME).post-$(LANG1).dix
#	apertium-validate-dictionary $<
#	lt-comp lr $< $@
#
#$(PREFIX2).autopgen.bin: $(BASENAME).post-$(LANG2).dix
#	apertium-validate-dictionary $<
#	lt-comp lr $< $@

###############################################################################
## Distribution
###############################################################################

# Files which are distributed, but not built:
EXTRA_DIST=$(PREFIX1).prob \
	   $(PREFIX2).prob \
	   $(BASENAME).$(PREFIX1).t1x \
	   $(BASENAME).$(PREFIX1).t2x \
	   $(BASENAME).$(PREFIX1).t3x \
	   $(BASENAME).$(PREFIX2).t1x \
	   $(BASENAME).$(PREFIX2).t2x \
	   $(BASENAME).$(PREFIX2).t3x \
	   modes.xml


###############################################################################
## Installation
###############################################################################

apertium_fin_fkvdir=$(prefix)/share/apertium/$(BASENAME)/

# Files which should be installed to $(prefix)/share/apertium/$(BASENAME)/:
apertium_fin_fkv_DATA=$(TARGETS_COMMON) \
	$(BASENAME).$(PREFIX1).t1x \
	$(BASENAME).$(PREFIX1).t2x \
	$(BASENAME).$(PREFIX1).t3x \
	$(BASENAME).$(PREFIX2).t1x \
	$(BASENAME).$(PREFIX2).t2x \
	$(BASENAME).$(PREFIX2).t3x


# All modes are created by the same goal, listing several will just give problems with make -j2
noinst_DATA=modes/$(PREFIX1).mode

install-data-local: install-modes


###############################################################################
## Cleanup
###############################################################################

CLEANFILES = $(TARGETS_COMMON)
clean-local:
	-rm -rf .deps modes


###############################################################################
## Test
###############################################################################

test: all
	apertium-regtest test
