# **Search snippets**
## *Search AI synonym suggested text*
```
    {% if suggestedProductStatus == 'ONLY_SUGGESTED' %}
    <h4 class="hr-products-sub-header">We didn't find exactly what you were looking for, but here are some products you might find interesting:</h4>
    {% elsif suggestedProductStatus == 'MIXED' %}
    <h4 class="hr-products-sub-header">We have expanded the search with more products you might find interesting</h4>
    {% endif %}
```

## *Converting a search to local_storage storing*
### Find *import "hash_storage"* and replace it with the following line
```js
import "local_storage";
```

### Find *var storage = hash_storage.create("hr-search");* and replace it with the following line
```js
var storage = local_storage.create("hr-search");
```

### Find storage.put("search_term", state.search_term) and add the following line below it
```js
storage.put("last_url", window.location.href); // adding window.location.href to storage, as this is necessary for the local_storage search history to work properly.
```

### Find the storage.keys().length condition which invokes the *activate()* method, and replace it with the following line
```js
if (storage.keys().length > 0 && storage.get("last_url") == window.location.href) { // storage.get("last_url") was added to the condition, as this is necessary for local_storage search history to function properly.
	activate();
}
```

### Find the *trigger.addEventListener("click", activate);* line and replace it with the following code snippet
```js
trigger.addEventListener("click", (e) => {
    storage.clear();
    activate();
});
```

### Find the *trigger.addEventListener("focus", activate);* line and replace it with the following code snippet
```js
trigger.addEventListener("focus", (e) => {
    storage.clear();
    activate();
});
```