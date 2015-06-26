TARGET=paper
DBTARGET=paper-db

GNUPLOT=gnuplot

LATEX=pdflatex
BIBTEX=bibtex
TEXFILES = ${wildcard *.tex} ${wildcard tables/*.tex}
BIBFILES = ${wildcard *.bib} ${wildcard bibtex/*.bib}
PANDOC=pandoc

FIGFILES = $(wildcard figs/*.pdf)

DOTFILES = ${wildcard figs/*.dot}
FIGFILES +=${DOTFILES:%.dot=%.ps}
FIGFILES +=${wildcard figs/*.tex} ${wildcard figs/*.ps}
CONFFILES += ${wildcard *.sty} ${wildcard *.cls}

.PRECIOUS: %.ps %.pdf

.PHONY: $(TARGET) clean clean-full cpdrop

$(TARGET): $(TARGET).pdf 

cpdrop: $(TARGET).pdf
	cp $(TARGET).pdf ~/Dropbox/research/pdfsmake/$(DBTARGET).pdf


$(TARGET).pdf: $(TEXFILES) $(FIGFILES) $(BIBFILES) $(CONFFILES) \
  $(wildcard figs/*.tbl)  $(DOTFILES)
	-rm -f *.aux
	$(LATEX) $(TARGET).tex
	$(BIBTEX) $(TARGET)
	$(LATEX) $(TARGET).tex
	$(LATEX) $(TARGET).tex
	@/bin/echo ""
	@/bin/echo "++++++++++++++++++++++++++++++++++++++++++++++++++++++++++"
	@/bin/echo "               ++++ ANY UNDEFINED REFERENCES ++++"
	-@grep -i undef $(TARGET).log || echo "No undefined references."
	@/bin/echo "                 ++++ ANY EMPTY REFERENCES ++++"
	-@egrep -i -n -e 'cite{ *}' -e 'ref{ *}' $(TEXFILES) $(FIGFILES) || echo "No empty references."
	@/bin/echo "                 ++++ TODO ++++"
	-@egrep -n -e 'TODO' $(TEXFILES) $(FIGFILES) || echo "No TODOs."
	@/bin/echo "++++++++++++++++++++++++++++++++++++++++++++++++++++++++++"
	@/bin/echo ""

$(TARGET).txt: $(TEXFILES) $(FIGFILES) $(BIBFILES) $(CONFILES) \
  $(wildcard figs/*.tbl)  $(DOTFILES) 
	$(PANDOC) -o $(TARGET).txt $(TARGET).tex

clean:
	rm -f $(TARGET).pdf $(TARGET).out $(TARGET).aux $(TARGET).bbl $(TARGET).dvi $(TARGET).lof $(TARGET).log $(TARGET).toc $(TARGET).lot $(TARGET).blg $(TARGET)._paper.pdf

%.pdf: %.dvi
	dvipdfmx -o $@ $< 

gen_figs:
	$(GNUPLOT) scripts/apache.plt > figs/apache.pdf.tmp
	pdfcrop figs/apache.pdf.tmp figs/apache.pdf
	$(GNUPLOT) scripts/ssh.plt > figs/ssh.pdf.tmp
	pdfcrop figs/ssh.pdf.tmp figs/ssh.pdf
	$(GNUPLOT) scripts/lmbench.plt > figs/lmbench.pdf.tmp
	pdfcrop figs/lmbench.pdf.tmp figs/lmbench.pdf
	rm figs/{apache,ssh,lmbench}.pdf.tmp
