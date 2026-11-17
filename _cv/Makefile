# Builds the CV and copies the PDF to assets/files/, the only CV file the site publishes.
# Jekyll skips this _cv/ folder, so the source and template files stay unpublished.

sandhya_CV.pdf: sandhya_CV.tex *.cls *.sty
	pdflatex -interaction=nonstopmode -halt-on-error sandhya_CV.tex
	pdflatex -interaction=nonstopmode -halt-on-error sandhya_CV.tex
	cp sandhya_CV.pdf ../assets/files/sandhya_CV.pdf

# Builds a private preview in preview/ and opens it; never touches the published PDF.
preview:
	mkdir -p preview
	pdflatex -interaction=nonstopmode -halt-on-error -output-directory=preview sandhya_CV.tex
	pdflatex -interaction=nonstopmode -halt-on-error -output-directory=preview sandhya_CV.tex

clean:
	rm -f *.aux *.log *.out sandhya_CV.pdf
	rm -rf preview

.PHONY: preview clean
