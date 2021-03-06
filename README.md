# hue-image-ie

Intended as a convenience component for images in the vue eco-system, works with ie11+, without the need for a polyfill, does not use the IntersectionObserver broswer API. This component is built with responsiveness and performance in mind. Lazy load all images, and don't request large image sizes for small screens.

Intended to be used by copying and modifying the source file, not intended to be used as a dependency.

## What It Does

This component ensures that any images contained within a view are only requested from the server when scrolled into the viewport (by default), or are loaded at a configutable point X pixels before being scrolled into the viewport.

It provides a placeholder SVG so the page doesn't jump around as images are requested, loaded and painted. The placeholders have a function attached to vary the background color by default, the background color can also be configured easily by changing the `fill` property in the svg elements `:style` attribute.

Provides the ability to provide a value to the images `srcset` attribute, via the prop `srcmap`, the documentation for `srcset` can be found (here)[https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#Attributes]

The format of the `srcmap` prop attempts to simplify the `<img>` `srcset` attribute an example of the prop value follows:
```
this.srcmap = {
    'small': 'path/to/small/image', // for screen widths below 480w
    'medium': 'path/to/medium/image', // for screen widths between 481w and 1280w
    'large': 'path/to/large/image', // for screen widths larger than 1281w
}
```
Check the speciaification for a defintion of the 'w' unit of measurement mentioned in the comments of the example above.

It would be easy to provide support for changing the breakpoints in the the example above, if thats something you need.