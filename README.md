# Pre
## For Javascript
```js
<script>
  /* javascript goes here */
</script>
```

## For CSS
```js
<style>
  /* styles goes here */
</style>
```

# Create **DATA**

```js
<script>
  let name = 'lukete';
</script>
```
```html
<h1>Hello {name}</h1>
```

# Dynamic attributes


```js
<script>
  let src = 'tutorial/image.gif';
</script>
```
```html
<img src={src} alt="">

<!-- or shorthand -->
<img {src} alt="">
```

# Styling
> Importantly, these rules are **SCOPED** to the component. 
```css
<style>
  p {
      color: #f00;
    }
</style>
  
```
```html
<p>This a paragraph</p>
```

# Nested Components

```js
<script>
  import Nested from './Nested.svelte';
  
</script>
```
```html
<p>This a paragraph.<p>
<Nested/>
```

# HTML TAGS
> You need to render HTML directly into a component.
- Use **@html**
```js
<script>
	let string = `this string contains some <strong>HTML!!!</strong>`;
</script>
```
```html
<p>{@html string}</p>
```

# Components
## Create
```js
import App from './App.svelte';

const app = new App({
 target: document.body,
 props: {
  answer: 27
 }
})
```

## Reactivity
> Svelte automatically updates the DOM when your component's state changes
> Svelte's reactivity is triggered by **assignments**
```js
let count = 0;
function handleClick() {
	count += 1;
}
```
```html
<button on:click={handleClick}>
	Clicked {count} {count === 1 ? 'time' : 'times' }
</button>
```
### Reactive Declarations.
> Don't worry if this looks a little alien. It's  (if unconventional) JavaScript
- Use **$:**
```js
let count = 0;
$: doubled = count * 2;
	function handleClick() {
		count += 1;
	}
```

```html

<button on:click={handleClick}>
	Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
<p>{count} doubled is {doubled}</p>
```

> You can easily group statements together with a block:
```js
$: {
	console.log(`the count is ${count}`);
	alert(`I SAID THE COUNT IS ${count}`);
}
```
> You can even put the **$:** in front of things like if blocks:
```js
$: if (count >= 10) {
	alert(`count is dangerously high!`);
	count = 9;
}
```

## Props
> In Svelte, we do that with the `export keyword`
```js
export let answer;
```

## Logic
### IF
```html
{#if user.loggedIn}
 <button>Log out</button>
{/if}


{#if !user.loggedIn}
	<button>
		Log in
	</button>
```
#### ELSE
```js
{#if user.loggedIn}
	<button on:click={toggle}>
		Log out
	</button>
{:else}
	<button on:click={toggle}>
		Log in
	</button>
{/if}
```
#### IF ELSE
```js
{#if x > 10}
	<p>{x} is greater than 10</p>
{:else if 5 > x}
	<p>{x} is less than 5</p>
{:else}
	<p>{x} is between 5 and 10</p>
{/if}
```

## EACH
> You can get the current **index as a second argument**
> If you prefer, you can use **destructuring — each cats as { id, name }**
```js
<ul>
	<!-- {#each cats as cat, i} -->
	<!-- {#each cats as { id, name }} -->
	{#each cats as cat}
		<li><a target="_blank" href="https://www.youtube.com/watch?v={cat.id}">
			{cat.name}
		</a></li>
	{/each}
</ul>
```
### Keyed each blocks
> The **(thing.id)** tells Svelte how to figure out what changed.
```js
import Thing from './Thing.svelte';

	let things = [
		{ id: 1, color: '#0d0887' },
		{ id: 2, color: '#6a00a8' },
		{ id: 3, color: '#b12a90' },
		{ id: 4, color: '#e16462' },
		{ id: 5, color: '#fca636' }
	];

	function handleClick() {
		things = things.slice(1);
	}

```

```html
{#each things as thing (thing.id)}
	<Thing current={thing.color}/>
{/each}
```

