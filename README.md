o-colors [![Build Status](https://circleci.com/gh/Financial-Times/o-colors.png?style=shield&circle-token=0d3884dacba2fdd8aec8afbd2eaa84c79040ddd5)](https://circleci.com/gh/Financial-Times/o-colors)

The color palette for the FT masterbrand and sub-brand products.

- [Usage](#usage)
	- [Sass](#sass)
	- [Markup](#markup)
- [Migration guide](#migration-guide)
	- [Using palette version 2](#using-palette-version-2)
- [Contact](#contact)
- [Licence](#licence)


## Usage

There are a number of ways of using colors in your component or product. o-colors can be used via the [Build Service](#), but it is recommended you import the Sass into your project to make use of the many Sass mixins and functions available.


### Sass:

As with all Origami components, o-colors has a [silent mode](http://origami.ft.com/docs/syntax/scss/#silent-styles). To use its compiled CSS (rather than incorporating its mixins into your own Sass) set `$o-colors-is-silent : false;` in your Sass before you import the o-colors Sass:

```scss
$o-colors-is-silent: false;
@import 'o-colors/main';
```

#### Mixins and functions

o-colors has a number of mixins and functions for you to access the color palette in your project, here they are listed from the most to least preferred methods of working with o-colors.

##### Use case mixin

Use the `oColorsFor` mixin to add colour-related properties to your ruleset:

```scss
.my-thing {
	@include oColorsFor(custom-box box, background text, 80);
}
```

Will output:

```css
.my-thing {
	background-color: #f2dfce;
	color: #302d29; // black mixed with #f2dfce at 80%
}
```

It takes three arguments:

* **Use case list**: a list of colour use cases in order of preference.  The first one that is defined will be returned.
* **Property list**: a list of all the properties you want the colour applied to (background, border, text). They each correspond to `background-colour`, `border-color` and `color`. Default is _all_ which includes all three properties.
* **Text Level**: the opacity (1-100) for the text color derived from the background colour of the use case. _If a text use case exists already, this will have no effect_.

In the example above, the background and text colours are set, preferably from the *custom-box* use case, and if either of those properties are not available for that use case, from the *box* use case. If the text use case is not set, the function will output a text colour based on either black or white (depending on the brightness of the background) blended with the background colour at the text level specified.

### Use case function

If you need to use a color value as part of a more complex CSS rule, e.g. a border color for just one side, or a gradient background, use the `oColorsGetColorFor` function:

```scss
.my-thing {
	color: oColorsGetColorFor(article-life-arts-body article-body body, text, (default: blue));
}
```

The `oColorsGetColorFor` function takes three arguments:

* **Use case list**: a list of colour use cases in order of preference.  The first one that is defined for the specified property will be returned
* **Property**: The property that you want to use the colour for (background, border, or text).  Note that in contrast to the `oColorsFor` mixin, you must specify only one property.   Options are `background`, `border`, `text`, and `all`.
* **Options**: A Sass *map* of additional options, all of which are optional, and may comprise:
	* **default**: The name of a palette colour to return if none of the specified use cases are defined for the desired property.  May also be set to `null` or `undefined` to return that instead of the built in default (which is transparent)

### Palette colour function

If you have a colour use case not covered by those built into the colors module, consider defining a custom use case (see below) and then using the use case mixin or function described above.  However, if you need to use a particular colour in one single place only, it may be worth using the `oColorsGetPaletteColor` function, which returns the CSS color for a palette colour name:

```scss
.my-thing {
	color: oColorsGetPaletteColor('pink-tint4');
}
```

## Defining custom use cases

You can add use cases for your particular component or product. This is done using the `oColorsSetUseCase` mixin:

```scss
@include oColorsSetUseCase(email, text, 'grey-tint5');
```

It takes three arguments:

* **Use case**: your particular use case
* **Property**: the property for which the color should be used for (background, border, or text)
* **Color**: a color from the palette

If you are creating a use case for a component, you *must* namespace your use case name with the name of your component.

### Markup

When using the build service or importing the module with [silent mode](http://origami.ft.com/docs/syntax/scss/#silent-styles) set to false, o-colors provides you with helper classes to access the color palette. All palette colors are available as `.o-colors-palette-[NAME]` (which style just `background-color`) and use cases are available as `.o-colors-[USECASE]-[PROPERTY]` (which style the appropriate property):

```html
<p class="o-colors-body-text">Article text</p>
```

## Migration guide

### Using palette version 2.

o-colors now allows access to the new version of the color palette. The new palette will become default by version `4.0.0` but for now can be accessed by setting the `$o-colors-palette-version` variable to `2` before importing o-colors into your project:

```scss
$o-colors-palette-version: 2;
@import 'o-colors/main';
```

In addition to the functions and mixins already discussed in this documentation, there are some new features available and new abilities added to the existing mixins and functions.

#### New: Mix colors

`oColorsMix` is a new function that mixes two colors based on a percentage. This gives the impression of the base color appearing at the percentage opacity over the background color.
`oColorsMix` will accept either a color value or the name of an o-colors palette color as arguments.

Usage:

```scss
.o-colors-palette-white {
	border: 1px solid oColorsMix(black, white, 20);
}
```

Output:

```css
.o-colors-palette-white {
	border: 1px solid #cccccc;
}
```

#### New: Tint colors

`oColorsGetTint` function will return a tinted palette color based on a specified brightness.
The function takes the name of a palette color and a brightness value between 0-100.

Usage:

```scss
.o-colors-tinted-color {
	background-color: oColorsGetTint('blue', 90);
}
```

Output:

```css
.o-colors-tinted-color {
	background-color: #177ee6;
}
```

#### New: Generated text colors

`oColorsGetTextColor` will return a text color based on the background and an opacity specified. The base of the text color is either black or white depending on the brightness of the background color and then mixed with the background at the specified opacity using `oColorsMix`.

_Warning_: if the combination of background and text colour created by the function would not pass WCAG AA level, o-colors will throw an error.

Usage:

```scss
.o-colors-palette-teal {
	color: oColorsGetTextColor(oColorsGetPaletteColor('teal'), 80);
}
```

Output:

```css
.o-colors-palette-teal {
	color: #cce3e5;
}
```

----

## Contact

If you have any questions or comments about this component, or need help using it, please either [raise an issue](https://github.com/Financial-Times/o-colors/issues), visit [#ft-origami](https://financialtimes.slack.com/messages/ft-origami/) or email [Origami Support](mailto:origami-support@ft.com).


----

## Licence

This software is published by the Financial Times under the [MIT licence](http://opensource.org/licenses/MIT).
