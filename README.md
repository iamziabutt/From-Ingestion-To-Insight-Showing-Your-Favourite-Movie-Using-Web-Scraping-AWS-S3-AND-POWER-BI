## From Ingestion to Insight: Data pipeline to show online streaming options of your favourite movie

Ths project combines `web scraping` and `api call` approaches to ingest data from movie databases `imdb` and `watchmode` to show in `Microsoft Power BI` what online streaming platforms are showing `Top imdb movies` in the UK.

### 🌟Goals:

 1. What are the Top 250 imdb movies of all time based on users vote count
 2. What streaming platforms are showing a movie from the list in the UK and what price are these platform charging


### 🛠️Plan:

 1. We will extract list of top 250 movies from imdb webpage through `web scraping`. This will be extracted through Python `Selenium` package. We will inspect the HTML inpsect option and pick up information from `parent div`, `classes` and `spans`.
 2. We will make an api call to `watchmode movie database` to show us streaming options for each of the movie that we extracted from the `imdb database`.
 3. We will `left join` both datasets on `transformed_title` column and loading the data to `AWS S3` bucket. We will use `boto3` package to perform loading
 4. In the end, we will make a connection between `AWS S3` and `Power Bi` through creating a `presigned URL` in 3 and using it in Power BI `get data` option.
 5. After creating the dashboard, we will publish it to PowerBi service for sharing.


 ### 🎯Resources required:

 ```
  - Boto3
  - Requests
  - Selenium
  - AWS S3
  - Power BI downloaded natively
  - Workspace setup in Power BI service for publishing and sharing
 ```

 ### 🏗️ Project architecture:

 ![Reference Image](/resources/Diagram.png)

 ### 📂Project structure:

 ```
text

Imdb-movies-project /
├── config/        
|   ├── credentials.ini             # stores AWS secret data
|   ├── watchmode.py                # stores watchmode api key
├── Extraction/              
|   ├── imdb_webscraping.ipynb      # Extracting list of top movies
|   ├── watchmode_streaming.ipynb   # extracting streaming options
├── Transformation/
|   ├── data_merger.ipynb # joining both datasets
├── Loading/
|   ├── s3_loading.ipynb            # writing to aws s3 bucket 
├── resources                       # storing Markdown file resources
|
├── Outputs/                        # extraction results saved in .csv files
|   ├── final_dataset.csv   
|   ├── imdb-moives.csv  
|   ├── output_streaming_json  
|   ├── streaming_options.csv  
|___

```
###  🚀 Step by step implementation:

**Step 1: ✅Extracting data from imdb database**
 - Run `selenium webdriver` and use `.get()` command to connect to the imdb page.
 - This will open a browser window on your system, keep this page opened. We will use `Inspect` tab to extract required elements
 - We could find elements either by html `class` or `xpath`. Our preferred approach will be `class`.
 -  We have extracted below elements:
             - movie title
             - movie rank
              - release year
              - star rating
             - vote count
             - duration, and
             - pg_rating
- We have then looped through all 250 movies and the elements and saved the results into a dataframe
- We have then converted this dataframe into a .csv file and saved the file as `imdb-movies.csv` in our `/Outputs` directory

**Step 2: ✅Making an API call to watchmode movie database**
 - Using `requests` library in python to making an api call.
 - Extracted below elements from the results
            - title
            - price
            - platform
            - type, and
            - url
- We have then converted the results from the api call to a .json document and wrote it into a .csv file and saved the file as `streaming_options.csv` in our `/Outputs` directory

**Step 3: ✅Transforming and creating a combined dataset**
 - `title` field in both dataset was normalized through `strip.()` and `lower.()` commands.
 - Joined both datasets by `transformed_title` on `left` join and created a dataframe out of it
- We have then converted this dataframe to a .csv file and saved the file as `final_dataset.csv` in our `/Outputs` directory

**Step 4: ✅Loading to AWS S3**
 - Created a bucket called ` imdb-250-movies-bucket` in AWS S3

![Reference Image](/resources/s3-bucket.png)

 - Read the data from `/Outputs` directory and written to the above bucket
- Obtained `presigned url` from this object in S3 to make a connection with Power BI

 ![Reference Image](/resources/creating-paginated-url.png)

**Step 5: ✅Making connection with Power BI**
 - In Power BI desktop, go to Home > Get data > Web > URL (paste your presigned url here)
 - This would open to `query editor` window where I converted this S3 object to .csv file

![Reference Image](/resources/powerbi-object-to-csv.png)

- Desgined a basic dashboard with this data and published to the Power Bi service
- Obtained a dashboard link from the service for sharing

 ![Reference Image](/resources/powerbi-dashboard.png)