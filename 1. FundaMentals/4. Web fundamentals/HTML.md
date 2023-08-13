The first and most dominant component of the front end of web applications is [HTML (HyperText Markup Language)](https://en.wikipedia.org/wiki/HTML). 

HTML is at the very core of any web page we see on the internet. 

It contains each page's basic elements, including titles, forms, images, and many other elements. The web browser, in turn, interprets these elements and displays them to the end-user.


As we can see, HTML elements are displayed in a tree form, similar to `XML` and other languages:



<mark style="background: #FF5582A6;"> HTML Usage</mark>

The `<head>` element usually contains elements that are not directly printed on the page, like the page title, while all main page elements are located under `<body>`. 
Other important elements include the `<style>`, which holds the page's CSS code, and the `<script>`, which holds the JS code of the page, as we will see in the next section.


Each of these elements is called a [DOM (Document Object Model)](https://en.wikipedia.org/wiki/Document_Object_Model). 


The [World Wide Web Consortium (W3C)](https://www.w3.org) defines `DOM` as:

`"The W3C Document Object Model (DOM) is a platform and language-neutral interface that allows programs and scripts to dynamically access and update the content, structure, and style of a document."`

The DOM standard is separated into 3 parts:

-   `Core DOM` - the standard model for all document types
-   `XML DOM` - the standard model for XML documents
-   `HTML DOM` - the standard model for HTML documents


Understanding the HTML DOM structure can help us understand where each element we view on the page is located, which enables us to view the source code of a specific element on the page and look for potential issues. 
We can locate HTML elements by their id, their tag name, or by their class name.

This is also useful when we want to utilize front-end vulnerabilities (like `XSS`) to manipulate existing elements or create new elements to serve our needs.