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

## *Recognizing the data type of a property and understanding how to map it*

### *Recognizing the data type*
The values that the Hello Retail feed reader version 2 is able to





