# **Cheatsheet**
## *FeedStuff*

### *Url selector JS*
```js
- $("html")[0].parentNode.location.href
```
### *Url Selector jQuery*
```js
- $("html").parent()[0].location.href
- $("link[rel='canonical']").attr("href")
- $("meta[property='og:url']").attr("content")
```
### *HTML Unescape*
```js
.unescapeHtml().replace(/<[^>]*>/g, "")
```
### *Based on child element content, select content from parent*
```js
extraDataList.filterSize: $("options > options > name:contains('Størrelse')").parent().find("values[l='1']").fns("text")
```
### *Remove from end of title*
```js
title: $("g\\:title, title").text().split("-")[0]
```
### *extraData to get products on sale*
```js
extraData.isOnSale: [$("main_variant > price").text(),$("main_variant > compare_at_price, main_variant > price").first().text()].subtract().matches(/^-/)
```
### *Percentage of original price*
```js
extraData.percantageOfPrice: [$("sale_price").text(),$("price:last").text()].divide()
```
### *SORT on largest saving*
```js
extraData.saving: [[$("oldPrice").text(),
$("price").first().text()].subtract().multiply(100),
$("oldPrice").text()].divide().round().replace(/^/, "000").replace(/\d*(0\d{2})$/g, "$1")
```
### *Number between 1-100*
```js
.matches(/^[1-9][0-9]?$|^100$/)
```
### *WooCommerce hierarchies to keywords*
```js
keywords: [$("keywords").text(), $("sku:first").text(), $("hierarchies > hierarchy > category").fns("text").join(" ")].join(" ")
```
### *Magento 2 out of stock if price is 0*
```js
inStock: $("price, instock").text().matches(/^[^0].*(true)/)
```
### *DANDOMAIN sort out variants*
```js
$("items > name, isVariantMaster").text().replace(/true$/, "").replace(/.*false$/, "")
```
### *SHOPIFY inStock selector*
```js
inStock: $("inventory_quantity").fns("text").sum().matches(/^[1-9]/)
```
### *SHOPIFY hasVariants selector*
```js
extraData.hasVariants: $("variants > sku").fns("text").fns("replace", /.*/, "1").sum().notMatches(/^1$/)
```
### *SHOPIFY sizes in stock*
```js
extraDataList.sizesInStock: $("items > productvariants available_for_sale:contains(true)").parent().find("selected_options size value").fns("text")
```
### *SHOPIFY ORDER FEED remove if POS*
```js
orderNumber: $("source_name, order_number").fns("text").join("||").replace(/^pos.*/, "").split("||").pop()
```
### *SHOPIFY Add metafields and language data*
```
1. Set the feed to type "Other"
2. Add metafields=true&locale=languageCode like this for example http://feed-	helper.addwish.com/shopify/products.py?metafields=true&locale=nb&domain=artbirdstore&apikey=12345&apipassword=shpat_12345&currency=NOK&extract_variants=
3. Run feed

Some additional info:

If you only need metadata you can leave out the locale part or vice versa.
languageCode is depends on their language. Here the language is Norwegian Bokmål (nb).
You should put these parameters as the first parameters to prevent the feed type from automatically changing to "Shopify".
```
### *Change image size*
```js
.replace(/(\.[a-z]{3,4}\?)/i, "_300x$1")
.replace(/(\.[a-z]{3,4})$/i, "$1.webp")
```
### *Hover image*
```js
{% assign has_hover = false %}
{% if product.extraData.altImg and product.extraData.altImg != "" and product.extraData.altImg != "undefined" and product.extraData.altImg != "null" %}
{% assign has_hover = true %}
{% endif %}
```
### *Adding imageType1 images, with a fallback to imageType2 images if the imageType1 does not exist*
```html
<img src="{{ product.extraData.imageType1 }}" onerror="this.onerror=null;this.src='{{ product.extraData.imageType2 }}';">

An example could be;
imageType1 = http://domain/product/image.webp
imageType2 = http://domain/product/image.png
```
### *Change the image size on SMARTWEB webP*
```js
imgUrl: [$("g\\:image_link").text().replace(/(.*)\/.*$/, "$1"), $("item > g\\:image_link").text().split("/").pop().replace(/^/, "/_thumbs/")].join("").replace(/(.*)\./, "$1.w293.h293.fill.")
```
### *If previous price does not exist on non sale products*
```js
extraData.discountPercentage: [[[$("previousprice").text(), $("price").text()].removeMatching(/^$/).shift(),
                    $("price").text()].subtract().multiply(100),
                    [$("previousprice").text(), $("price").text()].removeMatching(/^$/).shift()].divide().round().replace(/^/, "000").replace(/\d*(0\d{2})$/g, "$1")
```
## *HIERARCHY*
### *Remove from hierarchies*
```js
.fns("removeMatching", /Alla produkter/ig)
.fns("join", "|||").removeMatching(/^home|mÊrker/i).fns("split", "|||")
```
### *MAGENTO 2 Category filter w/o Brands*
```js
extraDataList.categories: $("hierarchies > item").fns("find", "item").fns("fns", "text").fns("join", ">").removeMatching(/^Mærker>/)
```
### *Recom on last hierarchy not first*
```js
extraData.hasMoreThanOneLevel: $(".m-breadcrumb li.page span").fns("text").slice(2).join("||").matches(/.*\|\|.*/)
Switch out the classes and number of slice maybe
```
### *Hierarchy selecter for category pages*
```js
aw$(".breadcrumbs .items .item").fns("text").fns("trim").slice(1).asHierarchy()
Switch out the classes
```
### *Clean out categories in search*
```js
url: $("hasProducts, url").text().replace(/^false.*/, "").replace(/^true/, "")
title: $("isVisibleInMenu, name").fns("text").join("||").replace(/^0.*/, "").split("||").pop()
```
## *RECOMS*
### *VIDEO for customers how to insert divs*
[Shopify](https://explain.helloretail.com/yAuJL7Rr)

[WooCommerce](https://explain.helloretail.com/RBurX5Gg)

### *Recom product title uniform height JS*
```js
on: {
 afterInit: function (e) {
  setTimeout(function(){
   var recomContainerHeight = document.querySelector("#aw-slider-{{ key }}").offsetHeight+"px";
   document.querySelectorAll("#aw-slider-{{ key }} .swiper-slide > .aw-item").forEach(function(slide){
   slide.style.height = recomContainerHeight;
   });
  },200);
 },
},
https://explain.helloretail.com/8LugvLyK
``` 
### *ForLoop for sizes in stock*
```js
{% for size in product.extraDataList.sizes %}
	{% assign index = forloop.index0 %}
	{% if product.extraDataList.stock[index] != "0" %}
		<span>{{ size }}</span>
	{% endif %}
{% endfor %}
```
### *Example of removing category box if filter is active*
```js
document.querySelectorAll(".af_filter_content ul li input[type='checkbox']").forEach(function(item){
    item.addEventListener("click",function(e){
        setTimeout(function(){
            if(window.location.href.includes("?")){
				document.querySelector("#aw-box-{{ key }}").style.display='none';
			}
			else {
				document.querySelector("#aw-box-{{ key }}").style.display='block';
			}
        }, 500)
    })
})
```
### *Category page NO BREADCRUMBS from customer*
```
https://addwish.com/supervisor/app/678784/websites/107e93d5-5ff6-4c75-896a-857b3f5df1b3/designs/edit/135110
```
### *Category page don't show on more than 8 products and show not on filter active*
```css
.catalog-category-view:has(.item:eq(8)):not(:has(.filter-active)) .column.main .category-product-actions
```
### *STRECHED recom fix*
Find customers parent element
Add this to design:
```js
document.querySelector('#aw-box-{{ key }}').style.width=document.querySelector("#Content_Productlist").offsetWidth+"px";
```
### *FREE SHIPPING USA*
```js
var heading = jQuery(".aw-heading"),
basketAmount = parseFloat( jQuery(".cart-total-wrapper .money").text().trim().split("$").pop().trim().replace(",", "") );

if( !isNaN(basketAmount) ) {
    if(basketAmount < 50) {
       var amountLeft = 50 - basketAmount;
       heading.text("Add $"+ amountLeft.toFixed(2).replace(".00", "") +" to reach FREE shipping");
    }
    else {
        heading.html("You've reached FREE shipping! <br>Others also bought");
    }
}
```
### *If shipping price is within the Total Price*
```js
var basketAmount = 0;
	document.querySelectorAll(".webshop-showbasket .product_price").forEach((item)=>{
	console.log(item.textContent.split("NOK").shift().trim());
	basketAmount += parseFloat(item.textContent.split("NOK").shift().trim());
	});
	
	var heading = jQuery(".aw-heading");
    
if( !isNaN(basketAmount) ) {
    if(basketAmount < 199) {
       var amountLeft = 199 - basketAmount;
       heading.text("Kjøp for "+ amountLeft.toFixed(2).replace(".", ",").replace(",00", "") +" kr mer og få fri frakt");
    }
    else {
        heading.text("Du har oppnådd fri frakt! Andre kunder kjøpte også");
    }
}
```
### *Add fade on last product in slider*
```js
.aw-slider-{{ key }} .swiper-slide.swiper-slide-next + .swiper-slide + .swiper-slide {
    opacity: .2;
}
```
### *URL : Display none on recom*
```js
var searchParams = new URLSearchParams(window.location.search);
	console.log(typeof searchParams.get('p'));
	if (searchParams.get('p') !== null){
		document.querySelector("#aw-box-{{ key }}").style.display="none"
	}
```
### *Trigger Upsell Dropdown after page reload*
```js
var viewportWidth = window.matchMedia("(min-width: 700px)");

if (sessionStorage.getItem("buyButtonClicked")) {

    if (sessionStorage.getItem("buyButtonClicked") === "true") {
        setTimeout(function () {
            document.querySelector("#slider-{{ key }}").style.overflow = "visible";
            if (viewportWidth.matches) {
                document.querySelector("#slider-{{ key }}").style.height = "660px";
                document.querySelector("#{{ key }}").style.height = "715px";
            }
            else {
                document.querySelector("#slider-{{ key }}").style.height = "680px";
                document.querySelector("#{{ key }}").style.height = "820px";
            }
            sessionStorage.setItem("buyButtonClicked", "false");
        }, 1000);

    }
}
Array.from(document.querySelectorAll(".button-primary.button-icon")).pop().addEventListener("click", function () {
    sessionStorage.setItem("buyButtonClicked", "true");
});
```
### *Free shipping Price Range Products in Cart*
```
1) Make a selector to grab the cart total: https://explain.helloretail.com/xQuqz0nn (make sure the price is formatted right).
2) Call this variable "price": https://explain.helloretail.com/d5u9NeAY (the reason being, that this treats it as an actual number) in the box-selector-section.
3) Use the variable "price" (which is now the crawled value from the basket page, and not "some value of products"), to set up conditions, like: a) "if below free shipping, do X", "if above, do Y".
4) Use this, in the first section (where the price (value of basket) is still below free shipping), to specify, that the products found in this step, have a certain price-range:
 
$price|freeShipping:495, 100
The explanation of this would be:
$price(this is still the crawled value of the basket-total)|freeShipping:495(here you set the treshold for when free shipping has been applied), 100(this is the margin, with witch the price are allowed to go the range).
So put into a concrete example this:
(200|freeShipping:495, 100)
would mean "show products, that have a price between 195 and 395".
like so: https://explain.helloretail.com/BluodB1q - the behaviour on the page should be like: https://explain.helloretail.com/6quZEB8k
```
## *SEARCH*
### *MOBILE GRID SEARCH*
```css
1. .hr-tab-wrapper {
	display: flex;
	flex-wrap: wrap;
	justify-content: center;
}
.hr-tab-body[data-tab='category'] .hr-tab-wrapper {
	display: block;
}
2. .hr-overlay-search .hr-search-overlay-product {
    padding: 10px;
    position: relative;
    background-color: #ffffff;
    border-radius: 12px;
    -webkit-border-radius: 12px;
    -moz-border-radius: 12px;
    flex-basis: 42%;
    flex-direction: column;
    display: flex;
    margin: 5px auto;
}
3. .hr-row {
	display: flex;
	flex-direction: column;
	width: 100%;
}
4. .hr-col-img {
	flex-basis: 120px;
}
5. .hr-overlay-search > .hr-tabs > .hr-tab-content .hr-tab-header {
	flex-basis: 100%;
}
```
### *Add redirects in grid search*
```js
// Add to the top
import "search_redirects";

// Add above engine_options
document.querySelector(".aw-search-input").addEventListener('keyup', (event) => {
    search_redirects.match_and_go(event.target.value, key)
});
```
### *Filter Sorting DESKTOP by [Mikkel](https://github.com/Mikkelmbk)*
```js
// Function
/* text */ var desktop_default_filters_to_sort = ""; // =asc or =desc to control the individual filters sorting directive.
/* text */ var desktop_extraData_filters_to_sort = "length,buckle,width=desc"; // =asc or =desc to control the individual filters sorting directive.
/* text */ var desktop_extraDataList_filters_to_sort = ""; // =asc or =desc to control the individual filters sorting directive.
/* text */ var desktop_add_to_assorted_filters_list = ""; // add label names here in the exact order that they should be sorted in (this will priority sort the provided label names and won't be sorted numerically or alphabetical).
desktop_default_filters_to_sort = desktop_default_filters_to_sort.split(",");
desktop_extraData_filters_to_sort = desktop_extraData_filters_to_sort.split(",");
desktop_extraDataList_filters_to_sort = desktop_extraDataList_filters_to_sort.split(",");
desktop_add_to_assorted_filters_list = desktop_add_to_assorted_filters_list.toLowerCase().split(",");
/* boolean */ var desktop_ascending_filter_sorting = true; // global sorting directive used if no =asc / =desc sorting directive is present for the individual filter.
/* boolean */ var desktop_numbers_first_in_filters = true; // control whether the labels considered to be numerical values should be shown before or after the alphabetical labels.
/* boolean */ var desktop_console_debug = false;
/* text */ var desktop_filter_title_start_with_number_or_contain_number_or_only_number = "start_with_number".toLowerCase(); // start_with_number / contain_number / only_number: control when a the labels in filter dropdowns should be considered numerical.
function sortFiltersDesktop(parentClass, sortingDirective) {
    console.time(`${parentClass} filtering took`);
    var numDesktop = [];
    var alphDesktop = [];
    var assortedDesktop = [];
    if (!document.querySelector(parentClass)) {
        if (desktop_console_debug) {
            console.log(`"${parentClass}" is not a valid filter selector and will not be sorted.`);
        }
        return;
    }
    const parentDesktop = document.querySelector(parentClass);
    var divsDesktop;

    sortingDirective ? parentDesktop.setAttribute("sorting-directive", sortingDirective) : parentDesktop.setAttribute("sorting-directive", "default");
    parentClass.includes("hierarchies") ? divsDesktop = [...parentDesktop.querySelectorAll(`${parentClass} li`)] : divsDesktop = [...parentDesktop.querySelectorAll(`${parentClass} label`)];

    divsDesktop.forEach(function (label) {
        var titleDesktop = label.querySelector(".aw-filter-tag-title").textContent.trim();
        if (desktop_add_to_assorted_filters_list != "") {
            var index = desktop_add_to_assorted_filters_list.indexOf(titleDesktop.toLowerCase());
            if (index != -1) {
                label.setAttribute("user-sorting", index);
            }
        };
        if (label.hasAttribute("user-sorting")) {
            assortedDesktop.push(label);
        }
        else if ((titleDesktop.match(/[^\d]*(\d+).*/) && desktop_filter_title_start_with_number_or_contain_number_or_only_number == "contain_number") || (titleDesktop.match(/^(\d+).*/) && desktop_filter_title_start_with_number_or_contain_number_or_only_number == "start_with_number") || (titleDesktop.match(/^\d+$/) && desktop_filter_title_start_with_number_or_contain_number_or_only_number == "only_number")) {
            if (desktop_filter_title_start_with_number_or_contain_number_or_only_number == "start_with_number") {
                label.setAttribute("num-sorting", titleDesktop.replace(/^(\d+).*/, "$1"));
                numDesktop.push(label);
            }
            else if (desktop_filter_title_start_with_number_or_contain_number_or_only_number == "contain_number") {
                label.setAttribute("num-sorting", titleDesktop.replace(/[^\d]*(\d+).*/, "$1"));
                numDesktop.push(label);
            }
            else {
                label.setAttribute("num-sorting", titleDesktop);
                numDesktop.push(label);
            }
        }
        else {
            label.setAttribute("alph-sorting", titleDesktop.replace(/\d+/, ""));
            alphDesktop.push(label);
        }
    });

    numDesktop.sort(function (a, b) {
        if (sortingDirective) {
            if (sortingDirective == "asc") {
                return a.getAttribute("num-sorting") - b.getAttribute("num-sorting");
            }
            else {
                return b.getAttribute("num-sorting") - a.getAttribute("num-sorting");
            }
        }
        else {
            if (desktop_ascending_filter_sorting) {
                return a.getAttribute("num-sorting") - b.getAttribute("num-sorting");
            }
            else {
                return b.getAttribute("num-sorting") - a.getAttribute("num-sorting");
            }
        }
    });
    alphDesktop.sort(function (a, b) {
        if (sortingDirective) {
            if (sortingDirective == "asc") {
                return (a.getAttribute("alph-sorting").toLowerCase() > b.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
            else {
                return (b.getAttribute("alph-sorting").toLowerCase() > a.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
        }
        else {
            if (desktop_ascending_filter_sorting) {
                return (a.getAttribute("alph-sorting").toLowerCase() > b.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
            else {
                return (b.getAttribute("alph-sorting").toLowerCase() > a.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
        }
    });
    assortedDesktop.sort(function (a, b) {
        if (sortingDirective) {
            if (sortingDirective == "asc") {
                return a.getAttribute("user-sorting") - b.getAttribute("user-sorting");
            }
            else {
                return b.getAttribute("user-sorting") - a.getAttribute("user-sorting");
            }
        }
        else {
            if (desktop_ascending_filter_sorting) {
                return a.getAttribute("user-sorting") - b.getAttribute("user-sorting");
            }
            else {
                return b.getAttribute("user-sorting") - a.getAttribute("user-sorting");
            }
        }
    });
    if (desktop_numbers_first_in_filters) {
        assortedDesktop.forEach(function (div) { parentDesktop.append(div); });
        numDesktop.forEach(function (div) { parentDesktop.append(div); });
        alphDesktop.forEach(function (div) { parentDesktop.append(div); });
    }
    else {
        assortedDesktop.forEach(function (div) { parentDesktop.append(div); });
        alphDesktop.forEach(function (div) { parentDesktop.append(div); });
        numDesktop.forEach(function (div) { parentDesktop.append(div); });
    }
    console.timeEnd(`${parentClass} filtering took`);
}

// Invocation
if (desktop_default_filters_to_sort != "") {
    desktop_default_filters_to_sort.forEach(function (filter) {
        var sortingDirective = filter.includes("=") ? filter.split("=").pop() : false;
        var filter = filter.includes("=") ? filter.split("=").shift() : filter;
        var filterStructured = `[data-filter='${filter}']`;
        filter == "hierarchies" ? sortFiltersDesktop(`${filterStructured} .aw-filter-list`, sortingDirective) : sortFiltersDesktop(`${filterStructured} .aw-filter-tag-list`, sortingDirective);
    });
}

if (desktop_extraData_filters_to_sort != "") {
    desktop_extraData_filters_to_sort.forEach(function (filter) {
        var sortingDirective = filter.includes("=") ? filter.split("=").pop() : false;
        var filter = filter.includes("=") ? filter.split("=").shift() : filter;
        var filterStructured = `[data-filter='extraData.${filter}']`;
        sortFiltersDesktop(`${filterStructured} .aw-filter-tag-list`, sortingDirective);
    });
}

if (desktop_extraDataList_filters_to_sort != "") {
    desktop_extraDataList_filters_to_sort.forEach(function (filter) {
        var sortingDirective = filter.includes("=") ? filter.split("=").pop() : false;
        var filter = filter.includes("=") ? filter.split("=").shift() : filter;
        var filterStructured = `[data-filter='extraDataList.${filter}']`;
        sortFiltersDesktop(`${filterStructured} .aw-filter-tag-list`, sortingDirective);
    });
}

###Positioning

Function : https://explain.helloretail.com/X6uR6R0w
Invocation : https://explain.helloretail.com/L1uXwXNv
```
### *Sort filters MOBILE by [Mikkel](https://github.com/Mikkelmbk)*
```js
// Function
/* text */ var mobile_default_filters_to_sort = "brand,hierarchies";
/* text */ var mobile_extraData_filters_to_sort = "";
/* text */ var mobile_extraDataList_filters_to_sort = "size=desc,color=asc";
/* text */ var mobile_add_to_assorted_filters_list = "36,42";
mobile_default_filters_to_sort = mobile_default_filters_to_sort.split(",");
mobile_extraData_filters_to_sort = mobile_extraData_filters_to_sort.split(",");
mobile_extraDataList_filters_to_sort = mobile_extraDataList_filters_to_sort.split(",");
mobile_add_to_assorted_filters_list = mobile_add_to_assorted_filters_list.toLowerCase().split(",");
/* boolean */ var mobile_ascending_filter_sorting = true;
/* boolean */ var mobile_numbers_first_in_filters = true;
/* boolean */ var mobile_console_debug = false;
/* text */ var mobile_filter_title_start_with_number_or_contain_number_or_only_number = "only_number".toLowerCase();
function sortFiltersMobile(parentClass, sortingDirective) {
    var numMobile = [];
    var alphMobile = [];
    var assortedMobile = [];
    if (!document.querySelector(parentClass)) {
        if (mobile_console_debug) {
            console.log(`"${parentClass}" is not a valid filter selector and will not be sorted.`);
        }
        return;
    }
    const parentMobile = document.querySelector(parentClass);
    var divsMobile;

    sortingDirective ? parentMobile.setAttribute("sorting-directive", sortingDirective) : parentMobile.setAttribute("sorting-directive", "default");
    parentClass.includes("hierarchies") ? divsMobile = [...parentMobile.querySelectorAll(`${parentClass} li`)] : divsMobile = [...parentMobile.querySelectorAll(`${parentClass} label`)];

    divsMobile.forEach(function (label) {
        var titleMobile = label.querySelector(".aw-filter-tag-title").textContent.trim();
        if (mobile_add_to_assorted_filters_list != "") {
            var index = mobile_add_to_assorted_filters_list.indexOf(titleMobile.toLowerCase());
            if (index != -1) {
                label.setAttribute("user-sorting", index);
            }
        };
        if (label.hasAttribute("user-sorting")) {
            assortedMobile.push(label);
        }
        else if ((titleMobile.match(/[^\d]*(\d+).*/) && mobile_filter_title_start_with_number_or_contain_number_or_only_number == "contain_number") || (titleMobile.match(/^(\d+).*/) && mobile_filter_title_start_with_number_or_contain_number_or_only_number == "start_with_number") || (titleMobile.match(/^\d+$/) && mobile_filter_title_start_with_number_or_contain_number_or_only_number == "only_number")) {
            if (mobile_filter_title_start_with_number_or_contain_number_or_only_number == "start_with_number") {
                label.setAttribute("num-sorting", titleMobile.replace(/^(\d+).*/, "$1"));
                numMobile.push(label);
            }
            else if (mobile_filter_title_start_with_number_or_contain_number_or_only_number == "contain_number") {
                label.setAttribute("num-sorting", titleMobile.replace(/[^\d]*(\d+).*/, "$1"));
                numMobile.push(label);
            }
            else {
                label.setAttribute("num-sorting", titleMobile);
                numMobile.push(label);
            }
        }
        else {
            label.setAttribute("alph-sorting", titleMobile.replace(/\d+/, ""));
            alphMobile.push(label);
        }
    });

    numMobile.sort(function (a, b) {
        if (sortingDirective) {
            if (sortingDirective == "asc") {
                return a.getAttribute("num-sorting") - b.getAttribute("num-sorting");
            }
            else {
                return b.getAttribute("num-sorting") - a.getAttribute("num-sorting");
            }
        }
        else {
            if (mobile_ascending_filter_sorting) {
                return a.getAttribute("num-sorting") - b.getAttribute("num-sorting");
            }
            else {
                return b.getAttribute("num-sorting") - a.getAttribute("num-sorting");
            }
        }
    });
    alphMobile.sort(function (a, b) {
        if (sortingDirective) {
            if (sortingDirective == "asc") {
                return (a.getAttribute("alph-sorting").toLowerCase() > b.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
            else {
                return (b.getAttribute("alph-sorting").toLowerCase() > a.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
        }
        else {
            if (mobile_ascending_filter_sorting) {
                return (a.getAttribute("alph-sorting").toLowerCase() > b.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
            else {
                return (b.getAttribute("alph-sorting").toLowerCase() > a.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
        }
    });
    assortedMobile.sort(function (a, b) {
        if (sortingDirective) {
            if (sortingDirective == "asc") {
                return a.getAttribute("user-sorting") - b.getAttribute("user-sorting");
            }
            else {
                return b.getAttribute("user-sorting") - a.getAttribute("user-sorting");
            }
        }
        else {
            if (mobile_ascending_filter_sorting) {
                return a.getAttribute("user-sorting") - b.getAttribute("user-sorting");
            }
            else {
                return b.getAttribute("user-sorting") - a.getAttribute("user-sorting");
            }
        }

    });
    if (mobile_numbers_first_in_filters) {
        assortedMobile.forEach(function (div) { parentMobile.append(div); });
        numMobile.forEach(function (div) { parentMobile.append(div); });
        alphMobile.forEach(function (div) { parentMobile.append(div); });
    }
    else {
        assortedMobile.forEach(function (div) { parentMobile.append(div); });
        alphMobile.forEach(function (div) { parentMobile.append(div); });
        numMobile.forEach(function (div) { parentMobile.append(div); });
    }
}

// Invocation
if (mobile_default_filters_to_sort != "") {
    mobile_default_filters_to_sort.forEach(function (filter) {
        var sortingDirective = filter.includes("=") ? filter.split("=").pop() : false;
        var filter = filter.includes("=") ? filter.split("=").shift() : filter;
        var filterStructured = `.hr-search-overlay-filter-wrap[data-filter='${filter}']`;
        filter == "hierarchies" ? sortFiltersMobile(`${filterStructured} .aw-filter-list`, sortingDirective) : sortFiltersMobile(`${filterStructured} .aw-filter-tag-list`, sortingDirective);
    });
}

if (mobile_extraData_filters_to_sort != "") {
    mobile_extraData_filters_to_sort.forEach(function (filter) {
        var sortingDirective = filter.includes("=") ? filter.split("=").pop() : false;
        var filter = filter.includes("=") ? filter.split("=").shift() : filter;
        var filterStructured = `.hr-search-overlay-filter-wrap[data-filter='extraData.${filter}']`;
        sortFiltersMobile(`${filterStructured} .aw-filter-tag-list`, sortingDirective);
    });
}

if (mobile_extraDataList_filters_to_sort != "") {
    mobile_extraDataList_filters_to_sort.forEach(function (filter) {
        var sortingDirective = filter.includes("=") ? filter.split("=").pop() : false;
        var filter = filter.includes("=") ? filter.split("=").shift() : filter;
        var filterStructured = `.hr-search-overlay-filter-wrap[data-filter='extraDataList.${filter}']`;
        sortFiltersMobile(`${filterStructured} .aw-filter-tag-list`, sortingDirective);
    });
}

Function : https://explain.helloretail.com/xQuw5wvd
Invocation 1 : https://explain.helloretail.com/7Ku6r6R6
Invocation 2 : https://explain.helloretail.com/JruoBovz
```
### *Filter Sorting PAGES by [Mikkel](https://github.com/Mikkelmbk)*
```js
// Function
var pages_default_filters_to_sort = "Mærke,Størrelse,Farve";
var pages_add_to_assorted_filters_list = "ONE SIZE,XXS,XXS/XS,XS,XS/S,S,S/M,M,M/L,L,L/XL,XL,XXL,3XL";
pages_default_filters_to_sort = pages_default_filters_to_sort.split(",");
pages_add_to_assorted_filters_list = pages_add_to_assorted_filters_list.toLowerCase().split(",");
var pages_ascending_filter_sorting = true;
var pages_numbers_first_in_filters = true;
var pages_console_debug = true;
var pages_filter_title_start_with_number_or_contain_number_or_only_number = "start_with_number".toLowerCase();
function sortFiltersPages(parentClass, sortingDirective) {
    var numPages = [];
    var alphPages = [];
    var assortedPages = [];
    if (!document.querySelector(parentClass)) {
        if (pages_console_debug) {
            console.log(`"${parentClass}" is not a valid filter selector and will not be sorted.`);
        }
        return;
    }
    const parentPages = document.querySelector(parentClass);
    var divsPages;

    sortingDirective ? parentPages.setAttribute("sorting-directive", sortingDirective) : parentPages.setAttribute("sorting-directive", "default");
    parentClass.includes("Kategorier") ? divsPages = [...parentPages.querySelectorAll(`${parentClass} li`)] : divsPages = [...parentPages.querySelectorAll(`${parentClass} label`)];

    divsPages.forEach(function (label) {
        var titlePages = label.querySelector(".aw-filter-tag-title").textContent.trim();
        if (pages_add_to_assorted_filters_list != "") {
            var index = pages_add_to_assorted_filters_list.indexOf(titlePages.toLowerCase());
            if (index != -1) {
                label.setAttribute("user-sorting", index);
            }
        };
        if (label.hasAttribute("user-sorting")) {
            assortedPages.push(label);
        }
        else if ((titlePages.match(/[^\d]*(\d+).*/) && pages_filter_title_start_with_number_or_contain_number_or_only_number == "contain_number") || (titlePages.match(/^(\d+).*/) && pages_filter_title_start_with_number_or_contain_number_or_only_number == "start_with_number") || (titlePages.match(/^\d+$/) && pages_filter_title_start_with_number_or_contain_number_or_only_number == "only_number")) {
            if (pages_filter_title_start_with_number_or_contain_number_or_only_number == "start_with_number") {
                label.setAttribute("num-sorting", titlePages.replace(/^(\d+).*/, "$1"));
                numPages.push(label);
            }
            else if (pages_filter_title_start_with_number_or_contain_number_or_only_number == "contain_number") {
                label.setAttribute("num-sorting", titlePages.replace(/[^\d]*(\d+).*/, "$1"));
                numPages.push(label);
            }
            else {
                label.setAttribute("num-sorting", titlePages);
                numPages.push(label);
            }
        }

        else {
            label.setAttribute("alph-sorting", titlePages.replace(/\d+/, ""));
            alphPages.push(label);
        }
    });

    numPages.sort(function (a, b) {
        if (sortingDirective) {
            if (sortingDirective == "asc") {
                return a.getAttribute("num-sorting") - b.getAttribute("num-sorting");
            }
            else {
                return b.getAttribute("num-sorting") - a.getAttribute("num-sorting");
            }
        }
        else {
            if (pages_ascending_filter_sorting) {
                return a.getAttribute("num-sorting") - b.getAttribute("num-sorting");
            }
            else {
                return b.getAttribute("num-sorting") - a.getAttribute("num-sorting");
            }
        }

    });
    alphPages.sort(function (a, b) {
        if (sortingDirective) {
            if (sortingDirective == "asc") {
                return (a.getAttribute("alph-sorting").toLowerCase() > b.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
            else {
                return (b.getAttribute("alph-sorting").toLowerCase() > a.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
        }
        else {
            if (pages_ascending_filter_sorting) {
                return (a.getAttribute("alph-sorting").toLowerCase() > b.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
            else {
                return (b.getAttribute("alph-sorting").toLowerCase() > a.getAttribute("alph-sorting").toLowerCase() ? 1 : -1);
            }
        }

    });
    assortedPages.sort(function (a, b) {
        if (sortingDirective) {
            if (sortingDirective == "asc") {
                return a.getAttribute("user-sorting") - b.getAttribute("user-sorting");
            }
            else {
                return b.getAttribute("user-sorting") - a.getAttribute("user-sorting");
            }
        }
        else {
            if (pages_ascending_filter_sorting) {
                return a.getAttribute("user-sorting") - b.getAttribute("user-sorting");
            }
            else {
                return b.getAttribute("user-sorting") - a.getAttribute("user-sorting");
            }
        }

    });
    if (pages_numbers_first_in_filters) {
        assortedPages.forEach(function (div) { parentPages.append(div); });
        numPages.forEach(function (div) { parentPages.append(div); });
        alphPages.forEach(function (div) { parentPages.append(div); });
    }
    else {
        assortedPages.forEach(function (div) { parentPages.append(div); });
        alphPages.forEach(function (div) { parentPages.append(div); });
        numPages.forEach(function (div) { parentPages.append(div); });
    }
}

// Invocation
if (pages_default_filters_to_sort != "") {
    pages_default_filters_to_sort.forEach(function (filter) {
        var sortingDirective = filter.includes("=") ? filter.split("=").pop() : false;
        var filter = filter.includes("=") ? filter.split("=").shift() : filter;
        var filterStructured = `[data-filter-name='${filter}']`;
        filter == "Kategorier" ? sortFiltersPages(`${filterStructured} .aw-filter-list`, sortingDirective) : sortFiltersPages(`${filterStructured} .aw-filter-tag-list`, sortingDirective);
    });
}

// Function position: https://explain.helloretail.com/P8uQo1AR.
// Invocation position: https://explain.helloretail.com/Wnu7vyO1.
```
### *Hide/Show filter based on input*
```js
// Placement of snippet: last line in the function named "load_more_results"
// Notes: This works with Overlay out of the box. Just rename the data-filter to the given filter, and the matched value of the input-field to trigger the filter.
var inputValue = document.querySelector(".hr-search > input").value
var mistralFilterContainer = document.querySelector(".aw-filter__single-wrapper[data-filter='extraData.filterName']");

if (document.querySelector(".hr-search > input").value.match(/.*TRIGGERWORD.*/i)) {
    mistralFilterContainer.style.display = "block";
} else {
    mistralFilterContainer.style.display = "none";
}
```
### *Remove unwanted sorting ascending or descending values*
```js
// (Important → the value which should be removed should be named "THISSHOULDBEREMOVED" in the ascending or descending title)

// Example - Line 135 JavaScript Estella.nl

document.querySelectorAll(".aw-sorting-tag-list label").forEach(function(item){
    if(item.textContent == "THISSHOULDBEREMOVED"){
        item.remove();
    }
})
```
### *Remove customers page scroll when Embedded search is open*
```css
.hr-search-disable-scroll{
    height:100vh!important;
    width:100vw!important;
    overflow:hidden!important;
}
```
### *Set up instant grid search*
change the "var input" to the right class
.attr("action", "/pages/search-results")
Then in the HTML :
```html
<div class="aw-grid-search-results__submit-wrapper">
	<a href="https://shenanigans.toys/pages/search-results?q={{query}}" class="aw-grid-search-results__submit-link">
		{{ show_more_results_text }} ({{ products.totalResults }})
	</a>
</div>
```
### *Fix scroll in search*
```css
    max-height: 100vh;
    verflow: scroll;
```
### *Hide scrollbar from Embedded searches that are narrower than the viewport width*
```css
.hr-overlay-search::-webkit-scrollbar {
    width: 0 !important;
}
```
### *Add filters in full search*
```
dubuy.dk, from all templates!!
```
### *Remove CROSS in desktop search*
```css
input[type=search]::-ms-clear { 
	display: none; 
	width : 0; 
	height: 0; 
}
input[type=search]::-ms-reveal { 
	display: none; 
	width : 0; 
	height: 0; 
}
input[type="search"]::-webkit-search-decoration,
input[type="search"]::-webkit-search-cancel-button,
input[type="search"]::-webkit-search-results-button,
input[type="search"]::-webkit-search-results-decoration { 
	display: none; 
}
```
### *Reset form event listener SEARCH*
```js
var searchBtn = document.querySelector(".header-search");
searchBtn.replaceWith(searchBtn.cloneNode(true));
```
### *Add hierachypath to category content template*
```html
<span class="hr-search-overlay-content-hierarchy-wrapper">
    {% for level in ctnt.hierarchy limit : 1 %}
    {% if level != "" and level != current_content_item.title %}
    <span class="hr-search-overlay-content-category-hierarchy-category">{{ level }}</span>
    {% unless forloop.last %}
    <span class="hr-search-overlay-content-hierarchy-divider">›</span>
    {% endunless %}
    {% endif %}
    {% endfor %}
</span>
```
(Remove limit : 1, to show all hierachies)
```css
.hr-search-overlay-content-hierarchy-wrapper {
    display: block;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
    font-size: .9em;
    color: #777;
}
```
https://explain.helloretail.com/DOum40Bd

## *PAGES*
### *SHOPIFY Insert div*
```
Online store → Themes → Debut tab → Actions → Edit code → Layout folder → Collection-template.liquid → insert div right above own products container

https://explain.helloretail.com/jkuX7JlW
```
### *SHOPIFY Automatic sorting on newest by date*
```js
document.querySelector("label input[value='created desc']").parentElement.style.order = -1;

	if( window.location.pathname === '/collections/nyheder' || window.location.pathname === '/collections/nyheder-women') {
		if( $(".aw-sorting-tag-list label.selected:has(input[value='created desc'])").length === 0 && $(".aw-sorting-tag-list label.selected").length === 0 ) {
			$(".aw-sorting-tag-list label:has(input[value='created desc'])").trigger("click");
		}
	}
```
### *Test pages div*
```js
let html = `<div id="helloretail-category-page-624421fb2d64b76dbff35627" data-filters=" { &apos;hierarchies&apos;: [&apos;Overlocker&apos;, &apos;Bernina Overlocker&apos;] } "></div>`

document.querySelector("SELECTOR TO BE POSITIONED RELATIVE TO").insertAdjacentHTML("beforebegin",html);
```

The &apos; represents a literal quote: '
But has to remain  &apos; in order for the html to recognize and format it correctly.

Once above code has been run in the console of the browser, run the following piece of code (WHILE THE PAGES DRAFT IS ENABLED)
```js
ADDWISH_PARTNER_NS.api.reload()
```
### *Hide irrelevant filters*

Example: Pandasia line 127 - 145 Pages Javascript.
```js
if(document.querySelectorAll("div[data-filter-name='Tilbud'] .aw-filter-tag-list label").length == "1"){
    document.querySelector(".aw-filter__single-wrapper[data-filter-name='Tilbud']").remove()
};
```
### *Remove customers product list*
```js
var custom_product_count = content.products.count;
if(custom_product_count > 0){
	if(document.querySelector("#maincontent .column.main #layer-product-list")){
		document.querySelector("#maincontent .column.main #layer-product-list").remove();
	}
}
```
### *Structuring extraDataList in Pages data-filters attribute*
```html
data-filters=' { "extraDataList.categoryIds": "619" }'
```
### *Look for elements several times until found or until cap of attempts reached*
```js
var timeCount = 0;
var timeout = setInterval(() => {
    timeCount += 1;
    if (timeCount >= 5) {
        clearInterval(timeout);

    }
    if (document.querySelector(".category-subheading") && Array.from(document.querySelectorAll(".m-breadcrumb li a span")).pop()) {
        clearInterval(timeout); 
Do something if elements have been found
    }
}, 100);
```
### *How to structure a Pages REST-API request url*
```js
fetch("https://core.helloretail.com/serve/pages/{key}", {
  "method": "POST",
  "mode": "cors",
  "credentials": "include",
    "headers":{
       'Content-Type':'application/json'
    },
    "body":JSON.stringify({
        "id":"{pages id}",
        "url":"{url to category page}",
        "layout":"true",
        "firstLoad":"true",
	"format":'json',
        "products":{
            "start":0,
            "count":4000,
            "filters":[
                "extraDataList.colorFilter:Silver",
                "price":"100,500",
                "brand":"nike"
                
            ],
            "sorting":[
                "title desc"
            ],
            "params":{
                "filters":"{'hierarchies':['Strömbrytare och vägguttag']}"
            },
            "trackingUserId":"{hello_retail_id cookie}"
        }
    })
}).then((res)=>{ return res.json() }).then((data)=>{ console.log(data) });
```
### *How to structure a TrackingUserId REST-API request url*
```js
fetch('https://core.helloretail.com/serve/trackingUser',{
    method: "POST", 
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
        "websiteUuid": "6a3b6823-a7d9-429c-95fe-b34cff1548bd"
    }), 
}).then((res) => {
    return res.json();
}).then((data) => {
    console.log(data)
});
```
### *How to structure a Page-view REST-API request url*
```js
fetch('https://core.helloretail.com/serve/collect/pageview',{
    method: "POST", 
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
    "location": "https://b2b.jbs.dk/da/",
    "trackingUserId": "6426929526c7b13a8a1566f9",
    "websiteUuid": "6a3b6823-a7d9-429c-95fe-b34cff1548bd",
    "referrer": "https://b2b.jbs.dk/da/",
    "url": "https://b2b.jbs.dk/da/produkt/023008750000000000101",
    "productNumber": "0230087500000000001"
}), 
}).then((res) => {
    return res.json();
}).then((data) => {
    console.log(data)
});
```
### *How to structure a Click REST-API request url*
```js
fetch('https://core.helloretail.com/serve/collect/click',{
    method: "POST", 
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
    "trackingUserId": "6426929526c7b13a8a1566f9",
    "websiteUuid": "6a3b6823-a7d9-429c-95fe-b34cff1548bd",
    "source": "32ef3ce4-19c6-4469-a69f-bb1ce3b450f5"
}), 
}).then((res) => {
    return res.json();
}).then((data) => {
    console.log(data)
});
```
## *RANDOM*
### *LipScore Rating*
```
https://explain.helloretail.com/WnuDrbGL
https://explain.helloretail.com/X6uQdooR
```
### *Show different labels depending on day*
```js
var weekday = ["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"];

var date = new Date();
var day = weekday[date.getDay()];

console.log(day)

if(day == 'Friday' || 'Saturday' || 'Sunday'){
 console.log("Weekend ")
} else {
console.log("Workday")
}
```
### *Enable debugging messages in console*
```js
ADDWISH_PARTNER_NS.util.set_cookie("addwish-debug","true")
```
### *Check click event*
```js
window.addEventListener("click", function(e){console.log(e.target)})
```
### *Set Timeout*
```js
setTimeout(function() {
    // *Add code here*
} ,1000)
```
### *JS Media Query*
```js
if(window.matchMedia("(max-width: 960px)")){
	// Don't activate on mobile
	return;
}
```
### *Add getCookie method to window object*
```js
if(!window.getCookie){
     window.getCookie = function(name) {
          var match = document.cookie.match(new RegExp('(^| )' + name + '=([^;]+)'));
          if (match) return match[2];
     }
}
```
### *How to add input fields to triggered email designs*

https://addwish.com/supervisor/triggeredemail/designEdit.html?designId=8924
```html
{# text priceColor = "#CA0303" #}

<h3 style="color:{{ priceColor }};font-weight:700;"></h3>
```
### *Make a diagonal line-through*
```css
.aw-oldPrice{
	position: relative;
}
.aw-line {
	position: absolute;
    width: 80%;
    left: 20px;
    height: 1px;
    border: none;
    background: #185398;
    transform: rotate(342deg) translate(-50%, -50%);
}
```
### *Make different shapes with Clip path*
```
https://bennettfeely.com/clippy/
```
### *HTML Symbols*
```
https://www.toptal.com/designers/htmlarrows/arrows/
```
### *IP for whitelist*
```
46.137.110.51
```
### *Mutation Observer example*
```js
var awHeading = document.querySelector(".aw-heading");
var basketAmount = parseFloat(document.querySelector("#CartTotal strong").textContent.trim().split("kr").shift().trim().replace(".", "").replace(",", "."));

updateAwHeading();
const observer = new MutationObserver(function (mutationsList, observer) {
    for (const mutation of mutationsList) {
        if (mutation.type === "childList") {
            mutation.addedNodes.forEach(function (item) {
                if (item.nodeName === "#text") {
                    updateAwHeading();
                }
            })
        }
    }
});
observer.observe(document.querySelector("#AjaxCartSubtotal"), { childList: true, subtree: true });

function updateAwHeading() {
    basketAmount = parseFloat(document.querySelector("#CartTotal strong").textContent.trim().split("kr").shift().trim().replace(".", "").replace(",", "."));
    if (!isNaN(basketAmount)) {
        if (basketAmount < 500) {
            var amountLeft = 500 - basketAmount;
            awHeading.textContent = "Køb for " + amountLeft.toFixed(2).replace(".", ",").replace(",00", "") + " kr mere og få gratis fragt";
        }
        else {
            awHeading.textContent = "Du har opnået fri fragt! Andre kunder købte også";
        }
    }
}
```
## *Regex ([Victor](https://github.com/VictorPersson) edition)*
### *Remove HTML from string*
```js
    .replace(/<[^>]*>/g, "")
```

### *Does string contain anything? (Ex are there variants? See Boldliving.se)*
```js
.matches(/^([\s\S])/)
```

### *Grab the value of a matching attribute (live on performancegear.dk):*
```js 
extraDataList.colors: $("attributes > attribute[name=pa_color] > attributeValue").fns("text")
```

### *Grab a value from a heriarchy, by checking if it contains string (live on luksusbaby.dk )*
```js
extraData.extraBrand: $("hierarchies[l='1']:contains(Mærker) > :last").text()
```
### *Use a stock-image IF NO IMAGE available (live on ljusbutiken.se)* 
```js
imgUrl: $("g\\:image_link").text().replace("", "https://www.ljusbutik.se/sv/images/image-missing-12.png|").split("|").pop()
```

### *Add something if the field is empty, ex placeholder IMG.*
```js
imgUrl: $("imgurl").text().replace(/^$/, "https://shop11399.hstatic.dk/upload_dir/pics/placeholders/aa2c4d4c6f4b2e03ed8ea29601d848d4.png")
```

### *Replace image size, mainly Shopify, but could be refitted for other feeds.*
```js
.replace(/(\.[a-z]{3,4}\?)/i, "_300x$1")
```
Way better than using
```js
// DO NOT USE -> .replace(".jpg", "_300x.jpg")
```

### *Remove URL if other selector is certain word*
```js
url: $("hierarchies > hierarchy > category:contains(Uncategorized), url").text().replace(/^Uncategorized.*/, "")
```
Alt, better regex way. If looking for 1 letter or number
```js
url: $("price, url").text().replace(/^0.*/, "").replace(/.*(http)/, "$1")
```

### *Remove everything from an array that **isn't/don't** match this:* 
```js
.removeMatching(/^(?!steel||titanium|titan|).*/gi)
```

### *[Managed Fetch Recommendation API request example](https://developer.helloretail.com/api/recoms/)* 
```js
fetch('https://core.helloretail.com/serve/recoms', {
    "method": 'POST',
    "mode": 'cors',
    "credentials": 'include',
    "headers": {
        'Content-Type': 'application/json'
    },
    "body": JSON.stringify({
        "websiteUuid":"6afc16a0-6657-4f10-859e-54ea03a6759b",
        "trackingUserId":"64538f21e1c2815a658631dd",
        "requests":[
            {
                "key": "k62a1aa2f5c5f7e7b1b50458f",
                "format":"json",
                "fields": ["title","url"], // fields can be removed in order to return ALL data.
                "context": {
                    "hierarchies":[["Clothing","Women"]],
                    "brand": "Nike",
                    "urls": ["https://product-urls-for-context"],
                    "price":20,
                    "extraData":{
                        "color":"black"
                    },
                    "extraDataList":{
                        "d9995dTargetGroups":["590"]
                    }
                }
            }
            
        ]
    })
}).then((res) => {
    return res.json();
}).then((data) => {
    console.log(data)
});
```

### *[Unmanaged Fetch Recommendation API request example](https://developer.helloretail.com/api/recoms/)* 
```js
fetch('https://core.helloretail.com/serve/recoms', {
    "method": 'POST',
    "mode": 'cors',
    "credentials": 'include',
    "headers": {
        'Content-Type': 'application/json'
    },
    "body": JSON.stringify({
        "websiteUuid":"3ced6ff1-d6a1-4263-9e0d-34cdfd9b3056",
        "trackingUserId":"644fa63d74a13834ab841abc",
        "requests":[
            {
                "trackingKey":"frontpage-12",
                "fields":["title","url"], // fields can be removed in order to return ALL data.
                "hideAdditionalVariants":true,
                "count":8,
                "sources":[
                    {
                        "type":"TOP",
                        "limit":4,
                        "filters":{
                            "hierarchies": {"$in": [["Legetøj","Klassisk Legetøj","Sjovt legetøj til lave priser"]]}
                        }
                    },
                    {
                        "type":"RETARGETED",
                    }
                ]
            }
        ]
    })
}).then((res) => {
    return res.json();
}).then((data) => {
    console.log(data)
});

```
