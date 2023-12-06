# **Browser console JS snippets**

### *Test pages div*
```js
let html = `<div id="helloretail-category-page-624421fb2d64b76dbff35627" data-filters=" { &apos;hierarchies&apos;: [&apos;Overlocker&apos;, &apos;Bernina Overlocker&apos;] } "></div>`

document.querySelector("SELECTOR TO BE POSITIONED RELATIVE TO").insertAdjacentHTML("beforebegin",html);
```

### *Create button to copy array of checked inputs in the Search filter & sorting column, to clipboard.*
```js
let html = `<span class="hr-copy-checkbox-setup" style="padding:4px 8px;background-color:#000;color:white;cursor:pointer;">Copy checkbox setup</span>`;

document.querySelector(".table.table-condensed.table-striped").insertAdjacentHTML("afterend",html);

document.querySelector(".hr-copy-checkbox-setup").addEventListener("click",function(){
    let checkbox = [];
    document.querySelectorAll("tr td:last-of-type input[type='checkbox']").forEach(function(item,index){
        if(item.checked){
            checkbox.push(item.parentElement.parentElement.querySelector("td:first-of-type").textContent);
        }
    });
    navigator.clipboard.writeText("Copied checkbox setup: "+JSON.stringify(checkbox));
    navigator.clipboard.readText().then((clipText)=>{ console.log(`Checkbox setup was copied to clipboard: <${clipText}>`) });
});
```
### *Create button to use array of checked inputs, copied to clipboard holder, to paste to new domain.*
```js
let html = `<span class="hr-paste-checkbox-setup" style="padding:4px 8px;background-color:#000;color:white;cursor:pointer;">Paste checkbox setup</span>`;

document.querySelector(".table.table-condensed.table-striped").insertAdjacentHTML("afterend",html);

document.querySelector(".hr-paste-checkbox-setup").addEventListener("click",function(){
    navigator.clipboard.readText().then((clipText)=>{
        if(clipText.includes("Copied checkbox setup:") && !clipText.includes("let html")){
            
        JSON.parse(clipText.split("setup: ").pop()).forEach(function(item){
            if(document.querySelector(`.table.table-condensed.table-striped tr:has(input[value='${item}']) td:last-of-type input`)){
                if(!document.querySelector(`.table.table-condensed.table-striped tr:has(input[value='${item}']) td:last-of-type input`).checked){
                    document.querySelector(`.table.table-condensed.table-striped tr:has(input[value='${item}']) td:last-of-type input`).click();
                }
                else{
                    console.log(`input named ${item} was already checked, skipping to next`);
                }
            }
            else{
                console.log(`element named ${item} did not exist, skipping to next`);
            }
        })
        }
        else{
            console.log("your most recent clipboard text, is not the copied checkbox setup, required for pasting of checkbox setup");
        }
    })
})
```