## Await Blocks
> Svelte makes it easy to await the value of  directly in your markup:
```js
let promise = getRandomNumber();

	async function getRandomNumber() {
		const res = await fetch(`tutorial/random-number`);
		const text = await res.text();

		if (res.ok) {
			return text;
		} else {
			throw new Error(text);
		}
	}

	function handleClick() {
		promise = getRandomNumber();
	}
```

```html
{#await promise}
	<p>...waiting</p>
{:then number}
	<p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}

<!--> or <!-->
{#await promise then value}
	<p>the value is {value}</p>
{/await}
```

## EVENTS
> you can listen to any event on an element with the **on:** **directive**
```js
let m = { x: 0, y: 0 };

	function handleMousemove(event) {
		m.x = event.clientX;
		m.y = event.clientY;
	}
```

```html
<div on:mousemove={handleMousemove}>
	The mouse position is {m.x} x {m.y}
</div>
```

> You can also declare event handlers inline:

```html
<div on:mousemove="{e => m = { x: e.clientX, y: e.clientY }}">
	The mouse position is {m.x} x {m.y}
</div>
```
### Modifiers
- **preventDefault** — calls
 - **event.preventDefault()** before running the handler. Useful for client-side form handling, for example
- **stopPropagation** — calls\
 - **event.stopPropagation()**, preventing the event reaching the next element
- **passive** — improves scrolling performance on touch/wheel events (Svelte will add it automatically where it's safe to do so)
- **capture** — fires the handler during the capture phase instead of the bubbling phase
- **once** — remove the handler after the first time it runs
- **self** — only trigger handler if event.target is the element itself
```js
function handleClick() {
		alert('no more alerts')
	}
```

```html
<!--> You can chain modifiers together, e.g. on:click|once|capture={...}. <-->
<button on:click|once={handleClick}>
	Click me
</button>
```

### Components Events
> Components can also dispatch events. To do so, they must create an **event dispatcher**
> **createEventDispatcher** must be called when the component is first instantiated — you can't do it later inside e.g. a setTimeout callback

```js
import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		});
	}
```

### Events Forwarding
> Unlike DOM events, component events **don't bubble**. If you want to listen to an event on some deeply nested component, the intermediate components must forward the event. 

```js
	import Inner from './Inner.svelte';
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher();

	function forward(event) {
		dispatch('message', event.detail);
	}
```

```html
 <Inner on:message={forward}/>
```
 > SHORTHAND
```js
import Inner from './Inner.svelte';
```

```html
 <Inner on:message />
```

> Event forwarding works for DOM events too
```html
<button on:click>
	Click me
</button>
```

## Bindings
> As a general rule, data flow in Svelte is **top down** — a parent component can set props on a child component, and a component can set attributes on an element, but not the other way around.

> **inputs**
```html
 <input bind:value={name}>
```

> **checkbox**
```html
 <input type=checkbox bind:checked={yes}>
```
> **textarea**
```html
 <textarea bind:value={value}></textarea>
 <textarea bind:value></textarea>
```

### Group
> If you have multiple inputs relating to the same value
- **bind:group**
```js
let menu = [
	'Cookies and cream',
	'Mint choc chip',
	'Raspberry ripple'
];
```
```html

{#each menu as flavour}
	<label>
		<input type=checkbox bind:group={flavours} value={flavour}>
		{flavour}
	</label>
{/each}
```

### Select
```html
<select bind:value={selected} on:change="{() => answer = ''}">

```

### Contenteditable
> Elements with a `contenteditable="true"` attribute support **textContent** and **innerHTML** bindings:
```html
<div
	contenteditable="true"
	bind:innerHTML={html}
></div>
```

### Media Elements
> The `<audio>` and `<video>` elements have several properties that you can bind to. This example demonstrates a few of them.
```html
<video
	poster="https://sveltejs.github.io/assets/caminandes-llamigos.jpg"
	src="https://sveltejs.github.io/assets/caminandes-llamigos.mp4"
	on:mousemove={handleMousemove}
	on:mousedown={handleMousedown}
	bind:currentTime={time}
	bind:duration
	bind:paused
></video>
```

### Dimensions
> Every **block-level** element has **clientWidth**, **clientHeight**, **offsetWidth** and **offsetHeight** bindings:
- These bindings are readonly — changing the values of w and h won't have any effect.
```html
<div bind:clientWidth={w} bind:clientHeight={h}>
	<span style="font-size: {size}px">{text}</span>
</div>
```


### THIS
> The readonly **this** binding applies to every element (and component) and allows you to obtain a reference to rendered elements.
```html
<canvas
	bind:this={canvas}
	width={32}
	height={32}
></canvas>
```
```js
import { onMount } from 'svelte';

	let canvas;
	
	onMount(() => {
		const ctx = canvas.getContext('2d');
		let frame;

		(function loop() {
			frame = requestAnimationFrame(loop);

			const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);

			for (let p = 0; p < imageData.data.length; p += 4) {
				const i = p / 4;
				const x = i % canvas.width;
				const y = i / canvas.height >>> 0;

				const t = window.performance.now();

				const r = 64 + (128 * x / canvas.width) + (64 * Math.sin(t / 1000));
				const g = 64 + (128 * y / canvas.height) + (64 * Math.cos(t / 1000));
				const b = 128;

				imageData.data[p + 0] = r;
				imageData.data[p + 1] = g;
				imageData.data[p + 2] = b;
				imageData.data[p + 3] = 255;
			}

			ctx.putImageData(imageData, 0, 0);
		}());

		return () => {
			cancelAnimationFrame(frame);
		};
	});
```

### Component Bindings
> Just as you can bind to properties of DOM elements, you can bind to component props. For example, we can bind to the value prop of this Keypad component as though it were a form element:
```html
<Keypad bind:value={pin} on:submit={handleSubmit}/>
```


## Lifecycle
> Every component has a lifecycle that starts when it is **created**, and ends when it is **destroyed**

### onMount
> runs after the component is **first rendered** to the DOM
> If the onMount callback returns a function, that function will be called when the component is destroyed.
```js
import { onMount } from 'svelte';

	let photos = [];

	onMount(async () => {
		const res = await fetch(`https://jsonplaceholder.typicode.com/photos?_limit=20`);
		photos = await res.json();
	});
