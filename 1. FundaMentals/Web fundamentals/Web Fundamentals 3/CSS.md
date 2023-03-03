[CSS (Cascading Style Sheets)](https://www.w3.org/Style/CSS/Overview.en.html) is the stylesheet language used alongside HTML to format and set the style of HTML elements.

Like HTML, there are several versions of CSS, and each subsequent version introduces a new set of capabilities that can be used for formatting HTML elements. 

Browsers are updated alongside it to support these new features.

<mark style="background: #BBFABBA6;">
Example</mark>

At a fundamental level, CSS is used to define the style of each class or type of HTML elements (i.e., `body` or `h1`), such that any element within that page would be represented as defined in the CSS file.

This could include the font family, font size, background color, text color and alignment, and more.

Code: css

```css
body {
  background-color: black;
}

h1 {
  color: white;
  text-align: center;
}

p {
  font-family: helvetica;
  font-size: 10px;
}
```

As previously mentioned, this is why we may set unique IDs or class names for certain HTML elements so that we can later refer to them within CSS or JavaScript when needed.


<mark style="background: #BBFABBA6;">Syntax</mark>

CSS defines the style of each HTML element or class between curly brackets `{}`, within which the properties are defined with their values (i.e. `element { property : value; }`).

Each HTML element has many properties that can be set through CSS, such as `height`, `position`, `border`, `margin`, `padding`, `color`, `text-align`, `font-size`, and hundreds of other properties. All of these can be combined and used to design visually appealing web pages.

CSS can be used for advanced animations for a wide variety of uses, from moving items all the way to advanced 3D animations. Many CSS properties are available for animations, like `@keyframes`, `animation`, `animation-duration`, `animation-direction`, and many others. You can read about and try out many of these animation properties [here](https://www.w3schools.com/css/css3_animations.asp).

<mark style="background: #BBFABBA6;">Frameworks</mark>

Many may consider CSS to be difficult to develop. In contrast, others may argue that it is inefficient to manually set the style and design of all HTML elements in each web page. This is why many CSS frameworks have been introduced, which contain a collection of CSS style-sheets and designs, to make it much faster and easier to create beautiful HTML elements.

Furthermore, these frameworks are optimized for web application usage. 
They are designed to be used with JavaScript and for wide use within a web application and contain elements usually required within modern web applications. 

Some of the most common CSS frameworks are:

-   [Bootstrap](https://www.w3schools.com/bootstrap4/)
-   [SASS](https://sass-lang.com/)
-   [Foundation](https://en.wikipedia.org/wiki/Foundation_(framework))
-   [Bulma](https://bulma.io/)
-   [Pure](https://purecss.io/)