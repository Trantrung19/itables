---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.14.5
kernelspec:
  display_name: itables
  language: python
  name: itables
---

# Custom Extensions

## Connected mode

To use custom extensions in the connected mode, you just have to go to the
[download](https://datatables.net/download/) page of DataTables,
select jQuery, DataTable, and the desired extensions, and take a note of the script URL
when using the _CDN_ download method.

Then, you pass this custom URL as either the `dt_url` argument of the
`show` function, or you set it on `opt.dt_url`.

```{code-cell}
from itables import show
from itables.sample_dfs import get_countries

df = get_countries(html=False)
```

For instance, if you wish to use the [select](https://datatables.net/extensions/select/)
extension in the connected mode, you just have to set `dt_url` according to
the [download](https://datatables.net/download/) page of DataTables, that is:

```{code-cell}
show(
    df,
    select=True,
    dt_url="https://cdn.datatables.net/v/dt/jq-3.7.0/dt-2.0.2/sl-2.0.0/datatables.min.js",
)
```

## Internationalisation

DataTables controls can use a different language than English. To
display the table controls in another language, go to the [internationalisation](https://datatables.net/plug-ins/i18n/)
plug-ins page and find the language URL, like e.g.

```{code-cell}
show(
    df,
    language={"url": "https://cdn.datatables.net/plug-ins/2.0.2/i18n/fr-FR.json"},
)
```

```tip
You can also use the internationalization in the offline mode. Download the translation file,
then set `opt.language` accordingly:

~~~python
import json
import itables.options as opt

with open("fr-FR.json") as fp:
    opt.language = json.load(fp)
~~~
```

## Offline mode

To use custom extensions in the offline mode, you will need
to create a bundle of jQuery, DataTable, and the desired extensions.

To do so, make a copy of
[`itables/dt_package`](https://github.com/mwouts/itables/tree/main/itables/dt_package):
```bash
$ ls itables/dt_package/
package.json  package-lock.json  README.md  src.js
```

Add or remove the desired extensions in `package.json` and `src.js`. To do this,
you can use the [DataTables download](https://datatables.net/download/) page and
follow the instructions for the _NPM_ download method.

For instance, say you want to bundle the PDF export button. Change
`src.js` to this code:
```javascript
import JSZip from 'jszip';
import jQuery from 'jquery';
import pdfMake from 'pdfmake';
import DataTable from 'datatables.net-dt';
import 'datatables.net-dt/css/dataTables.dataTables.min.css';

import 'datatables.net-buttons/js/buttons.html5.mjs';
import 'datatables.net-buttons/js/buttons.print.mjs';
import 'datatables.net-buttons-dt/css/buttons.dataTables.min.css';

DataTable.Buttons.jszip(JSZip);
DataTable.Buttons.pdfMake(pdfMake);

pdfMake.vfs = pdfFonts.pdfMake.vfs;

export { DataTable, jQuery };
```

and run these commands:
```bash
# Install the dependencies in package.json
npm install

# Install the additional dependencies
npm install pdfmake --save

# Create dt_bundle.js and dt_bundle.css
npm run build
```

Finally, copy `dt_bundle.js`, and `dt_bundle.css` to a location that can be accessed by `itables`, and
pass the path to `dt_bundle.js` as the `dt_bundle` argument of the `init_notebook_mode` method
(or change `itables.options.dt_bundle` accordingly).