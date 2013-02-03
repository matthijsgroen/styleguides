Stylesheet file structure
=========================

The general rules are:

- Files started with an _ are not main files and are used to be imported elsewhere
- Group files on functional or elemental parts. e.g.: _grid.css.scss, _typography.css.scss, _side_menu.css.scss
- In @import statements, don't use the underscore prefix, it is not needed.
- The application.css.scss should only have @import or global variable declarations.

Example file tree:

```

stylesheets/
  application.css.scss
  _grid.css.scss
  _typography.css.scss
  _colors.css.scss
  elements/
    _side_menu.css.scss
    _header.css.scss
    _footer.css.scss

```

Order of properties
-------------------

+ Fonts & Color
+ Text
+ Background
+ Dimensions
+ Positioning & Page Flows
+ Borders
+ Bullets & Advanced
+ Pseudo class selectors
+ Nested selectors

Using the 'category' in the actual code is optional.

Example:

```css

h1 {
  /* Fonts & Color */
  color: gray;
  font-size: 1em;
  /* Text */
  line-height: 1.2em;
  text-align: left;
  /* Background */
  background-color: red;
  /* Positioning & Page Flows */
  width: 200px;
  padding: 3px;
  /* Etc... */
}

```

Properties with shorthand methods
---------------------------------

Use the explicit property when you need to set only one value:

```sass

margin-left: 4px;

```

Use SASS' nested form when you need to set up to 3 values:

```sass

margin: {
  right: 2px;
  bottom: 3px;
}

```

Use the shorthand method when you're setting all values:

```sass

margin: 1px 2px 3px 4px;

```

