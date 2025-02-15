---
layout: post
title: Latex equations to MS-Office - Word & Powerpoint
categories: [Tech]
---


<p>
    Type your LaTeX code below:
</p>
<div id="controls">
    <div id="input_area">
        <span class="delimiter">$$</span>
        <textarea id="latex_input" name="latex_input" rows="3" cols="40" placeholder="Type LaTeX here"></textarea>
        <span class="delimiter">$$</span>
    </div>
    <div id="buttons">
        <div class="button-column">
        <b>MathML functions</b>
        <button onclick="process_mathml(false);">Preview</button>
        <button onclick="process_mathml(true);">Show code</button>
        <button onclick="download_mathml();">Save as file</button>
        </div>
        <div class="button-column">
        <b>SVG functions</b>
        <button onclick="process_svg(false);">Preview</button>
        <button onclick="process_svg(true);">Show code</button>
        <button onclick="download_svg();">Save as file</button>
        </div>
        <div class="button-column">
        <b>Reset</b>
        <button onclick="clear_input();">Clear input</button>
        <button onclick="clear_output();">Clear output</button>
        </div>
    </div>
</div>
<script>
//==============================================================================
//
//   (C) Copyright by Rafael M. Siejakowski, 2020-2021.
//
//   This file is licensed under the BSD 3-Clause License.
//   You may not use this file except in compliance with the license.
//   A copy of the license can be obtained from
//   https://opensource.org/licenses/BSD-3-Clause
//
//==============================================================================
// Global variable for the LaTeX converter
let display_mode = true;

function is_valid_DOM(element)
{
	if (!element)
	{
		console.error("Couldn't obtain a handle for the DOM element");
		return false;
	}
	else return true;
}

// Display the single or double dollars depending on the mode set
function update_display_mode()
{
	let inlineselector = document.getElementById('inlinemath');
	if (!is_valid_DOM(inlineselector))
		return;
	display_mode = !(inlineselector.checked);
	delimiters = document.getElementsByClassName('delimiter');
	for (i=0; i<delimiters.length; i++)
	{
		delimiters.item(i).innerHTML = (display_mode)? '$$' : '$';
	}
}

// Shows the in-browser preview
function preview(result, format_name)
{
	clear_output();
	let preview_area = document.getElementById('preview_area');
	if (!is_valid_DOM(preview_area))
		return;
	preview_area.innerHTML = result;
	let preview_container = document.getElementById('preview_container');
	if (!is_valid_DOM(preview_container))
		return;
	preview_container.style.visibility = 'visible';
	preview_container.style.display = 'block';
	// Put the correct word: 'MathML' or 'SVG' in the text.
	let outformat = document.getElementsByClassName('output_format');
	if (!is_valid_DOM(outformat))
		return;
	for (i=0; i<outformat.length; i++)
	{
		outformat.item(i).innerHTML = format_name;
	}
	preview_area.scrollIntoView();
}

// Displays the markup code
function show_code(result, format_name)
{
	let source_container = document.getElementById('source_container');
	if (!is_valid_DOM(source_container))
		return;
	source_container.style.visibility = 'visible';
	source_container.style.display = 'block';
	let source_area = document.getElementById('source_area');
	if (!is_valid_DOM(source_area))
		return;
	source_area.value = result;
	source_area.focus();
	source_area.select();
	source_area.scrollIntoView();
}

// Clears input
function clear_input()
{
	let input = document.getElementById('latex_input');
	if (!is_valid_DOM(input))
		return;
	input.value = '';
}

// Clears and hides output
function clear_output()
{
	let preview_container = document.getElementById('preview_container');
	if (!is_valid_DOM(preview_container))
		return;
	preview_container.style.visibility = 'collapse';
	preview_container.style.display = 'none';
	let preview_area = document.getElementById('preview_area');
	if (!is_valid_DOM(preview_area))
		return;
	preview_area.innerHTML = '';
	let source_container = document.getElementById('source_container');
	if (!is_valid_DOM(source_container))
		return;
	source_container.style.visibility = 'collapse';
	source_container.style.display = 'none';
	let source_area = document.getElementById('source_area');
	if (!is_valid_DOM(source_area))
		return;
	source_area.value = '';
}

// Main function for running the conversion to MathML
function process_mathml(show_src)
{
	let input = document.getElementById('latex_input')
	if (!is_valid_DOM(input))
		return;
	let latex = input.value;
	let output = window.MathJax.tex2mml(latex, {display: display_mode});
	preview(output, 'MathML');
	if (show_src)
	{
		show_code(output, 'MathML');
	}
}

// Main function for running the conversion to SVG
function process_svg(show_src)
{
	let input = document.getElementById('latex_input')
	if (!is_valid_DOM(input))
		return;
	let latex = input.value;
	let output = window.MathJax.tex2svg(latex, {display: display_mode}).innerHTML;
	preview(output, 'SVG');
	if (show_src)
	{
		show_code(output, 'SVG');
	}
}

function convert_to_mm(SVG_len)
{
	SVG_len.convertToSpecifiedUnits(SVG_len.SVG_LENGTHTYPE_MM);
	let dim = SVG_len.valueInSpecifiedUnits * 10.0; // Upscale 10 times
	SVG_len.newValueSpecifiedUnits(SVG_len.SVG_LENGTHTYPE_MM, dim);
}

function convert_to_absolute_units(svg_markup)
{
	let dummy = document.createElement("div");
	dummy.innerHTML = svg_markup;
	if (!dummy.hasChildNodes()) 
	{
		return svg_markup;
	}
	let SVG_root = dummy.firstChild;
	convert_to_mm(SVG_root.width.baseVal);
	convert_to_mm(SVG_root.height.baseVal);
	return dummy.innerHTML;
}

// Main function for downloading an SVG file
function download_svg()
{
	let input = document.getElementById('latex_input');
	if (!is_valid_DOM(input))
		return;
	let latex = input.value;
	let output_code = window.MathJax.tex2svg(latex, {display: display_mode}).innerHTML;
	const xml_prolog = '<?xml version="1.0" encoding="UTF-8" standalone="no"?>' + "\n";
	let encoded = window.btoa(xml_prolog + convert_to_absolute_units(output_code));
	let anchor = document.createElement("a");
	anchor.href = "data:image/svg+xml;base64," + encoded;
	anchor.download = "formula.svg";
	anchor.type = "image/svg+xml";
	anchor.title = "Download as SVG file";
	anchor.innerHTML = "Download as SVG file";
	anchor.click();
}

// Main function ofr downloading a MathML file
function download_mathml()
{
	let input = document.getElementById('latex_input')
	if (!is_valid_DOM(input))
		return;
	let latex = input.value;
	let output_code = window.MathJax.tex2mml(latex, {display: display_mode});
	let xml_prolog = '<?xml version="1.0" encoding="UTF-8" standalone="no"?>' + "\n";
	let encoded = window.btoa(xml_prolog + output_code);
	let anchor = document.createElement("a");
	anchor.href = "data:application/mathml+xml;base64," + encoded;
	anchor.download = "formula.xml";
	anchor.type = "application/mathml+xml";
	anchor.title = "Download as MathML file";
	anchor.innerHTML = "Download as MathML file";
	anchor.click();
}
</script>