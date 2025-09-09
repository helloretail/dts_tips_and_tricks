# **Feed V2 JS snippets**
### *UnescapeHTML*
```js
const parser = new DOMParser();

description: parser.parseFromString(product.description, "text/html").textContent
```
### *isNew based on hours elapsed from when we saw the product / date provided by customer in feed*
```js
function hoursChecker(customerDate, hoursToConsiderNew) {
    const createdDate = new Date(customerDate).getTime();
    const currentDate = new Date().getTime();

    // Convert time from milliseconds to hours. Return true if elapsed hours is lower than provided number. 
    const hoursElapsed = Math.round((currentDate - createdDate) / (1000 * 60 * 60));
    return hoursElapsed < hoursToConsiderNew ? true : false;
}

function transform(item: any): TransformationResult {
    return {
        ...item,
        extraData: {
            isNewHours: hoursChecker(item.date, 720),
        }
    };
};
```

### *isNew based on days elapsed from when we saw the product / date provided by customer in feed*

```js
function daysChecker(customerDate, daysToConsiderNew) {
    const createdDate = new Date(customerDate).getTime();
    const currentDate = new Date().getTime();

    // Convert time from milliseconds to days. Return true if elapsed days is lower than provided number. 
    const daysElapsed = Math.round((currentDate - createdDate) / (1000 * 60 * 60 * 24));
    return daysElapsed < daysToConsiderNew ? true : false;
}

function transform(item: any): TransformationResult {
    return {
        ...item,
        extraData: {
            isNewDays: daysChecker(item.date, 30),
        }
    };
};
```

### *Extract specific filter value based on filter name, from array of anonymous filters (WOOCOMMERCE)*

```js
// Helper function
function specificAttributesHandler(attribute, name) {
	const value = [];
	if (Array.isArray(attribute)) {
		attribute.forEach((attr) => {
			if (attr._xmlAttributes.name === name) {
				if (Array.isArray(attr.attributeValue)) {
					value.push(...attr.attributeValue);
				} else {
					value.push(attr.attributeValue);
				}
			}
		})
	}
	else {
		if (attribute?._xmlAttributes.name === name) {
			if (Array.isArray(attribute.attributeValue)) {
				value.push(...attribute.attributeValue);
			} else {
				value.push(attribute.attributeValue);
			}
		}
	}
	return value;
}

// Transform code
function transform(product:any): TransformationResult {
	return {
		extraDataList: {
			colors: specificAttributesHandler(product.attributes[0]?.attribute,"Kleur"),
			material: specificAttributesHandler(product.attributes[0]?.attribute,"Materiaal"),
			sizes: specificAttributesHandler(product.attributes[0]?.attribute,"Maat"),
			width: specificAttributesHandler(product.attributes[0]?.attribute,"Breedte/wijdte")
		}
	};
}
```

### *Check if specific filter value, based on filter name, exists within an anonymous array of filters, and return a boolean value (mostly applicable to XML feeds)*

```js
function attributeExistsCheck(attribute,name) {
	const exists = function(attr) {
		return attr.name === name
	}
	//return attribute.some(exists) ? 'Yes' : 'No' --- Ternary operator which will return 'Yes' or 'No', instead of 'true' or 'false'
    return attribute.some(exists)
}


function transform(product:any): TransformationResult {
	return {
		extraData: {
			removeableSole: attributeExistsCheck(product.PATH_TO_ARRAY_OF_ANONYMOUS_FILTERS, "Uitneembaar voetbed")
		}
	};
}
```

### *Remove duplicate values from an array, and assign the array of unique values to an extraDataList selector*

```js
function imageSwatchHandler(variants){
	// Prepare empty array.
	let images = [];
	// Loop through ALL of the images, and push them to the 'images' array.
	variants.forEach(function(variant){
		images.push(variant.image.thumb);
	});
	// filter on contents of the 'images' array, and compare indexOf, to the index of the current iteration.
	let uniqueImages = images.filter(function(image, index){
		// indexOf will always return the index of the first element that matches the criteria, thus ensuring 
		// that any duplicate values found in the following iterations of the loop, does not match the indexOf value,
		// preventing them from being returned in the filtered array.
		return images.indexOf(image) === index;
	});
	return uniqueImages
}

function transform(product:any): TransformationResult {
	return {
		extraDataList: {
			imageSwatches: imageSwatchHandler(product.PATH_TO_ARRAY_OF_DUPLICATE_VALUES)
		}
	};
}
```

### *Select specific key value pairs based on parts of the key name (Used for pattern matching)*

