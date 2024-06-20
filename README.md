# About

Product categorization project using an embeddings API to assign categories to products based on embeddings comparisons.<br>
See notebook [here](https://github.com/FlorianLD/product_categorization_feature/blob/main/notebook.ipynb).

# Objective

The objective was to create a POC to see how this type of feature could be integrated to an app for a catalog team creating product pages.<br>
In the context of product page creation, product categorization is one of the steps that can benefit from automation in the following ways:
- Facilitating onboarding for new team members that are not familiar with the category list
- Narrowing down the category selection by returning the most suitable categories to choose from
- Reducing frequency of UI interactions from the end user (e.g. opening a category menu, scrolling, selecting a category) so they can focus on higher-value tasks

# Tools

- [Cohere](https://cohere.com/): embeddings API
- [Pinecone](https://www.pinecone.io/): vector database
- [Retool](https://retool.com/): product page creation app

# Embeddings

In this project, the embeddings are product names transformed into lists of float values, known as vectors.<br>
The closer two products' embeddings are, the closer their semantic meaning.<br>
For this reason, embeddings can be used to group data based on their semantic similarity.<br>
For more details, refer to [Cohere's embeddings guide](https://docs.cohere.com/docs/embeddings).

# Process

The feature works as follows:

1) Generating embeddings for the products of the dataset, using product names
2) Storing the product data and embeddings on Pinecone
3) Generating embeddings for a new product, using the product name
4) Comparing the new product's embeddings to the embeddings of the products from the dataset
5) Returning the product from the dataset whose embeddings are the most similar to the new product's embeddings
6) Retrieving the category from the returned product and assigning its category to the new product

### Steps 1 and 2

![Test](/diagram1.png)

### Steps 3, 4, 5 and 6

![Test](/diagram2.png)


# Prototype

Retool is the platform used to build the prototype for the product page creation app.<br>

The prototype app works as follows:
1) The user enters the new product's name in the "Product name" field
2) The user clicks the "Generate category" button to trigger the category retrieval
3) In the "Category" field, the retrieval returns up to 3 categories to choose from, sorted by similarity score, from highest to lowest

![Test](/demo.gif)

The two APIs used to assign a category to a new product were added and configured in Retool's Query Library:
- Cohere embeddings API
- Pinecone query API

The Query Library enables users to reference APIs so that they can be linked to events and automatically triggered when users interact with specific UI elements on a Retool app.

![Test](/query_library.png)

In this prototype, the Pinecone Query API ``topK`` parameter is set to 10 to return a list of 10 products, sorted by similarity score, from highest to lowest.<br>
The API response is then processed to return the top 3 categories and populate the "Category" field.<br>
The goal is to give the end user more agency when assigning a category to a new product: by returning a choice of 3 categories, we account for potential mistake or inaccuracy, while enabling the end user to leverage their own product expertise to choose the correct category among the ones returned.<br>

To retrieve the top 3 categories, the API response is processed as follows:
- First category: find the product with the highest similarity score, and return its category
- Second category: find the product with second highest similarity score, whose category is different from the first category returned, and return its category
- Third category: find the product with third highest similarity score, whose category is different from the first and second categories returned, and return its category

See the simplified table below for illustration:

![Test](/returned_categories.png)