```

### onDestroy
> To run code when your component is **destroyed**
```js
import { onDestroy } from 'svelte';

	let seconds = 0;
	const interval = setInterval(() => seconds += 1, 1000);

	onDestroy(() => clearInterval(interval));
```

### beforeUpdate and afterUpdate
> The beforeUpdate function schedules work to happen immediately before the DOM has been updated. afterUpdate is its counterpart, used for running code once the DOM is in sync with your data.
```js
let div;
let autoscroll;

beforeUpdate(() => {
	autoscroll = div && (div.offsetHeight + div.scrollTop) > (div.scrollHeight - 20);
});

afterUpdate(() => {
	if (autoscroll) div.scrollTo(0, div.scrollHeight);
});
```

### tick
> The tick function is unlike other lifecycle functions in that you can call it any time, not just when the component first initialises. It returns a promise that resolves as soon as any pending state changes have been applied to the DOM (or immediately, if there are no pending state changes).
```js
import { tick } from 'svelte';
```

## Stores

### Writable Stores
> A store is simply an **object** with a **subscribe** method that allows interested parties to be notified whenever the store value changes
- `count.update(n => n + 1);`
- `count.set(n => 0);`
```js
import { writable } from 'svelte/store';
export const count = writable(0);
```

### Auto subscriptions
> The app in the previous example works, but there's a subtle bug — the unsubscribe function never gets called. If the component was instantiated and destroyed many times, this would result in a **memory leak**.
**One way to fix it would be to use the onDestroy**
```js
import { onDestroy } from 'svelte';
import { count } from './stores.js';


	let count_value;

	const unsubscribe = count.subscribe(value => {
		count_value = value;
	});

	onDestroy(unsubscribe);
