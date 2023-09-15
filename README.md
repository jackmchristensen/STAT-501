# Getting Started with SAS
For now there are two main parts to SAS that we'll call 'blocks'. The Data Block and the Proc Block. The Data Block is for creating and editing data tables. The Proc Block is for processing data and outputting the results.
- ### Data Block Syntax
	- Every data block starts with the 'data' keyword
	- Then you must tell SAS which data table you want to create or edit
	- Then the next few lines will be either the data you are creating or the data you are editing
	- End the block with 'run'
	- This will look like:
```SAS
data someDataTable;
	/*** CODE ***/
run;
```
- ### Proc Bloc Syntax
	- Starts with the 'proc' keyword
	- Then which function you would like to use
	- Which data you would like to process
	- Then the body of the block contain the code for processing the data
	- End with 'run'
	- For the univariate proc this would look like:
```SAS
proc univariate data = someDataTable;
	/*** CODE ***/
run;
```
- To run a block you have to select it by highlighting the code and then click the little running man icon in the tool bar
	- You can run multiple blocks at once, but they will be ran from top to bottom so if you try to make a graph before making a data table you will get an error
- You only need to import data once. Once it's in SAS you don't need to run that block again, unless you've changed the data
- ### Doing stuff with SAS can be broken down into three main steps
	- Download the data
	- Get the data into SAS
	- Do shit with the data
## Downloading the Data
- Save the file from Brightspace somewhere on your computer
	- If you're using the remote desktop simply drag and drop the file onto the browser and choose where you would like to save the file
		- I think this uses your Purdue network drives so you might want to look into best practices for using those
	- Copy the path to your data file
## Some Quick Definitions
- ### Proc Block
	- #### dbms
		- Similar to dlm this denotes the type of data being imported
		- Instead telling dbms to look for a special key, you give it a key word to look for
			- In this class, since we are only using tab delimited text files we will just use the 'tab' keyword
		- You must also tell dbms to 'replace' the data, otherwise the data will not get overwritten 
	- #### datafile
		- Gets the file at the given path
- ### Data Block
	- #### dlm
		- Short for delimiter. This is how the data is organized. A delimiter is the special key that separates columns. This is mainly used for importing .txt files because if you import an excel or .csv there are standard ways of separating columns.
		- dlm can technically be anything, but for this class we will only be using tabs. In SAS you must give the ASCII code for tab which is '09'x
			- This just means it's the ninth ASCII character
			- The x tells SAS to use hexadecimal
	- #### firstobs
		- Where the data starts
		- If the data starts on the first row of the .txt file then you don't need to have this however, the data given in class usually has the variable names in the first row so this tells SAS to ignore that row and only grab data starting on the row specified (for this class probably row 2)
	- #### infile
		- Looks at the data inside a file
	- #### input
		- This is where you tell SAS what variables you want to use for your data table
## Getting Data into SAS
- Proc Block
	- Use the 'import' function
		- This reads data from some file and outputs a new data table
	- In order for SAS to be able to do anything with the. txt file we need to give it some information
		- Where are we outputting the data
		- Where is the .txt file located
		- How is the data organized
	- This is how you would import ex01-044beer.txt and store the data in a data table called Beer:
```SAS
proc import out data = Beer datafile = 'D:\STATS\HW 03\DATA\ex01-044beer.txt' dbms = tab replace;
run;
						
	/*** or if you don't want to put everything on one line you can split it up into separate lines without a semicolon at the end ***/
						
proc import out data = Beer
	datafile = 'D\STATS\HW 03\DATA\ex01-044beer.txt'
	dbms = tab replace;
run;
```
- Data Block
	- I like to think that this way doesn't actually import the data table, rather it creates a new data table and copies the data from the .txt file into it
	- First look in the text file
		- Again you need to tell SAS some information about the text file
			- Where the file is
			- How the data is organized
			- And possibly when the data actually begins
				- This is only necessary if the variable names are in the .txt file
		- Next you need to create the variables the the data table will use
			- Note 'proc import' creates the variables automatically
			- To find the variables you should use you can just open the .txt file and copy the variables in there
				- The variables can be called whatever you want, but it's probably a good idea to keep things organized
				- For simplicity, I will just be using the variable names already given in the .txt file
			- SAS will assume all data are numbers
				- In order to read a string you must denote the variable as a string using '$'
					- Unfortunately SAS defaults to eight character strings so you need to tell SAS if a string will be longer using the 'length' keyword
		- This is how you would copy the data from ex01-044beer.txt and store it in a data table called Beer
```SAS
data Beer;
	infile 'D:\STATS\HW 03\DATA\ex01-044beer.txt' dlm = '09'x firstobs = 2;
	length Type $ 50;	/* Initializing the variable Type as a string with 50 characters */
	length Beer $ 50;	/* Note you can have a variable with the same name as the data table but this might get confusing */
	length Brewery $ 50;
	input Type Beer Brewery Calories Carbo_g_ Alcohol_pct_;	/* Note I didn't need to initialize the number variables because SAS defaults to using numbers */
run;
```
- Which one should you use?
	- It really comes down to personal preference
 	- The Proc Block method is a lot simpler, but you don't have as much control over things
		- You can rename variables after the fact (and this works for renaming variables whenever you want) by using the 'rename' function in a Data Block 
	- The Data Block method gives you more control, but it's more prone to errors and if a string is longer than what you've specified you need to go back and change the code
		- Also, since we are already being given the variable names I don't think the ability to name your own variables will be of too much use in this class   
- So we got the data into SAS, where does it live?
	- In the explorer window navigate to the highest directory
		- There is an 'up one level' button in the tool bar that lets you go back a directory
	- Go into the Libraries directory, then in the Work
		- Here you can open the data tables and look at the contents to double check everything got imported correctly or to look at variable names
		- I think SAS will default to the Work directory when calling data, but if you would like to be sure you're getting the correct data table you can specify it by writing 'Work.someDataTable'
## Doing Shit with the Data
- Now that we have data inside SAS we can finally do stuff with it
- For this step we will only be using the Proc Blocks
- Most of what we will be doing will be using the univariate function
- What's univariate?
	- Univariate just means single variable
	- When making graphs and tables we will normally only want to use one variable at a time, this is why we use univariate
	- To use the univariate function you must first tell SAS which variable you want to use, then what you want to do with that variable.
	- Another nice thing about the univariate function is that it will automatically give you a bunch of data about the given variable like mean, standard deviation, variance, range, etc.
	- For example if we wanted to make a histogram of the alcohol content in the beer data this is what we would do:
```SAS
proc univariate data = Beer;	/* Tells SAS which data table to look at (You can also use Work.Beer to be more exact) */
	var Alcohol_pct_; 			/* Which variable in the data table to use */
	histogram Alcohol_pct_; 	/* Creates a histogram graph of the Alcohol data */
run;
```
![Here is the output of the code above](https://github.com/jackmchristensen/STAT-501/blob/main/Screenshots/Histogram.png)
Also, if you want to overlay the normal and kernel lines you just need to specify that when calling the histogram function:
```SAS
proc univariate data = Beer;
	var Alcohol_pct_;
	histogram Alcohol_pct_ / normal kernel;	/* Draws the normal and kernel curves on top of histogram*/
run;
```
## Some Final Notes
- I've gotten into the habit of always checking the logs after running a block. This will tell you if there was an error in your code and if there is, what the error might be
- I think the convention used to be to write SAS in all uppercase, but the more recent SAS examples I've seen have been in lowercase. I think it's a preference thing, you can mix and match uppercase and lowercase and SAS doesn't care, just do whatever you think looks nice