```js
function attributesObjectKeySanitizer(key){
	// Sanitize keys to remove spaces, dashes, underscores.
	return key.replace(/\-([a-z]|\s([a-z]|\_([a-z])))/g,function(v) { return v.toUpperCase(); }).replace(/-/g,"");
}

function transform(product: any): TransformationResult {
	// define parent object with nested object objects to later spread on.
    let attributesObject = {
        extraData: {},
        extraDataNumber: {},
        extraDataList: {}
    };
    
	// loop through every key value pair of the product object.
    for (const [key, value] of Object.entries(product)) {
		// if a given iteration within the loop has a key that ends on -ti, add this key value pair to the extraData object defined above.
        if (key.endsWith("-ti")) {
             attributesObject.extraData[attributesObjectKeySanitizer(key)] = value;
        }
		// if a given iteration within the loop has a key that ends on -number, add this key value pair to the extraDataNumber object defined above.
        if(key.endsWith("-number")){
            attributesObject.extraDataNumber[attributesObjectKeySanitizer(key)] = value;
        }
		// if a given iteration within the loop has a key that ends on -til, add this key value pair to the extraDataList object defined above.
        if(key.endsWith("-til")){
            attributesObject.extraDataList[attributesObjectKeySanitizer(key)] = value;
        }
    }

    return {
        url: product.url,
        extraData: {
			// automatically assign the extraData values that was added to the extraData object nested within attributesObject.
            ...attributesObject.extraData
        },
        extraDataNumber: {
			// automatically assign the extraDataNumber values that was added to the extraData object nested within attributesObject.
            ...attributesObject.extraDataNumber
        },
        extraDataList: {
			// automatically assign the extraDataList values that was added to the extraData object nested within attributesObject.
            ...attributesObject.extraDataList
        }
    };
}
```
### *How to rename property keys in product feeds before the spread operator parses them*
```js
function fixProductAttributes(product) {
	Object.entries(product).forEach(([key, value]) => {
		delete product[key];
		key = key.replace("g:","");
		key = key.replace(/[^a-zA-Z0-9]+/g, '_');
		product[key] = value;

	});
	return product
}

function transform(product:any): TransformationResult {

	fixProductAttributes(product); // replace special characters with underscore for all values, allowing spread operator to auto assign properties.

	return {
		...product,
	};
}
```

### Explicit range filter definitions through singular function with dynamic range definition per use case.
```js
// Determine whether provided value is an array or not. Invoke correct rangeHelper based on type of value.
function rangeHelper(value, conditions, text) {
	if (Array.isArray(value)) {
		return arrayRangeHelper(value, conditions, text);
	}
	else if(typeof value === 'object'){
		return arrayRangeHelper(Object.values(value), conditions, text);
	}
	else {
		return simpleRangeHelper(value, conditions, text);
	}
}

function simpleRangeHelper(value, conditions, text) {
	return conditions.map((condition) => {

		return conditionHandler(value, condition, text);

	}).filter(Boolean) // filter(Boolean to remove unwanted null return values);
}

function arrayRangeHelper(values, conditions, text) {
	return new Set(values.flatMap((value) => {
		return conditions.map((condition) => {

			return conditionHandler(value, condition, text);

		})
	}).filter(Boolean)) // filter(Boolean to remove unwanted null return values);
}

function conditionHandler(value, condition, text) {
	let parsedValue = value;

	if(value && isNaN(value) && typeof value === 'string'){
		let extractedNumbers = value.match(/\d+/g).map(Number);
		parsedValue = extractedNumbers ? Number(extractedNumbers[0]) : null;
	}

	if (parsedValue >= condition.start && (parsedValue <= condition.end || condition.end == 'above')) {
		if (text.pos == 'prefix') {
			if (parsedValue >= condition.start && condition.end == 'above') {
				return `${text.text}${condition.start} - and above`;
			}
			return `${text.text}${condition.start} - ${text.text}${condition.end}`;
		}
		else if (text.pos == 'suffix') {
			if (parsedValue >= condition.start && condition.end == 'above') {
				return `${condition.start}${text.text} - and above`;
			}
			return `${condition.start}${text.text} - ${condition.end}${text.text}`;
		}
		else if (text.pos == null) {
			if (parsedValue >= condition.start && condition.end == 'above') {
				return `${condition.start} - and above`;
			}
			return `${condition.start} - ${condition.end}`;
		}
	}
}

const rangeIndex = {
	price: [
		{ start: 0, end: 25 },
		{ start: 25, end: 50 },
		{ start: 50, end: 100 },
		{ start: 100, end: 200 },
		{ start: 200, end: 300 },
		{ start: 300, end: 400 },
		{ start: 400, end: 500 },
		{ start: 500, end: "above" }
	]
};

function transform(product): TransformationResult {
	return {
		...product,
		extraDataList: {
			priceRanges: rangeHelper(product.price, rangeIndex.price, { pos: "suffix", text: " kr" }),
		}
	};
}

```

### Find the cheapest variant object in an array of variants.
<!-- MDN documentation https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce -->
```json
// Example product.variants data.
{
  "variants": [
    {
      "group": "614",
      "color": "PATINA GREEN",
      "price": 600
    },
    {
      "group": "555",
      "color": "DARK NAVY",
      "price": 504
    },
    {
      "group": "700",
      "color": "BLUE SKY",
      "price": 305
    }
  ]
}
```

