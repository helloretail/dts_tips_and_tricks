# **Search snippets**
### *Search AI synonym suggested text*
```
    {% if suggestedProductStatus == 'ONLY_SUGGESTED' %}
    <h4 class="hr-products-sub-header">We didn't find exactly what you were looking for, but here are some products you might find interesting:</h4>
    {% elsif suggestedProductStatus == 'MIXED' %}
    <h4 class="hr-products-sub-header">We have expanded the search with more products you might find interesting</h4>
    {% endif %}
```