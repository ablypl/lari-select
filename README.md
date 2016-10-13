# Lari Select

Select component for vuejs [Demo] (http://select.lari.pl/)




## Installation

```
$ npm install lari-select --save
```

## Examples

### Single Select

```vue
<template>
    <lari-select></lari-select>
</template>

<script>
    import { LariSelect } from 'lari-select'
    export default {
        components: {
            LariSelect
        }
    }
</script>
```

### Multiple

```vue
<template>
    <lari-multiselect></lari-multiselect>
</template>

<script>
    import { LariMultiselect } from 'lari-select'
    export default {
        components: {
            LariMultiselect
        }
    }
</script>
```

## Docs

#### Single selected

Available props:
* **url: { default: '/tags'}** - url for remote results and store new records (RESTFul Resource)
* **field: { default: 'name' }** - variable from object to display. Eg. If your object looks like **{ id: 1,  title: "test title" }** you should change it into 'title';
* **name: {}** - input field name. For multiple select its an array, so you dont need to include '[]'
* **return: { default: 'id'}** - field to return to input as a value
* **resources: { default: false }** - available options. Array of objects. You should define it when you don't want to query remote data.
* **selected : {}** - current item. For single select provide an object, for multipleselect provide an array of ids
* **withcreate: { default: true }** - allow users to create new resources.
* **labels: {}** - Provide an object of labels. Eg. { placeholder: "Select new element" }. Available options:
    * Single select
        * placeholder: default **"Select element"**
    *Multiple select
        * add: default **"Add tags"**,
        * clear: default **"Clear tags"**,
        * delete: default **"Delete all elements?"**,
        * yes: default **"Yes"**,
        * no: default **"No"**
    * General:
        * typeText: default **"Type element name"**,
        * create: default **"Create"**,
        * no_resuts: default **"No results"**,
        * search: default **"Search**"        



## Badges

![](https://img.shields.io/badge/license-MIT-blue.svg)
![](https://img.shields.io/badge/status-stable-green.svg)

---
