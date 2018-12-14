# Simple filterable items Vue component

This is just a basic component which handles filtering and sorting of items.  I needed something like this
for the 99% case where I have a table that users just want to filter and order by clicking columns.  Most of the 'data tables' I could find for Vue were way more complex and way more demanding of the way you use them - I just needed the basics and to be css/html agnostic.

The filtering and sorting are _very_ basic.  Just using plain javascript `String.includes()` and `Array.sort()`.  If you need something more advanced then you might be better forking this and adding the features you need.

## Usage

Assuming you have the the `FilterableItems.vue` file in a 'components' directory in your app where you're
building things up - then something like this :

```
Vue.component('filterable-items', require('./components/FilterableItems.vue'));
```

then elsewhere in your app (this is using bulma for css):

```Vue
<filterable-items :items='myArrayOfCarObjects' searchables="model_name,owner_name,owner_country">
  <span slot-scope="{ items: cars, inputAttrs, inputEvents, sortOn }">
    <input class="input" type="text" v-bind="inputAttrs" v-on="inputEvents" placeholder="Filter table...">
    <table class="table is-fullwidth is-striped is-hover">
      <thead>
        <tr>
          <th @click.prevent="sortOn('model_name')" class="cursor-pointer">Model</th>
          <th @click.prevent="sortOn('owner_name')" class="cursor-pointer">Owner</th>
          <th @click.prevent="sortOn('owner_country')" class="cursor-pointer">Country</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="car in cars" :key="car.id">
          <td>
            <a :href="`/cars/${car.id}`">
              {{ car.model_name }}
            </a>
          </td>
          <td>{{ car.owner_name }}</td>
          <td>{{ car.owner_country }}</td>
        </tr>
      </tbody>
    </table>
  </span>
</filterable-items>
```

### A little more detail

The `filterable-items` component can take two props 'items' is an array of objects, and the optional 'searchables' is a comma-seperated
list of object keys which will be checked when searching.  If you don't pass them then it will search all of the object's keys for a match.

The `span` within the component exposes some slot variables for you to use.  It returns the filtered items as 'items' (here mapped to 'cars' for readability in the table), some input attributes and events used to pass the search/filter term 'up' to the component and a 'sortOn' action so you can control the sort order of the items.

The component will also emit two events which you can listen for outside of the component. `filtered` is fired when someone types into the filter box (if you have one) and will give an event object `{text: the-text-the-user-typed, matches: [the,matching,objects]}`.  The second is `sorted` which is fired when someone clicks on a column (or whatever) which called the `sortOn` function - it gives you `{column: the-column-name-they-clicked, order: boolean}`.  The `order` gives you `true` for a-z and `false` for z-a ordering.

```Vue
<my-wrapper-component>
  <filterable-items @filtered="updatePageTitle" :items="books">
    <span slot-scope="{ items: books, inputAttrs, inputEvents, sortOn }">
      <input class="input" type="text" v-bind="inputAttrs" v-on="inputEvents" placeholder="Filter books...">
      <ul>
        <li v-for="book in books" :key="book.id">{{ book.title }}</li>
      </ul>
    </span>
  </filterable-items>
</my-wrapper-component>

// ... inside MyWrapperComponent

data() {
  return {
    books: [{id: 1, title: "The Growing Season"}, {id: 2, title: "The Gracekeepers"}]
  }
},
methods: {
  updatePageTitle(event) {
    document.title = `Searching for ${event.text} - ${event.matches.length} results`;
  }
}
```

### Thanks

A quick thanks to Adam Wathan whose [vue component video course](https://adamwathan.me/advanced-vue-component-design/) let me understand doing this kind of renderless re-usable component - cheers!