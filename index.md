---
layout: default
---

### Contenu

- [Déclarer un doctype](#doctype)
- [Mathématique des box model](#box-model-math)
- [Unités en Rem Sur Safari mobile](#rems-mobile-safari)
- [Éléments flottants en premier](#floats-first)
- [Nettoyage des éléments flottants](#floats-clearing)
- [Éléments flottants et hauteur calculée](#floats-computed-height)
- [Floated are block level](#floats-block-level)
- [Vertical margins often collapse](#vertical-margins-collapse)
- [Styling table rows](#styling-table-rows)
- [Firefox and `<input>` buttons](#buttons-firefox)
- [Firefox inner outline on buttons](#buttons-firefox-outline)
- [Always set a `type` on `<button>`s](#buttons-type)
- [Internet Explorer's selector limit](#ie-selector-limit)
- [Position explained](#position-explained)
- [Position and width](#position-width)
- [Fixed position and transforms](#position-transforms)


<a name="doctype"></a>
### Déclarer un doctype
Toujours inclure un doctype. Je recommande simplement le doctype HTML5 :

```html
<!DOCTYPE html>
```

[Ne pas mettre de doctype peut provoquer des problèmes](http://quirks.spec.whatwg.org) avec des tableaux malformés, des inputs et plus encore vu que la page sera affichée en 'quirks mode'.


<a name="box-model-math"></a>
### Mathématiques des box model
Les éléments qui ont une `width` (largeur) spécifiée deviennent *plus large* quand ils ont un `padding` et/ou une `border-width`. Pour éviter ces problèmes, utilisez le reset désormais commun [`box-sizing: border-box;`](http://www.paulirish.com/2012/box-sizing-border-box-ftw/).


<a name="rems-mobile-safari"></a>
### Unités en Rem Sur Safari mobile
Alors que Safari Mobile prend en charge l'utilisation des `rem` sur toutes les valeurs des propriétés, il semblerait que ça plante quand des `rem` sont utilisés dans les media queries et que ça fasse clignoter à l'infini les textes de différentes tailles des pages.

Pour le moment, utilisez des tailles en `em` plutôt qu'en `rem`.

```css
html {
  font-size: 16px;
}

/* Bugs de textes clignotants sur Safari Mobile */
@media (min-width: 40rem) {
  html {
    font-size: 20px;
  }
}

/* Fonctionne bien sur Safari Mobile */
@media (min-width: 40em) {
  html {
    font-size: 20px;
  }
}
```

**Help!** *Si vous avez un lien vers un rapport de bug d'Apple ou WebKit pour ça, J'aimerais l'inclure. Je ne suis pas sûr d'où signaler ça vu que cela ne s'applique que sur la version mobile de Safari.*


<a name="floats-first"></a>
### Éléments flottants en premier
Les éléments flottants devraient toujours être placés en premier dans l'ordre d'un document. Les éléments flottants exigent quelque chose auquel s'envelopper sinon ils peuvent "descendre" au lieu d'apparaitre sous le contenu.

```html
<div class="parent">
  <div class="float">Float</div>
  <div class="content">
    <!-- ... -->
  </div>
</div>
```


<a name="floats-clearing"></a>
### Nettoyage des éléments flottants
Si vous le faite flotter, vous avez *probablement* besoin de le "nettoyer". Tout contenu qui suit un élément avec un `float` va s'envelopper autour de cet élément jusqu'à ce qu'il soit "nettoyé". Pour cela, utilisez l'une des techniques suivantes.

Utilisez le [micro clearfix](http://nicolasgallagher.com/micro-clearfix-hack/) pour effacer vos éléments flottants avec une classe distincte.

```css
.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}
```

Vous pouvez également spécifier un `overflow`, avec `auto` ou `hidden` sur l'élément parent.

```css
.parent {
  overflow: auto; /* clearfix */
}
.other-parent {
  overflow: hidden; /* clearfix */
}
```

Soyez conscient que l'`overflow` peut causer d'autres effets secondaires indésirables, généralement autour des éléments positionnés au sein de l'élément parent.

**Pro-Tip!** *Rendez vos collègues heureux  en ajoutant un commentaire comme `/* clearfix */` quand vous nettoyer vos éléments flottants vu que la propriété peut être utilisée pour d'autres raisons.*


<a name="floats-computed-height"></a>
### Éléments flottants et hauteur calculée
Un élément parent qui a seulement des éléments flottants aura une hauteur calculée : `height: 0;`. Ajoutez un clearfix au parent pour forcer le navigateur à calculer une hauteur.


<a name="floats-block-level"></a>
### Éléments flottants et niveau de block
Les éléments avec un `float` vont automatiquement être en `display: block;`. Ne spécifiez pas ces deux propriétés tant que ce n'est pas nécessaire. Le `float` va outrepasser votre `display`.

```css
.element {
  float: left;
  display: block; /* Pas nécessaire */
}
```

**Fun fact:** *Ils y a quelques années, nous devions spécifier un `display: inline;` sur la plupart des éléments flottants pour fonctionner proprement sur IE6 et éviter le [bug des double margin](http://www.positioniseverything.net/explorer/doubled-margin.html). Toutefois, ces jours sont passés depuis longtemps.*


<a name="vertical-margins-collapse"></a>
### Vertically adjacent margins collapse
Top and bottom margins on adjacent elements (one after the other) can and will collapse in many situations, but never for floated or absolutely positioned elements. [Read this MDN article](https://developer.mozilla.org/en-US/docs/Web/CSS/margin_collapsing) or the CSS2 spec's [collapsing margin section](http://www.w3.org/TR/CSS2/box.html#collapsing-margins) to find out more.

Horizontally adjacent margins will **never collapse**.


<a name="styling-table-rows"></a>
### Styling table rows
Table rows, `<tr>`s, do not receive `border`s unless you set `border-collapse: collapse;` on the parent `<table>`. Moreover, if the `<tr>` and children `<td>`s or `<th>`s have the *same* `border-width`, the rows will not see their border applied. [See this JS Bin link for an example.](http://jsbin.com/yabek/2/)


<a name="buttons-firefox"></a>
### Firefox and `<input>` buttons

For reasons unknown, Firefox applies a `line-height` to submit and button `<input>`s that cannot be overridden with custom CSS. You have two options in dealing with this:

1. Stick to `<button>` elements
2. Don't use `line-height` on your buttons

Should you go with the first route (and I recommend this one anyway because `<button>`s are great), here's what you need to know:

```html
<!-- Not so good -->
<input type="submit" value="Save changes">
<input type="button" value="Cancel">

<!-- Super good everywhere -->
<button type="submit">Save changes</button>
<button type="button">Cancel</button>
```

Should you wish to go the second route, just don't set a `line-height` and use *only* `padding` to vertically align button text. [View this JS Bin example](http://jsbin.com/yabek/4/) in Firefox to see the original problem and the workaround.

**Good news!** *It looks like [a fix for this](https://bugzilla.mozilla.org/show_bug.cgi?id=697451#c43) might be coming in Firefox 30. That's good news for our future selves, but be aware this doesn't fix older versions.*


<a name="buttons-firefox-outline"></a>
### Firefox inner outline on buttons

Firefox [adds an inner outline](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button) to buttons (`<input>`s and `<button>`s) on `:focus`. Apparently it's for accessibility, but its placement seems rather odd. Use this CSS to override it:

```css
input::-moz-focus-inner,
button::-moz-focus-inner {
  padding: 0;
  border: 0;
}
```

You can see this fix in action in the same [JS Bin example](http://jsbin.com/yabek/4/) mentioned in the previous section.

**Pro-Tip!** *Be sure to include some focus state on buttons, links, and inputs. Providing an affordance for accessibility is paramount, both for pro users who tab through content and those with vision impairments.*


<a name="buttons-type"></a>
### Always set a `type` on `<button>`s
The default value is `submit`, meaning any button in a form can submit the form. Use `type="button"` for anything that doesn't submit the form and `type="submit"` for those that do.

```html
<button type="submit">Save changes</button>
<button type="button">Cancel</button>
```

For actions that require a `<button>` and are not in a form, use the `type="button"`.

```html
<button class="dismiss" type="button">x</button>
```

**Fun fact:** *Apparently IE7 doesn't properly support the `value` attribute on `<button>`s. Instead of reading the attribute's content, it pulls from the innerHTML (the content between the opening and closing `<button>` tags). However, I don't see this as a huge concern for two reasons: IE7 usage is way down, and it seems rather uncommon to set both a `value` and the innerHTML on `<button>`s.*


<a name="ie-selector-limit"></a>
### Internet Explorer's selector limit
Internet Explorer 9 and below have a max of 4,096 selectors per stylesheet. There is also a limit of 31 combined stylesheets and `<style></style>` includes per page. Anything after this limit is ignored by the browser. Either split your CSS up, or start refactoring. I'd suggest the latter.

As a helpful side note, here's how browsers count selectors:

```css
/* One selector */
.element { }

/* Two more selectors */
.element,
.other-element { }

/* Three more selectors */
input[type="text"],
.form-control,
.form-group > input { }
```


<a name="position-explained"></a>
### Position explained
Elements with `position: fixed;` are placed relative to the browser viewport. Elements with `position: absolute;` are placed relative to their closest parent with a position other than `static` (e.g., `relative`, `absolute`, or `fixed`).


<a name="position-width"></a>
### Position and width
Don't set `width: 100%;` on an element that has `position: [absolute|fixed];`, `left`, and `right`. The use of `width: 100%;` is the same as the combined use of `left: 0;` and `right: 0;`. Use one or the other, but not both.


<a name="position-transforms"></a>
### Fixed position and transforms
Browsers break `position: fixed;` when an element's parent has a `transform` set. Using transforms creates a new containing block, effectively forcing the parent to have `position: relative;` and the fixed element to behave as `position: absolute;`.

[See the demo](http://jsbin.com/yabek/1/) and read [Eric Meyer's post on the matter](http://meyerweb.com/eric/thoughts/2011/09/12/un-fixing-fixed-elements-with-css-transforms/).
