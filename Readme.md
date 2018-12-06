# Simple filterable items Vue component

This is just a basic component which handles filtering and sorting of items.  I needed something like this
for the 99% case where I have a table that users just want to filter and order by clicking columns.

The filtering and sorting are _very_ basic.  Just using plain javascript `String.includes()` and `Array.sort()`.  If you need something more advanced then you might be better forking this and adding the features you need.

## Usage

Assuming you have the the `FilterableItems.vue` file in a 'components' directory in your app where you're
building things up - then something like this :

```
Vue.component('filterable-items', require('./components/FilterableItems.vue'));
```

then elsewhere in your app (this is using bulma for css):

```
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
