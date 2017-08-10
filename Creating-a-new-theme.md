## How to create a new theme

It's very easy to add a new theme, there are just a few simple steps:

 1. Add a new `<option>` element to `select#theme` in [`src/web/html/index.html`](https://github.com/gchq/CyberChef/blob/master/src/web/html/index.html) with the name of your theme.
    
    ```html
    <option value="mytheme">My Theme</option>
    ```

 2. Copy the contents of [`src/web/stylesheets/themes/_dark.css`](https://github.com/gchq/CyberChef/blob/master/src/web/stylesheets/themes/_dark.css) into a new file with the name of your theme i.e. `src/web/stylesheets/themes/_mytheme.css`.
 3. Modify the JSDoc comment appropriately.
 4. Change the class name attached to the `:root` selector to be the same as the value you gave the `<option>` tag in step 1.
    
    ```css
    :root.mytheme {
    ```

 5. Add an import for your theme in `src/web/stylesheets/index.css`.

    ```css
    @import "./themes/mytheme.css";
    ```

 6. Change the values of the CSS properties to modify the theme to your taste.
 7. [Submit a pull request!](https://github.com/gchq/CyberChef/wiki/Contributing)