```js
function transform(product): TransformationResult {
	return {
		...product,
		extraData: {
            // acc's initial value is the entire object of variants[0].
            // curr's initial value is the entire object of variants[1].
            // acc's price and curr's price are compared to find the smallest one. The one that has the smallest price is assigned as the new acc value.
            // the new acc's price value is then compared to curr's(which is now variants[2]) price value to find the smallest one of these two. The one that has the smallest price is assigned as the new acc value.
            // when the entire variants array has been looped through, the final acc value will be object of the variants array that has the smallest price. This object is then returned to you.
            // With the object that has the smallest price returned to you, you can use object notation to select the price property of the returned object.
            cheapestVariantPrice: product.variants.reduce((acc, curr) => curr.price < acc.price ? curr : acc).price;
        }
	};
}
```

### Automatically spread *all* wooCommerce attributes into extraDataList.
```js
function transform(product: any): TransformationResult {

	const wooCommerceAttributesObject = {};

	if (Array.isArray(product.attributes?.[0].attribute)) {
		const attributeNames = product.attributes?.[0].attribute?.map(attribute => attribute._xmlAttributes.name);

		if (Array.isArray(attributeNames)) {
			attributeNames.forEach((attributeName) => {

				const attribute = new Set(product.attributes?.[0].attribute?.filter(attribute => attribute._xmlAttributes.name === attributeName).map(attribute => attribute.attributeValue));

				wooCommerceAttributesObject[attributeName.replace(/[^a-zA-Z0-9]+/g, '_')] = attribute;
			});
		};
	}
	else if(typeof(product.attributes?.[0].attribute === "Object")){
		wooCommerceAttributesObject[product.attributes?.[0].attribute._xmlAttributes.name.replace(/[^a-zA-Z0-9]+/g, '_')] = Array.isArray(product.attributes?.[0].attribute.attributeValue) ? product.attributes?.[0].attribute.attributeValue : [product.attributes?.[0].attribute.attributeValue];
	}

	return {
		...product,
		extraDataList: {
			...wooCommerceAttributesObject,
		}
	}
}

```

### Automatically spread *all* Shopware properties into extraDataList.
```js
function transform(product: any): TransformationResult {
	if(!product.extraData?.displayGroup) return;

    const shopwarePropertiesObject = {};

    if (product.properties && Array.isArray(product.properties.property)) {
        const propertyNames = product.properties.property.map(property => property.name);
        if (Array.isArray(propertyNames)) {
            propertyNames.forEach((propertyName) => {
                const property = Array.from(new Set(product.properties.property.filter(property => property.name === propertyName).map(property => property.options.option)));
                shopwarePropertiesObject[propertyName.replace(/[^a-zA-Z0-9]+/g, '_').toLowerCase()] = property;
            });
        }
    }
    else if (product.properties && typeof (product.properties.property === "Object")) {
        shopwarePropertiesObject[product.properties.property.name.replace(/[^a-zA-Z0-9]+/g, '_')] = Array.isArray(product.properties.property.options.option) ? product.properties.property.options.option : [product.properties.property.options.option];
    }
    return {
        ...product,
        extraDataList: {
            ...shopwarePropertiesObject
        }
    };
}
```

### Product feed V2 Shopify transform function configuration.
```js
function attributesObjectKeySanitizer(key){
	return key
	.replace(/ø/gi,"oe")
	.replace(/æ/gi,"ae")
	.replace(/å/gi,"aa")
	.replace(/[^a-zA-Z ]/g,"")
	.replace(/\s/g,"_")
}

function transform(product:any): TransformationResult {

	let options = {
		extraData: {},
		extraDataNumber: {},
		extraDataList: {}
	};

	product.options.forEach((option) => {
		if(Array.isArray(option.values)){
			options.extraDataList[attributesObjectKeySanitizer(option.name)] = option.values;
		}
		else if(!isNaN(option.values)){
			options.extraDataNumber[attributesObjectKeySanitizer(option.name)] = option.values;
		}
		else{
			options.extraData[attributesObjectKeySanitizer(option.name)] = option.values;
		}
	});

	return {
		url: `https://www.domain-name.dk/products/${product.handle}`,
		imgUrl: product.main_image?.src,
		title: product.title,
		inStock: product.variants_sellable.length > 0,
		hierarchies: product.hierarchies,
		price: product.main_variant.presentment_prices[0].price?.amount,
		oldPrice: product.main_variant.presentment_prices[0].compare_at_price?.amount,
		productNumber: product.id,
		brand: product.vendor,
		description: product.body_html ? new DOMParser().parseFromString(product.body_html, "text/html").textContent : null,
		extraData: {
			...options.extraData,
			altImage: product.images?.filter(image => image.position == 2)[0]?.src
		},
		extraDataNumber: {
			...options.extraDataNumber
		},
		extraDataList: {
			...options.extraDataList,
			collectionIds: product.collection_ids
		}
	};
}
```

### *How to extract extraattributes from a Magento 2 feed through Feed v2*
- <a href="https://explain.helloretail.com/Wnu8gqjk" target="_blank">Create a temporary XML feed</a>
- <a href="https://explain.helloretail.com/v1u9pLXJ" target="_blank">Rewrite the request url to fetch extra data, and add authorization header</a>
- <a href="https://explain.helloretail.com/Qwuom4Oj" target="_blank">Rewrite the selector path and remove all filter options</a>
