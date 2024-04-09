# PDF Reader

A simple Go library which enables reading PDF files. 

This repository contains merged code from https://github.com/rsc/pdf and https://github.com/ledongthuc/pdf and https://github.com/dslipak/pdf.  
Added bugfixs and stabilize error handling.

Features
  - Get plain text content with and without font and formatting information.
  
TODO
  - include encryption for PDF V5 R6 (SHA=256)
  - review available go implementations and decide which one is to go best with:
	  - https://github.com/seehuhn/go-pdf
	  - https://github.com/EndFirstCorp/pdflib
	  - https://github.com/KarmaPenny/pdfparser
	  - https://github.com/pdfcpu/pdfcpu


## Install:

`go get -u github.com/mazeforgit/pdf`


## Read plain text

```golang
package main

import (
	"bytes"
	"fmt"
	"os"
	"strconv"

	"github.com/mazeforgit/pdf"
)

func main() {

	testFile := "./testdata/story_Word2019-2312-1712620132_SaveAs-Standard-PDFA__pdf17_trailer.pdf"
	
	// open file
	fmt.Println(". open testFile = " + testFile)
	f, err := Open(testFile)
	if err != nil {
		os.Exit(-1)
	}
	
	totalPage := f.NumPage()
	fmt.Println(". totalPage = " + strconv.Itoa(totalPage))
	
	for pageIndex := 1; pageIndex <= totalPage; pageIndex++ {
		fmt.Println(". pageIndex = "  + strconv.Itoa(pageIndex))
		var buf bytes.Buffer

		p := f.Page(pageIndex)
		if p.V.IsNull() {
			continue
		}
		
		texts := p.Content().Text
		var lastY = 0.0
		line := ""

		for _, text := range texts {
			if lastY != text.Y {
				if lastY > 0 {
					buf.WriteString(line + "\n")
					line = text.S
				} else {
					line += text.S
				}
			} else {
				line += text.S
			}

			lastY = text.Y
		}
		
		// page content
		fmt.Println(buf.String())
		fmt.Println(". buf.Len() = " + strconv.Itoa(buf.Len()))
	}
	
	// version
	fmt.Println(". version = " + f.Version())
	
	// verdicts
	verdicts = f.Verdicts()
	fmt.Println(". verdicts = " + strconv.Itoa(len(verdicts)))
	for i:=0; i< len(verdicts); i++ {
		fmt.Println(verdicts[i])
	}

	// close file
	f.Close()
}
```