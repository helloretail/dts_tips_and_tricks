# **Feed V2 JS basics**
## *INTRODUCTION AND EXPLANATION*
### *INTRODUCTION*
The Hello Retail feed reader version 2 is the primary way for Hello Retail to receive, manipulate, and index a customers product data to later utilize said data in our solutions.

Once Hello Retail are in possession of a product feed that allows us to read the customers product data, the process of ensuring that the necessary data is stored in our database, begins.

The Hello Retail feed reader version 2 provides Hello Retail (and the customer) with an overview of the [raw source data](https://explain.helloretail.com/z8udz2x5) that was extracted from the customers product feed, which is displayed as JSON data.

Through the raw source data that are presented to us in the Hello Retail feed reader version 2, it is possible to start mapping the desired properties that we want to store on each product in our database, so that these properties can be utilized in our onsite solutions.

### *EXPLANATION*
By default, the Hello Retail feed reader version 2 is built to automatically map most of the properties that are found in the customers product feed data. This is done in order to reduce the time spent on mapping the properties in the product feed, as it is assumed that most of the provided data is going to be used.

The [JavaScript spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax), followed by the [root product parameter name](https://explain.helloretail.com/o0uK2zPd) *(...product)*, ensures that the Hello Retail feed reader version 2 starts automatically mapping the properties that it can.

While the Hello Retail feed reader version 2 is capable of mapping most properties automatically through the aforementioned functionality, there are situations in which the Hello Retail feed reader version 2 is not capable of automatically mapping properties.

To fully understand *why* certain properties are not automatically mapped, it becomes necessary to talk about JavaScript data types, and how some of these data types cannot be stored in the Hello Retail product database. This is, however, not crucial to understand the following parts of this guide, and so we will omit this for now. You can reach out to Mikkel for an indepth explanation if interested.

In this guide we will focus on mapping the properties that the Hello Retail feed reader version 2 does not automatically map.

This concludes the Introduction and Explanation segment.

## *Mapping properties in Feed v2*

### *Mapping a direct child property*

In this example data, the product url is delivered to us as *productLink*. Feed v2 might not recognize that *productLink* is in fact the url, and so it might not automatically map this property to our system. We are therefore going to manually map this property to url in our system.
```js
    {
        "type": "product_page",
        "id": "109560",
        "sku": "700179392",
        "productLink": "https://bolist-shop.5dev.se/produkt/snabbmaskering",

    }
```
Mapping *productLink* to url, from product data.
```js
function transform(product:any): TransformationResult {
	return {
		...product, /* feed v2 auto mapper.*/
		url: product.productLink /* manually mapped url. */
	};
}
```

### *Mapping a property that is nested within an object*
In this example data, the product prices are delivered to us in an object that exists at the root of the product data. The feed v2 auto mapper won't be able to read these prices due to the data structure. Furthermore, the feed v2 auto mapper most likely would not understand that *defaultPrice* should be mapped to our price variable, because the name is not similar enough to "price".

```js
	{
		"type": "product_page",
		"id": "109560",
		"sku": "700179392",
		"productLink": "https://bolist-shop.5dev.se/produkt/snabbmaskering",
		"prices":{
			"defaultPrice": 200,
			"originalPrice": 180,
		}

	}
```
Mapping *defaultPrice* to price, from product data.

Mapping *originalPrice* to oldPrice, from product data.
```js
function transform(product:any): TransformationResult {
	return {
		...product, /* feed v2 auto mapper.*/
		price: product.prices.defaultPrice, /* we mention "prices" before defaultPrice, because defaultPrice exists within the "prices" object in the product data. */
		oldPrice: product.prices.originalPrice
	};
}
```