```
> Shorthand
```js
import { count } from './stores.js';
```

```html
<h1>The count is {$count}</h1>
```


### Readable Stores
- The **first argument** to readable is an **initial value**( can be null or undefined)
- The **second argument** is a **start function** that takes a set callback and returns a **stop function**.
 - The **start function** is called when the **store gets its first subscriber**
 - The **stop** is called when the **last subscriber unsubscribes.**
```js
import { readbable } from 'svelte';
export const time = readable(null, function start(set){
	set(new Date());
		}, 1000);
	return function stop() {};
});

```

### Derived Stories
> You can create a store whose value is based on the value of one or more other stores
```js
import { readable, derived } from 'svelte/store';

export const time = readable(new Date(), function start(set) {
	const interval = setInterval(() => {
		set(new Date());
	}, 1000);

	return function stop() {
		clearInterval(interval);
	};
});

const start = new Date();

export const elapsed = derived(
	time,
	$time => Math.round(($time - start) / 1000)
);
```

### Custom Stores
```js
import { writable } from 'svelte/store';
function createCount() {
	const { subscribe, set, update } = writable(0);

	return {
		subscribe,
		increment: () => update(n => n + 1),
		decrement: () => update(n => n - 1),
		reset: () => set(0)
	};
}

export const count = createCount();
```

### Store Bindings
```js
input bind:value={$name}>
```

## Motion

### Tweened
> Tweening those values. Svelte includes tools to help you build slick user interfaces that use animation to communicate changes.

```js
import { tweened } from 'svelte/motion';
import { cubicOut } from 'svelte/easing';
const progress = tweened(0,{
 duration:400,
 easing: cubicOut
});

```

```html
<progress value={$progress}></progress>
```

## Actions
> Actions are essentially element-level lifecycle functions. They're useful for things like:
- interfacing with third-party libraries
- lazy-loaded images
- tooltips
- adding custom event handlers

## Classes
> Like any other attribute, you can specify classes with a JavaScript attribute, seen here:
```html
<button
	class="{current === 'foo' ? 'active' : ''}"
	on:click="{() => current = 'foo'}"
>foo</button>
 ```
 > This is such a common pattern in UI development that Svelte includes a special directive to simplify it: The active class is added to the element whenever the value of the expression is truthy, and removed when it's falsy.

 ```html

<style>
	.active {
		font-size: 4em;
	}
</style>
<button
	class:active="{current === 'foo'}"
	on:click="{() => current = 'foo'}"
>foo</button>
 ```

## Component Composition
### Slots
```html
<div class="box">
	<slot></slot>
</div>
```
### Slots fallbacks
> component can specify fallbacks for any slots that are left empty
```html

<div class="box">
	<slot>
		<em>no content was provided</em>
	</slot>
</div>
```

### Named Slots
- Creating
```html
<div class="box">
	<slot name="content">
		<em>no content was provided</em>
	</slot>
</div>
```
- Using
```html
<span slot="name">
		luketevl
	</span>
```


### Slot props
- Creating
```js
let hovering;

	function enter() {
		hovering = true;
	}

	function leave() {
		hovering = false;
	}
