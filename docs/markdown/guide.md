# Markdown
- https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

## Tools
- https://readme-typing-svg.demolab.com/demo/?duration=2000&color=FA46BB&center=true&random=true&lines=Best+regards%2C+Dennis

## Github markdown API
- Notice that github will sanitize markdown and remove a lot of stuff like the usage of inline style, style elements or script elements
- The only available size formats you can use are `px` and `%`

## Custom styles in markdown
readme.md
```md
<div align="center">
    <img src="test.svg" width="400" height="400" alt="css-in-readme">
</div>
```

example.svg
```svg
<svg fill="none" viewBox="0 0 800 400" width="800" height="400" xmlns="http://www.w3.org/2000/svg">
	<foreignObject width="100%" height="100%">
		<div xmlns="http://www.w3.org/1999/xhtml">
			<style>
				@keyframes rotate { 0% { transform: rotate(3deg);} 100% { transform: rotate(-3deg);} }
				@keyframes gradientBackground { 0% { background-position: 0% 50%; } 50% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }
				@keyframes fadeIn { 0% { opacity: 0; } 66% { opacity: 0;} 100% { opacity: 1;} }
				.container { font-family: system-ui, -apple-system, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif, 'Apple Color Emoji','Segoe UI Emoji'; display: flex; flex-direction: column; align-items: center; justify-content: center; margin: 0; width: 100%; height: 400px; background: linear-gradient(-45deg, #fc5c7d, #6a82fb, #05dfd7); background-size: 600% 400%; animation: gradientBackground 10s ease infinite; border-radius: 10px; color: white; text-align: center; }
				h1 { font-size: 50px; line-height: 1.3; letter-spacing: 5px; text-transform: uppercase; text-shadow: 0 1px 0 #efefef, 0 2px 0 #efefef, 0 3px 0 #efefef, 0 4px 0 #efefef, 0 12px 5px rgba(0, 0, 0, 0.1); animation: rotate ease-in-out 1s infinite alternate; }
				p { font-size: 20px; text-shadow: 0 1px 0 #efefef; animation: 5s ease 0s normal forwards 1 fadeIn; }
			</style>
			<div class="container">
				<h1>Made with HTML &amp; CSS<br />not an animated GIF</h1>
				<p>Click to see the source</p>
			</div>
		</div>
	</foreignObject>
</svg>
```

## Tables
```markdown
|Redis Type|Javascript Type|
|---|---|
|string|String|
|list|Array of String|
|set|Array of String|
|hash|Object(keys have String values)|
|float|String|
|integer|number|
```

## Images
```markdown
![Test Image 4](https://github.com/tograh/testrepository/3DTest.png)
```

## Listing
```markdown
- [Root Topic](#root-topic)
  - [1. Overview](#1-overview)
  - [2 Basic components](#2-basic-components)
    - [2.1. Message composer](#21-message-composer)
    - [2.2 Message senders](#22-message-senders)
```

## Allowed HTML tags
```markdown
h1 h2 h3 h4 h5 h6 h7 h8 br b i strong em a pre code img tt div ins del sup sub p ol ul table thead tbody tfoot blockquote dl dt dd kbd q samp var hr ruby rt rp li tr td th s strike summary details
```