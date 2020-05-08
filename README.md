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


# OBS
- Accessibility (shortened to a11y) isn't always easy to get right, but Svelte will help by warning you if you write inaccessible markup.
- Svelte doesn't perform any sanitization of the expression inside {@html ...} before it gets inserted into the DOM. In other words, if you use this feature it's critical that you manually escape HTML that comes from sources you don't trust, otherwise you risk exposing your users to XSS attacks.








```js

```
