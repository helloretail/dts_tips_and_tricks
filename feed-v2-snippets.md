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
            isNewHours: hoursChecker("2023-05-10"),
            isNewDays: daysChecker(item.date, 30),
        }
    };
};
```

### *Group values*
```js
function coverageChecker(percentage) {
	percentage = parseInt(percentage);
	let value = '';
	if(percentage >= 1 && percentage <= 5) {
		value = '1-5'
	} else if(percentage >= 6 && percentage <= 10) {
		value = '6-10'
	} else if(percentage >= 11 && percentage <= 15) {
		value = '11-15'
	} else if(percentage >= 16 && percentage <= 20) {
		value = '16-20'
	} else if(percentage >= 21 && percentage <= 25) {
		value = '21-25'
	} else if(percentage >= 26 && percentage <= 30) {
		value = '26-30'
	} else if(percentage >= 31 && percentage <= 35) {
		value = '31-35'
	} else if(percentage >= 36 && percentage <= 40) {
		value = '36-40'
	} else if(percentage >= 41 && percentage <= 45) {
		value = '41-45'
	} else if(percentage >= 46 && percentage <= 50) {
		value = '46-50'
	} else if(percentage >= 51 && percentage <= 55) {
		value = '51-55'
	} else if(percentage >= 56 && percentage <= 60) {
		value = '56-60'
	} else if(percentage >= 61 && percentage <= 65) {
		value = '61-65'
	} else if(percentage >= 66 && percentage <= 70) {
		value = '66-70'
	} else if(percentage >= 71 && percentage <= 75) {
		value = '71-75'
	} else if(percentage >= 76 && percentage <= 80) {
		value = '76-80'
	} else if(percentage >= 81 && percentage <= 85) {
		value = '81-85'
	} else if(percentage >= 86 && percentage <= 90) {
		value = '86-90'
	} else if(percentage >= 91 && percentage <= 95) {
		value = '91-95'
	} else if(percentage >= 96 && percentage <= 100) {
		value = '96-100'
	}
	return value;
}
function transform(product:any): TransformationResult {
	return {
		...product,
		priceExVat: product.price_no_tax,
		oldPriceExVat: product.previous_price_no_tax,
		oldPrice: product.previous_price,
		inStock: product.can_be_ordered > 0 ? true : false,
		extraData: {
			coverage: product.variants[0].coverage_percentage,
			coverageTest: coverageChecker(product.variants[0].coverage_percentage)
		}
	};
}
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

### *Rename root level object keys*

```js
function transform(product: any): TransformationResult {

	const sanitizedKeys = Object.fromEntries(
		Object.entries(product).map(([k, v]) => [k.replace(/\-/g, "_").replace(/å/g, "aa").replace(/æ/g, "ae").replace(/ø/g, "oe").replace("type","thisIsNowType"), v])
	);

	return {
		...sanitizedKeys,
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

### *How to extra extraattributes from a Magento 2 feed through Feed v2*
- <a href="https://explain.helloretail.com/Wnu8gqjk" target="_blank">Create a temporary XML feed</a>
- <a href="https://explain.helloretail.com/v1u9pLXJ" target="_blank">Rewrite the request url to fetch extra data, and add authorization header</a>
- <a href="https://explain.helloretail.com/Qwuom4Oj" target="_blank">Rewrite the selector path and remove all filter options</a>
