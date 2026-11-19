---
layout: post
title: "Using sibling-index() For Fun Animations"
date: 2026-11-19
license: CC-BY-4.0
tags: [css]
---

*Note: This page contains moving and fading elements, which may cause discomfort for people with motion sensitivity or vestibular disorders.*

Now that `sibling-index()` is available in modern browsers, a whole new range of CSS possibilities has opened up. Here are a few interesting usages of it I've found.

Like all other CSS features, `sibling-index()` is one-based. This means for the first child, `sibling-index` will be one, and not zero. This is important when using it within the `calc()` function. Every one of these examples does exactly that, by subtracting one from `sibling-index()`.

We can use `sibling-index()` with a bit of help from `calc()` for the animation-delay property to make children elements smoothly appear one after another. In this example, I have also used it to adjust the hue of each child element (except the button):

<iframe class="doc" title="Child Element Smooth Appearance Demo"
srcdoc='
{% include doc.html %}
<title>Child Element Smooth Appearance Demo</title>
<style>
	.container {
	display: flex;
    	justify-content: center; 
		height: 4rem;
		margin: auto;
		gap: 1rem;
		padding: 1rem;
		position: relative;
	}
	.anim>* {
		animation: show 1s ease both;
		animation-delay: calc((sibling-index() - 1) * 0.1s);
	}
	.container>div {
		aspect-ratio: 1 / 1;
    	background-color: hsl(calc(10deg * (sibling-index() - 1)) 100% 50%);
		border-radius: .4rem;
	}
	button {
		height: 100%;
	}
	@keyframes show {
		from {
			visibility: hidden;
			opacity: 0;
		}
		to {
			opacity: 1;
		}
	}
</style>
<div class="container">
<div></div>
<div></div>
<div></div>
<div></div>
<div></div>
<button class="normal" onclick="play()">Play Animation</button>
<script>
	const container = document.querySelector(".container");
	function play() {
		container.classList.remove("anim");
		void container.offsetWidth;
		container.classList.add("anim");
	}
</script>
</div>
'></iframe>

Here's CSS I used. We need the `visibility` property because the `opacity` property does not control whether the elements will receive clicks. Note that some of the rules are left out to highlight the core idea:

```css
.container {
	display: flex;
	justify-content: center; 
	height: 4rem;
	margin: auto;
	gap: 1rem;
	padding: 1rem;
	position: relative;
}

.anim>* {
	animation: show 1s ease both;
	animation-delay: calc((sibling-index() - 1) * 0.1s);
}

.container>div {
	aspect-ratio: 1 / 1;
	background-color: hsl(calc(10deg * (sibling-index() - 1)) 100% 50%);
	border-radius: .4rem;
}

@keyframes show {
	from {
		visibility: hidden;
		opacity: 0;
	}
	to {
		opacity: 1;
	}
}
```

As you can see, the DOM is very simple. The CSS controls all the styling without need for any JavaScript workarounds:

```html
<div class="container">
<div></div>
<div></div>
<div></div>
<div></div>
<div></div>
<button onclick="play()">Play Animation</button>
</div>
```

This example is good, but doesn't show an actual use for it. One good way to put this to work is with menu items. This example also makes them slide in from the left.

<iframe class="doc" title="Menu Item Animation Demo"
srcdoc='
{% include doc.html %}
<title>Menu Item Animation Demo</title>
<style>
	.menu {
    	margin: auto;
		max-width: 20rem;
		background: #222222;
		padding: 1rem;
	}
	ul {
		list-style: none;
		padding: 0;
		display: grid;
		gap: .5rem;
	}
	.anim li {
		animation: show 0.5s ease both;
		animation-delay: calc((sibling-index() - 1) * 0.05s);
	}
	button {
		height: 100%;
	}
	@keyframes show {
		from {
			transform: translateX(-.5rem);
			visibility: hidden;
			opacity: 0;
		}
		to {
			visibility: visible;
			opacity: 1;
		}
	}
</style>
<div class="menu">
<h2>Menu</h2>
<ul>
	<li>Home</li>
	<li>About</li>
	<li>Contact</li>
	<li>Your Profile</li>
</ul>
<button class="normal" onclick="play()">Play Animation</button>
<script>
	const menu = document.querySelector(".menu");
	function play() {
		menu.classList.remove("anim");
		void menu.offsetWidth;
		menu.classList.add("anim");
	}
</script>
</div>
'></iframe>

Again, here's our CSS:

```css
.menu {
	margin: auto;
	max-width: 20rem;
	background: #222222;
	padding: 1rem;
}

ul {
	list-style: none;
	padding: 0;
	display: grid;
	gap: .5rem;
}

.anim li {
	animation: show 0.5s ease both;
	animation-delay: calc((sibling-index() - 1) * 0.05s);
}

@keyframes show {
	from {
		transform: translateX(-.5rem);
		visibility: hidden;
		opacity: 0;
	}
	to {
		visibility: visible;
		opacity: 1;
	}
}
```

And the HTML:

```html
<div class="menu">
<h2>Menu</h2>
<ul>
	<li>Home</li>
	<li>About</li>
	<li>Contact</li>
	<li>Your Profile</li>
</ul>
<button onclick="play()">Play Animation</button>
</div>
```

Finally, we can also use `sibling-index()` for a loading animation. In the previous examples, the animations were finite, but this one runs indefinitely, as long as the elements remain in the document. By using a negative value for the `animation-delay` property, the animation will play as though it had already been running for that amount of time. The `alternate` keyword in the `animation` property tells the browser to change the "playing direction" of the animation after each play, which causes the effect of the dots going both up and down instead of just upwards and snapping back down.

<iframe class="doc" title="Wave Loader Demo"
srcdoc='
{% include doc.html %}
<title>Wave Loader Demo</title>
<style>
	.loader {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 4rem;
		gap: .5rem;
	}
	.loader span {
		width: .75rem;
		height: .75rem;
		border-radius: 50%;
		background: currentColor;
		animation: bounce 0.8s ease-in-out infinite alternate;
		animation-delay: calc((sibling-index() - 1) * -0.15s);
	}
	@keyframes bounce {
		from {
			transform: translateY(.4rem);
			opacity: .3;
		}
		to {
			transform: translateY(-.4rem);
			opacity: 1;
		}
	}
</style>
<div class="loader">
	<span></span>
	<span></span>
	<span></span>
	<span></span>
	<span></span>
</div>
'></iframe>

Here is the CSS:

```css
.loader {
	display: flex;
	justify-content: center;
	align-items: center;
	height: 4rem;
	gap: .5rem;
}

.loader span {
	width: .75rem;
	height: .75rem;
	border-radius: 50%;
	background: currentColor;
	animation: bounce 0.8s ease-in-out infinite alternate;
	animation-delay: calc((sibling-index() - 1) * -0.15s);
}

@keyframes bounce {
	from {
		transform: translateY(.4rem);
		opacity: .3;
	}
	to {
		transform: translateY(-.4rem);
		opacity: 1;
	}
}
```

And the HTML:

```html
<div class="loader">
	<span></span>
	<span></span>
	<span></span>
	<span></span>
	<span></span>
</div>
```

As you can see, the `sibling-index()` function is very useful and entertaining when used properly. However, there are a few things worth noting. Because this feature is only newly available, users with older browsers might not see it. To see what browsers support it, head to the [Can I Use](https://caniuse.com/wf-sibling-count) webpage about it. Also, when using large animations, ensure that users can dial them down by using the `prefers-reduced-motion` media query:

```css
@media (prefers-reduced-motion: reduce) {
	.my-big-animation {
		animation: none;
	}
}
```