# Facebook Marketplace Recommendation Ranking System (AiCore)

Project that I'm working on as a part of my 'Data and AI' training at [AiCore](https://www.theaicore.com/).

![A screenshot of the AiCore portal](images/portal.png)

In the Facebook Marketplace Search Ranking project, we are asked to develop and train a multimodal model that accepts images and text. The output of the model will generate embedding vectors that are helpful to make recommendations to buyers using their demographic information. 
Once the model is trained, we will have to deploy it by creating an API using FastAPI, containerising it using Docker, and uploading it to an EC2 instance. The API will contain all the models that we create and train, so any new request can be processed to make predictions on the cloud. To ensure that the model can be easily modified without building a new image again, the files corresponding to each model will be bound to the Docker image, so it allows us to update the models with retrained models even after deploying them.

## 👀 Project overview 👀

- process and clean text and image datasets;
- design and train a multimodal model that combines images and text to generate embedding vectors to create a search index using Pytorch;
- create a pipeline to systematically clean new data and upsert it into a database;
- develop an API that encapsulates the models using FastAPI;
- deploy a container with the API using docker compose.

## Technologies

## Milestones 1-2

![An image of the technology structure](images/technology.png)

The first and second milestones of the project consist in setting up GitHub, creating a repository for the project, an AWS account, and going over the technology that we are asked to reproduce.

A nice introductory video that I encourage everyone to watch is available on the [AiCore YouTube Channel](https://youtu.be/1Z5V2VrHTTA).

## Milestone 3

In milestone 3, we're required to clean both the tabular and the image datasets, which are made available to us as EC2 instances on AWS.

### Tabular data cleaning

The tabular dataset contains information about the listing, including its price, location, and description. We are required to create a file named `clean_tabular_data.py` within our repository, which has to be populated with code to clean the tabular dataset. The dataset was in a EC2 instance on AWS, which is accessed by ssh from the local machine.

As per usual, I started exploring the data using a jupyter notebook first, `clean_tabular_data.ipynb`, which is located within the `tabular_data/` directory. The pandas frame showed that the main cleaning required in this task was to convert the prices into a numerical format, as all entries were preceded by the pound sign (£), as in colomn 3 here below, and some entries included a comma (,), as illustrated by column 84.

![An image from the pandas data frame in which the pound sign appears in the price column](images/pound.png)
![An image from the pandas data frame in which a comma appears in the price column](images/comma.png)

Additionally, some rows contained at least one NaN, as in the image below. These rows will have to be deleted from the file as they constitute incomplete data.

![An image from the pandas data frame in which at least one NaN per column appears](images/nan.png)

The python file was therefore designed to include two functions, one to open the tabular data using pandas (`obtain_tabular_data()`), and one to do the necessary cleaning (`clean_price_column()`). The former implements code to open the .cvs file containing the dataset, and utilises the `.dropna()` method to delete all rows including at least one case of missing information. Its arguments, in the number of two, are the filepath and the line separator, which is by default a comma in .csv files.

The `clean_price_column()` function assigns the `['price']` column of the original pandas data frame to the `price_column` variable, and then includes the steps that follow:

```python3
price_column = price_column.str.strip('£')
price_column = price_column.replace(',','', regex=True) # commas need to go to convert price string to float
price_column = price_column.astype('float64')
```

These include the necessary operations to convert a string column into a column of floats, i.e., deletion of the pound sign (£), deletion of commas if present, and type conversion from string to float64. That the conversion has been successful can be verified by calling the .info() method on the panda frame, which prints the following:

![An image containing info on the pandas data frame](images/info.png)

### Image data cleaning

![An image showing the start image and resulting image after the resizing process](images/resizing.png)

> Little curiosity: My 6-year-old daughter has developed an interest in computing lately, and `resizing.png` is the first image she's ever named.