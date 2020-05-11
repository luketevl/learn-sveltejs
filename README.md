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



```js

```
