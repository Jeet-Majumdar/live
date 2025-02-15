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
        <textarea id="latex_input" name="latex_input" rows="3" cols="40" 
            placeholder"Type LaTeX here"></textarea>
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