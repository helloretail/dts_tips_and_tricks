# Starweb documentation of dynamicPriceHandler

<a href="https://starwebab.notion.site/DynamicPriceHandler-98c6dbd5b444448c926f771ff9bf8a93#c607116d2d2842d183b50f768aeee231" target="_blank">Link to Starweb produced documentation</a>

## HTML structure that must be adhered to by Hello Retail in **all** Hello Retail frontend solutions;
```html
<div class="product-price hr-starweb-prices" data-sku="{{ product.productNumber }}">
  <div class="selling-price">
    <span class="price">
      <span class="amount hr-price hr-starweb-currentPrice">current price</span>
    </span></div>
  <div class="original-price">
    <span class="price">
      <span class="amount hr-oldPrice hr-starweb-oldPrice">old price</span>
    </span>
  </div>
</div>
```
## JavaScript snippets that must be present within Hello Retail Recommendations that should have dynamic prices;
<a href="https://explain.helloretail.com/OAuZD9Bd" target="_blank">Function declaration & invocation</a>
```js
function starwebHelloRetailPriceHandler_{{ key }}(selector){
		let priceList = document.querySelectorAll(selector);
		if(priceList.length > 0){
			if(typeof dynamicPriceHandler === 'object'){
				console.log("starwebSearchPriceHandler invoked");
				dynamicPriceHandler.setSeparators(" ",",");
				dynamicPriceHandler.render(priceList,false).then(()=>{
					console.log("successfully injected Starweb prices");
				}).catch((err)=>{
					console.log("something went wrong: ",err);
				});
			}
			else{
				console.log("dynamicPriceHandler is not defined.");
			}
		}
		else{
			console.log("no products to invoke starweb price method on.");
		}
	}
```
```js
on: {
      slideChange: function (e) {
        setTimeout(function(){
          starwebHelloRetailPriceHandler_{{ key }}("#{{ key }} .hr-starweb-prices");
        },200);
      },
    },
```

## JavaScript snippet that must be present within Hello Retail Search that should have dynamic prices;
<a href="https://explain.helloretail.com/Bluv87xn" target="_blank">Function declaration Desktop search, Embedded search, Pages</a>
```js
function starwebHelloRetailPriceHandler_search(selector){
	let priceList = document.querySelectorAll(selector);
	if(priceList.length > 0){
		if(typeof dynamicPriceHandler === 'object'){
			console.log("starwebHelloRetailPriceHandler_search invoked");
			dynamicPriceHandler.setSeparators(" ",",");
			dynamicPriceHandler.render(priceList,false).then(()=>{
				priceList.forEach((starwebSelector)=>{
					starwebSelector.classList.remove("hr-starweb-prices"); // remove hr-starweb-prices selector from elements that have already had their prices adjusted by the starweb endpoint. This is done in order to spare the starweb endpoint, as more and more products are lazyloaded into the search.
				});
			}).catch((err)=>{
				console.log("something went wrong: ",err);
			});
		}
		else{
			console.log("dynamicPriceHandler is not defined.");
		}
	}
	else{
		console.log("no products to invoke starweb price method on.");
	}
}
```

<a href="https://explain.helloretail.com/2Nu8LYzv" target="_blank">Function invocation Desktop search & Embedded search 1 (Initial content)</a> <br>
<a href="https://explain.helloretail.com/NQuAPk5q" target="_blank">Function invocation Desktop search & Embedded search 2 (Initial content)</a>

```js
starwebHelloRetailPriceHandler_search(".hr-overlay-search .hr-products.initialcontent .hr-starweb-prices");
```
<a href="https://explain.helloretail.com/p9u2Dgop" target="_blank">Function invocation Desktop Search & Embedded search</a>

```js
starwebHelloRetailPriceHandler_search(".hr-overlay-search .hr-search-overlay-product .hr-starweb-prices");
```

<a href="https://explain.helloretail.com/kpuZ0yLz" target="_blank">Function invocation Mobile search (Initial content)</a>

