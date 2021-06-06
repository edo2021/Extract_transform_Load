# Extract_transform_Load

The purpose was to extract, transform & load datasets into a database in pgAdmin while providing step by step instructions for users to follow.decided to observe active COVID-19 cases across the world in relation to continued vaccination efforts running from January 1, 2021 to March 21, 2021. We have successfully extracted, transformed, &amp; loaded this data utilizing csv files, Python in Jupyter Notebook and a SQL database.
​
​   ![image](https://user-images.githubusercontent.com/76269709/120911209-df3b0a00-c64a-11eb-82bb-63496436316e.png)

# Step-by-Step Instructions
## By following these numbered steps, you will be able to recreate our repository and successfully upload this data into a SQL database for future analyses.
​
### Establishing the Python Environment
​
1. On GitHub, search for our project repository titled **Extract-Transform-Load** and click on the green button on the right-hand side that reads "Code".
​
1. Click on the green "Code" button and copy the Repo SSH link by clicking the clipboard button directly adjacent to the link.
​
1. Navigate to your Desktop, right click and select "Create New Folder". Give your repository a title and right click to select "New Terminal at Folder" (if on a Mac) or "Git Terminal" (if on a PC).
​
1. In the terminal window, type the commmand 'git clone' and then paste the SSH link you copied from GitHub by typing 'command + v'. Hit Enter to clone the repository.
​
    1. Type 'source activate PythonData' to activate the Python environment.
​
    1. Once the Python environment is activated, type the command 'jupyter notebook' in the terminal to lauch the Jupyter Notebook extension to begin extracting data.

### Create a file to store db Username and Password
1. On your jupyter notebook - Home page you will want to store your password in a file so that it does not git pushed to Github. On the home page go to the right side of the page where you will find a drop down arrow titled "New". CLick this and create a new Text File.

1. Once the file is created title the file "db_storage.py". Then click to open the file.

1. After the file is opened you will want to create two variables:
    
    1. db_username = 'postgres'

    1. db_pw = 'enter your pgadmin password here'

1. Finally, once that is complete you can save the changes and then close the file. Then you will want to go to the folder housing your repo. Click and drag it to Visual Studio Code. Once your repo is open in Visual Studio Code, go to the .gitignore file. At the top of the text file on a clean line type db_storage.py. Save the changes and close Visual Studio Code.
​
### Creating Jupyter Notebook
​
1. Once the Jupyter Notebook environment is launched, take a minute to overview the csv files that are present within the **Resources** folder. This folder contains the two csv files from Kaggle that you will extract data from:
    1. country_vaccine.csv
    1. daily-corona.csv
​
1. Create a new .ipynb file by navigating to the righthand side of the screen and selecting the button that reads "New". Select the option **Notebook Python 3**. Provide a new title for your .ipynb file by highlighting the "Untitled" heading in the upper left corner of the Jupyter Notebook.

### Extraction - country_vaccine_csv
​
1. Now that your jupyter notebook is prepared, you can now import your dependencies that will assist you in extracting and transforming data for both csv files. The dependency modules used in this project are Pandas and SQLAlchemy.
    1. In the first cell, type the command 'import pandas as pd', hit Enter, and then type 'from sqlalchemy import create_engine'

    1. In the next cell you will need to import your pgadmin login information. Type 'from db_storage import db_pw, db_username'.
​
    1. To filter warning labels that may pop up in this extraction, type the code 'import warnings' to import the dependency, hit Enter, and then type 'warnings.filterwarnings("ignore") and hit Enter to establish it.
​
1. You must assign each csv to a variable and utilize Pandas to read the files into their separate DataFrames. *Create variables that are pertinent to the information contained in the csv files to keep track of which DataFrame you are editing.*
​
    1. Example: 'vaccine_file = "Resources/country_vaccine.csv"'.
​
    1. With the csv routed to a variable, pass the variable into a DataFrame for Pandas to read it. Example: 'vaccine_df = pd.read_csv(vaccine_file)' and then type 'vaccine_df' to look at the first 10 rows of the csv.
​
​
### Transformation - country_vaccine_csv
​
1. The first transformation will be to reduce the recorded entries by date spanning from 01/01/2021 to 03/21/2021. The '.loc' function will be used to filter these rows by a selected range, which is then passed into a new "clean" variable.
    1. Example: 'clean vaccine = = vaccine_df.loc[(vaccine_df['date'] >= '2021-01-01') & (vaccine_df['date'] < '2021-03-21')]'.
​
    1. Use the '.fillna()' command to replace any NaN entries within the columns **total_vaccinations** and **people_vaccinated** with the integer 0. This will clean the data for future analyes.
        1. Example: 'clean_vaccine['total_vaccinations'] = clean_vaccine['total_vaccinations'].fillna(0)'
​
        1. Example: 'clean_vaccine['people_vaccinated'] = clean_vaccine['people_vaccinated'].fillna(0)'
​
    1. Use the '.sort_values()' command to sort the DataFrame layout based on the date. Example: 'clean_vaccine = clean_vaccine.sort_values('date')'
​
1. **Note:** If you were to run a .dtypes() on the clean_vaccine DataFrame, you will notice that the date column is actually in an object format. The next transformation is to convert this type into a datetime so that it can eventually be connected to our second csv in the SQL Database.
​
    1. Convert the "date" column in the vaccine DataFrame to datetime using the command 'pd.to_datetime()'. Example: 'clean_vaccine['date'] = pd.to_datetime(clean_vaccine['date'])'
​
1. **Note:** The primary key for a SQL Database needs to be a unique term/variable. Given that both of these CSVs report data for both country and the date several times as a result of continual data collection, you need to combine the date and the country name into a new column, so that each entry has a unique identifer in the DataFrame which can be used in the Loading portion of this project.
​
1. The "date" column of the corona DataFrame needs to be converted to a string and combined with the "country" column. Use the 'reset_' function to select both the country and date columns, and use the '.astype()' function on the "date" column to convert its data type from an object to a string. Pass these into a new reference variable.
​
    1. Example: 'clean_vaccine['date_country'] = clean_vaccine['country']+ (clean_vaccine['date'].astype(str))'
​
1.  Reset the index of the DataFrame using '.reset_index()' command and passing into a new reference variable. This CSV is now successfully extracted and transformed!
​
### Extraction -- daily_corona_csv
​
1. Use the same routing methods you just implemented for the country_vaccine_csv to connect the daily_corona_csv into a Pandas variable that will be passed into a DataFrame. 
    1. Example: 'corona_file = "Resources/daily_corona.csv"', hit Enter, and then 'corona_df = pd.read_csv(corona_file)'. Follow with a '.head()' command to view the first 5 rows of the csv.
​
### Transformation -- daily_corona_csv
​
1. Repeat the '.loc()' function on the variable containing the corona DataFrame to reduce the entries between our date range of interest (01/01/2021 to 03/21/2021).
​
1. Utilize the '.fillna()' function to fill replace the NaNs in columns **daily_new_cases** and **daily_new_deaths** with the integer 0. 
​
    1. Example: 'clean_corona['daily_new_cases'] = clean_corona['daily_new_cases'].fillna(0)'
​
1. Convert the "date" column in the vaccine DataFrame to datetime using the command 'pd.to_datetime()'. Example: 'clean_vaccine['date'] = pd.to_datetime(clean_vaccine['date'])'.
​
1. Reset the corona DataFrame index using the '.reset_index()' function, then drop the original index column using the '.drop()' function. 
​
1. **Note:** The primary key for a SQL Database needs to be a unique term/variable. Given that both of these CSVs report data for both country and the date several times as a result of continual data collection, you need to combine the date and the country name into a new column, so that each entry has a unique identifer in the DataFrame which can be used in the Loading portion of this project.
​
1. The "date" column of the corona DataFrame needs to be converted to a string and combined with the "country" column. Use the 'reset_' function to select both the country and date columns, and use the '.astype()' function on the "date" column to convert its data type from an object to a string. Pass these into a new reference variable.
​
    1. Example: 'reset_corona['date_country'] = reset_corona['country']+ (reset_corona['date'].astype(str))'
​
### Loading into SQL Database
​
1. Launch pgAdmin4 and input your password to access the database system. Once the environment is activated, create a new database (Example: corona_db) and open the Query Tool.
​
1. Create a table to connect to the data contained in the vaccine DataFrame in your Jupyter Notebook (Example: 'CREATE TABLE country_vaccine ()')
​
    1. Input the column titles and their data types into the SQL vaccine table (Example: 'country TEXT,'). These titles should be formatted exactly like the column titles in the Jupyter DataFrames, so reference the Jupyter Notebook as needed. Assign the "date_country" column as your primary key.**Please note that the original "date" column was an object, so in the SQL table the datatype needs to be TEXT**
​
1. Create a table to connect to the second DataFrame containing the daily corona case information (Example: 'CREATE TABLE daily_corona).
​
    1. Input column titles and their associated data types into the table, and assign the "date_country" column as the primary key. 
​
1. Use the 'SELECT * FROM' command on both tables to ensure that the column titles were created successfully. Now it is time to go back to the Jupyter Notebook to create the connection for the SQL database and load the data.
​
1. In the Jupyter Notebook, create a connection variable that references the link to your PgAdmin postgres database. (Example: 'rds_connection_string = f"{db_username}:{db_pw}@localhost:5432/corona_db')
​
1. Pass this connection variable into an engine using the 'create_engine' function. 
​
1. To ensure that your notebook and SQL database connected succesfully, pull the table names from the SQL database using the '.table_names()' function.
​
1. Now you must push the DataFrames in Jupyter Notebook to the SQL tables so that you can then join them together. Utilize the '.to_sql()' function to push the transformed versions of your DataFrames to their associated table in SQL by referencing the table name. The connection should be assigned to the engine. 
​
    1. Example: 'reset_vaccine.to_sql(name='country_vaccine', con=engine, if_exists='append', index=False)'
​
    1. Repeat steps to input the transformed daily corona DataFrame to it's SQL table daily_corona.
​
1. Read the table for the country_vaccine in Jupyter Notebook using the '.read_sql_query()' function and the SQL language used to read data within the table.
​
    1. Example: 'pd.read_sql_query('select * from country_vaccine', con=engine)'
​
1. Repeat the same '.read_sql_query()' command on the daily_corona table to ensure that it loaded correctly. Now it is time to go back to SQL postgres to join the tables!
​
1. Once you are back in the Query Tool in SQL, use the 'SELECT' and 'FROM' functions to choose all of the column titles within the daily_corona table. Use the 'INNER JOIN' command to connect those titles to the country_vaccine table, and use the 'ON' command to specify that this inner join should be on the designated primary key, "date.country".
​
    1. Example: 'SELECT daily_corona.date_country, daily_corona.daily_new_cases, daily_corona.daily_new_deaths, 
country_vaccine.daily_vaccinations, country_vaccine.total_vaccinations 
FROM daily_corona
INNER JOIN country_vaccine
ON daily_corona.date_country = country_vaccine.date_country;'
​
1. Run the 'SELECT * FROM country_vaccine' command again in the Query Tool to view both tables connected!

### Language(s)

* Pandas
* Python
* SQL

### Built With

* Jupyter Notebook
* pgAdmin
* Visual Studio Code