```
- Using
 - Then, to expose hovering to the contents of the component, we use the **let directive**:
```html
<Hoverable let:hovering={active}>
	<div class:active={active}>
		{#if active}
			<p>I am being hovered upon.</p>
		{:else}
			<p>Hover over me!</p>
		{/if}
	</div>
</Hoverable>
```

## Context API
> The context API provides a mechanism for components to 'talk' to each other without passing around data and functions as props, or dispatching lots of events. It's an advanced feature, but a useful one.

> There are two halves to the context API — **setContext** and **getContext**. If a component calls setContext(key, context), then any child component can retrieve the context with const context = getContext(key).
```js
import { onMount, setContext } from 'svelte';
import { mapbox, key } from './mapbox.js';

setContext(key, {
	getMap: () => map
});
```
```js
import { getContext } from 'svelte';
import { mapbox, key } from './mapbox.js';

const { getMap } = getContext(key);
const map = getMap();
```

## Contexts vs. stores
> Contexts and stores seem similar. They differ in that stores are available to any part of an app, while a context is only available to a component and its descendants. This can be helpful if you want to use several instances of a component without the state of one interfering with the state of the others.
> In fact, you might use the two together. Since context is not reactive, values that change over time should be represented as stores:


## Special elements
- `<svelte:self>` | allows a component to contain itself recursively.
```html
{#if file.type === 'folder'}
	<svelte:self {...file}/>
{:else}
	<File {...file}/>
{/if}
```
- `<svelte:component>` | allows a component to contain itself recursively.
 -  The this value can be any component constructor, or a falsy value — if it's falsy, no component is rendered.
```html
<!--> Problem <-->
{#if selected.color === 'red'}
	<RedThing/>
{:else if selected.color === 'green'}
	<GreenThing/>
{:else if selected.color === 'blue'}
	<BlueThing/>
{/if}

<!--> Solution <-->
<svelte:component this={selected.component}/>

```
- `<svelte:window>` | Just as you can add event listeners to any DOM element, you can add event listeners to the **window** object
 -  As with DOM elements, you can add  like preventDefault.
```html
<svelte:window on:keydown={handleKeydown}/>
```


# OBS
- Accessibility (shortened to a11y) isn't always easy to get right, but Svelte will help by warning you if you write inaccessible markup.
- Svelte doesn't perform any sanitization of the expression inside {@html ...} before it gets inserted into the DOM. In other words, if you use this feature it's critical that you manually escape HTML that comes from sources you don't trust, otherwise you risk exposing your users to XSS attacks.
- Svelte's reactivity is triggered by assignments, using array methods like **push** and **splice** **won't** automatically cause updates
```js
//incorrect
function addNumber() {
	numbers.push(numbers.length + 1);
}
// correct
function addNumber() {
	numbers.push(numbers.length + 1);
	numbers = numbers;
	
	// or 
	numbers = [...numbers, numbers.length + 1];
}
```
- Conversely, if you need to reference all the **props** that were passed into a component, including ones that weren't declared with export, you can do so by accessing **$$props** directly. It's not generally recommended, as it's difficult for Svelte to optimise, but it's useful in rare cases.
- A **# character** always indicates a **block opening tag**. A **/ character** always **indicates a block closing tag**. A **: character**, as in {:else}, indicates a **block continuation tag**
- In some frameworks you may see recommendations to avoid inline event handlers for performance reasons, particularly inside loops. That advice doesn't apply to Svelte — the compiler will always do the right thing, whichever form you choose.
- Because we haven't set an initial value of **selected**, the binding will set it to the default value (the first in the list) automatically. Be careful though — until the binding is initialised, selected remains undefined, so we can't blindly reference e.g. selected.id in the template.
- **EACH BLOCK BINDINGS** Note that interacting with these <input> elements will mutate the array. If you prefer to work with immutable data, you should avoid these bindings and use event handlers instead.
- **DIMENSIONS** Elements are measured using a technique similar to . There is some overhead involved, so its not recommended to use this for large numbers of elements. display: inline elements cannot be measured with this approach; nor can elements that can't contain other elements (such as -canvas-). In these cases you will need to measure a wrapper element instead.
- **BIND THIS** Note that the value of canvas will be undefined until the component has mounted, so we put the logic inside the onMount
- Use **component bindings** sparingly. It can be difficult to track the flow of data around your application if you have too many of them, especially if there is no 'single source of truth'.
- It's recommended to put the **fetch in onMount** rather than at the top level of the script because of server-side rendering (SSR). With the exception of onDestroy, **lifecycle functions don't run during SSR**, which means we can avoid fetching data that should be loaded lazily once the component has been mounted in the DOM.
- Auto-subscription only works with store variables that are declared (or imported) at the top-level scope of a component.
- It's possible to derive a store from multiple inputs, and to explicitly set a value instead of returning it (which is useful for deriving values asynchronously). Consult the  for more information.
- **Named slots** can also have props; use the let directive on an element with a slot="..." attribute, instead of on the component itself.

```js

```