```js
starwebHelloRetailPriceHandler_search(".hr-overlay-search .hr-search-overlay-product .hr-starweb-prices");
```
<a href="https://explain.helloretail.com/5zuKQDXj" target="_blank">Function invocation Mobile search</a>

```js
starwebHelloRetailPriceHandler_search(".hr-overlay-search .hr-search-overlay-product .hr-starweb-prices");
```

## How to configure price formatting and handling through the dynamicPriceHandler method;
The parent elements (first mutual container of both hr-starweb-oldPrice and hr-starweb-currentPrice) should **always** have the class hr-starweb-prices.

The parent element should always have the data-attribute called sku, containing the products sku. The SKU value is sometimes stored as productNumber in our system. If this is the case, you can simply add {{ product.productNumber }} in the data attribute on the product HTML shown in the very beginning of this documentation.

If SKU is not indexed in productNumber in our product lookup, you can create an extraData.sku in in the feed, and ensure that the data attribute on the HTML now contains {{ product.extraData.sku }}.

<a href="https://explain.helloretail.com/OAuZDKzG" target="_blank">Indexing the SKU value in Feed v1;</a>
```
$("mainVariant sku").text()
```
<a href="https://explain.helloretail.com/X6uvPkJB" target="_blank">Indexing the SKU value in Feed v2;</a>
```
product.mainVariant.sku ? product.mainVariant.sku : Array.isArray(product.mainVariant.data) && product.mainVariant.data.length && product.mainVariant.data[0]?.sku ? product.mainVariant.data[0]?.sku : null
```
<br>
The direct elements containing the oldPrice and currentPrice should always have the classes hr-starweb-oldPrice and hr-starweb-currentPrice.

The priceList variable should **always** select all of the hr-starweb-prices elements (The mutual parent element of current and old price).

The dynamicPriceHandler.**setSeparators** accepts two arguments;
- The first argument determines which thousand separator you want the price to have.
- The second argument determines which decimal separator you want the price to have.

The dynamicPriceHandler.**render** accepts two arguments;
- The first argument is the priceList variable, as this is where you provide Starweb with the list of product tiles you want them to dynamically adjust the price of.
- The second argument is called the leaveDefaultPrice argument, and works as follows;
The leaveDefaultPrice allows Hello Retail to inform Starweb of how Starweb should handle the prices if Starweb could not recognize the product and find the correct price to show.

If leaveDefaultPrice is true, and Starweb could not recognize the product, Starweb will not manipulate the prices of this product.

If leaveDefaultPrice is false, and Starweb could not recognize the product, Starweb will set both the oldPrice and currentPrice textContent to be empty, leaving the product tile of the unrecognizable product with no prices displayed.

## How Starweb injects the price points, and how you can apply styling to these injected price points;
When Starweb injects prices into the prepared HTML structure from Hello Retail, Starweb does so by introducing 2 new span tags; One for the actual price value, and one for the currency symbol.

Example of prepared Hello Retail HTML structure, with injected Starweb span elements.
```html
<div class="product-price">
  <div class="selling-price">
    <span class="price">
      <span class="amount hr-price hr-starweb-currentPrice">
        <span class="hr-starweb-currentPrice-price-span">2 187,50</span> <!-- Price span injected by Starweb -->
        <span class="hr-starweb-currentPrice-unit-span">kr</span> <!-- Currency span injected by Starweb -->
      </span>
    </span>
  </div>
  <div class="original-price">
    <span class="price">
      <span class="amount hr-oldPrice hr-starweb-oldPrice">
        <span class="hr-starweb-oldPrice-price-span">2 905,63</span> <!-- Price span injected by Starweb -->
        <span class="hr-starweb-oldPrice-unit-span">kr</span> <!-- Currency span injected by Starweb -->
      </span>
    </span>
  </div>
</div>
```
In order to apply styling to the Starweb injected span elements, you simply apply styling like you would to any other element.

Should you want to reverse the order of the price and currency symbol, you can do so through display flex and the "order" property.