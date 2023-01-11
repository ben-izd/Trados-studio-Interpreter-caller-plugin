# Trados Studio Interpreter Caller

This plugin is a batch task plugin for trados studio that help you to use other languages like `Python`, `Julia`, `R` and other interpreting languages to manipulate translation segments.

> All the examples shown below used Trados Studio 2021.
> You may recieve error for using previous versions of Trados Studio.

You can access the source code in [this repository](https://github.com/beny74483/Trados_studio_Interpreter_caller_source).

## Installation
### Trados Studio 2022
Download and either double click on <kbd>[Interpreter_Caller_2022.sdlplugin](https://github.com/ben-izd/Trados-studio-Interpreter-caller-plugin/blob/4605175124dd322bd403d834e7703e918c5d51cd/Interpreter_Caller_2022.sdlplugin)</kbd> to install it automatically or copy the file to

	C:\Users\YOUR_USER_NAME\AppData\Roaming\Trados\Trados Studio\17\Plugins\Packages

### Trados Studio 2021
Copy <kbd>[Interpreter_Caller_2021.sdlplugin](https://github.com/ben-izd/Trados-studio-Interpreter-caller-plugin/blob/4605175124dd322bd403d834e7703e918c5d51cd/Interpreter_Caller_2021.sdlplugin)</kbd> file to

	C:\Users\YOUR_USER_NAME\AppData\Roaming\SDL\SDL Trados Studio\16\Plugins\Packages
	
### Trados Studio 2019
Copy <kbd>[Interpreter_Caller_2019.sdlplugin](https://github.com/ben-izd/Trados-studio-Interpreter-caller-plugin/blob/49c486e5f9acf4003cdce0c74110e451b16099ef/Interpreter_Caller_2019.sdlplugin)</kbd> file to

	C:\Users\YOUR_USER_NAME\AppData\Roaming\SDL\SDL Trados Studio\15\Plugins\Packages

## How it works?

To show the plugin workflow I use a simple translation file like below:

<!-- 5.png -->
![image](https://user-images.githubusercontent.com/56647066/211738252-959a36cc-f1e5-4d25-bee5-136f3accd8bf.jpg)


Trados studio automatically split a file into diffrent parts called segments. Each segment has some property:

<!-- 2.jpg -->
![image](https://user-images.githubusercontent.com/56647066/211738455-71437b4d-ea61-4a1a-8070-1169681626ec.jpg)



Plugin will get a interpreter and file_script path and call each of these properties as argument like:

<!-- 3.jpg -->
![image](https://user-images.githubusercontent.com/56647066/211738491-6d2e9b4b-7c5c-49fb-90ea-f81800332d9b.jpg)


and base on given settings may use the returned text as <code>Segment Source **(2)**</code> or <code>Segment Translation **(3)**</code> or <code>Segment Status **(4)**</code>

----------
## Plugin Interface

<!-- 15.jpg -->
![image](https://user-images.githubusercontent.com/56647066/211738661-cfcf6e77-4281-467b-a7b7-e1ffe9cf1def.jpg)

**(1)** -> Interpreter path for languages if you install them on the defualt location:
> python:
<code>C:\Users\YOUR_USER_NAME\AppData\Local\Programs\Python\Python38\python.exe</code>
> Julia:
<code>C:\Users\YOUR_USER_NAME\AppData\Local\Programs\Julia\Julia-1.4.1\bin\julia.exe</code>
>R Language: 
<code>C:\Program Files\R\R-4.0.3\bin\Rscript.exe</code>

**(2)** -> Your script file path like <code>C:\my_file.py</code> or <code>C:\my_file.j</code> or <code>C:\my_file.r</code> base on your choosen interpreter

**(3-1)** -> Do you want to use the return text from running script
**(3-2)** -> If yes then use it for which part
there are 3 diffrent options:
1. Segment Translation
2. Segment Source
3. Segment Status: should be return status text (case-insensitive) or status index according to this (see example 2):

<!-- 1.jpg -->
![image](https://user-images.githubusercontent.com/56647066/211738818-e8f175f1-d5e6-4cc5-a63c-24f44f151ea8.jpg)


**(4-1)** -> Do you want to after running the script, it changes the effected segments status (also remember if you change **(3-2)** item to "Segment status" you can't use this part)
**(4-2)** -> If yes then change it to which status

**(5-1)** -> Do you want to skip a segment by returning a specified text
**(5-2)** -> This option determines a text that when you return that from your script, plugin will skip the segment

#### These options are for filtering segments

**(6)** -> You can apply it to diffrent selections of segments as shown
**(7)** -> You can apply it to a specified range of <code>Segment Number</code>
**(8)** -> You can apply it to a sepecific <code>Segment Status</code>

#### For Advance users:

**(9)** -> If you want to debug your script, check whether it works or how long it takes, put your script path on this input. (remember the specified interpreter will be used.)

## Template

This is a template to start developing your python script:

	import sys
	from typing import List
	
	# Number of Total Arguments: 4
	
	# script_file_path      :str       = sys.argv[0]  # your script path like 'c:\\my_file.py'
	segment_number          :str       = sys.argv[1]  # Segment number
	segment_source_text     :str       = sys.argv[2]  # Segment source
	segment_target_text     :str       = sys.argv[3]  # Segment target
	segment_status          :str       = sys.argv[4]  # segment status like "Translated", ... same as Trados Studio writing
	segment_meta_data_list  :List[str] = sys.argv[5:] # variable size depend on segment meta data include username and time of modification, ... (format: <key>,<value>,<key>,...)

and this is a template for your python debug script:

	import sys
	from typing import List
	
	# Number of Total Arguments: 13
	
	# script_file_path        :str       = sys.argv[0]   # your script path like 'c:\\my_file.py'
	segment_number            :str       = sys.argv[1]
	old_segment_status        :str       = sys.argv[2]   # segment status like "Translated", ... same as Trados Studio writing
	new_segment_status        :str       = sys.argv[3]   # segment status like "Translated", ... same as Trados Studio writing
	old_source_text           :str       = sys.argv[4]
	new_source_text           :str       = sys.argv[5]
	old_target_text           :str       = sys.argv[6]
	new_target_text           :str       = sys.argv[7]  
	old_target_count          :str       = sys.argv[8]   # number of sub-segments in the segment
	send_time                 :str       = sys.argv[9]   # time when script file runs with format: "%Y-%m-%d %H:%M:%S.%f" with 2 digits for microsecond
	recieved_time             :str       = sys.argv[10]  # time when script file ends with format: "%Y-%m-%d %H:%M:%S.%f" with 2 digits for microsecond
	skip_for_selection_mode   :str       = sys.argv[11]  # return 0 for False, 1 for True
	skip_for_range            :str       = sys.argv[12]  # return 0 for False, 1 for True
	skip_for_status           :str       = sys.argv[13]  # return 0 for False, 1 for True
	skip_for_skipping_keyword :str       = sys.argv[14]  # return 0 for False, 1 for True and -1 for uncalculated
	segment_meta_data_list    :List[str] = sys.argv[15:] # variable size depend on segment meta data include username and time of modification, ... (format: <key>,<value>,<key>,...)
	
## Examples

#### Example 1

In this example I write code below in <code>my_file.py</code>. This code return segment source:

<!-- 13.jpg -->
![image](https://user-images.githubusercontent.com/56647066/173574181-12f4b052-e94c-4070-8181-bd4b6ea23fa3.png)


and config for plugin is:

<!-- 10.jpg -->
![image](https://user-images.githubusercontent.com/56647066/173574205-f87f32ac-833b-48c0-8f3e-dd25c2c71ac5.png)


This config means insert the return text (which is segment source) from script to every segment that don't have translation (like copy source to translation but treat tag structures as text).

> **Note**
> Since we are dealing with japanese characters, we use 
	<code>sys.stdout.buffer.write(bytes(segment_source_text,'utf-8'))</code> which supports UTF-8 characters instead of simple <code>print(segment_source_text)</code>

and the result:

<!-- 14.jpg -->
![image](https://user-images.githubusercontent.com/56647066/173574231-0beca253-e1ba-484e-b7a9-ebbe78123bc5.png)


### Warning:

Just like segment 2 in this example, tags will not work as you passed them as return text, so for this reason there are two options in <code>Plugin Interface (6) - Apply only to</code> that has <code>Plain Text</code> in them which means have no tags.

#### Example 2

In this example we apply one line code to our first sample project.
config:

<!-- 20.jpg -->
![image](https://user-images.githubusercontent.com/56647066/173574247-37f5e1f6-7027-4d96-9cea-db6b3b43e9de.png)


<code>my_file.py</code> contains following code:

	print('Translated')
	
	# you could also use
	# print('2') OR print('translated')
	
result:

<!-- 21.jpg -->
![image](https://user-images.githubusercontent.com/56647066/173574290-61211110-8250-44a9-80f8-ff0e789a84a1.png)


changes all segments to "Translated" status.

#### Example 3

For this example we will use julia language script.
<code>my_file.j</code> contain following code:

	print(ARGS[2])

config:

<!-- 22.jpg -->
![image](https://user-images.githubusercontent.com/56647066/173574313-dda83028-bad1-474d-9909-5e4a4b78db3d.png)


result:

<!-- 24.jpg -->
![image](https://user-images.githubusercontent.com/56647066/173574335-2df73ad4-7edb-4883-94bd-e4e920e759f9.png)


Just like Example 1, it will copy source to target (treating tag structures as text).

#### Example 4

This is a diffrent usage, in this example I create a report in html containing segment number, source, target, status with help of third-party library called [BeautifulSoup4 (bs4)](https://pypi.org/project/beautifulsoup4/).

Plugin config:

<!-- 19.jpg -->
![image](https://user-images.githubusercontent.com/56647066/173574363-51e1b0d0-ec82-4c08-9f70-5902eade0ae8.png)


<code>D:\my_file.py</code> contains following code:

	import sys
	from typing import List
	
	segment_number          :str       = sys.argv[1]
	segment_source_text     :str       = sys.argv[2]
	segment_target_text     :str       = sys.argv[3]
	segment_status          :str       = sys.argv[4]
	segment_meta_data_list  :List[str] = sys.argv[5:]
	
	from os import path
	from bs4 import BeautifulSoup
	
	file_name = 'report.html'
	if path.exists(file_name):
	    with open(file_name,'r+',encoding='utf-8') as f1:
	        bs1 = BeautifulSoup(f1.read(),'html.parser')
	        bs1.find('table').append(BeautifulSoup(f'<tr><td>{segment_number}</td><td>{segment_source_text}</td><td>{segment_target_text}</td><td>{segment_status}</td></tr>','html.parser'))
	        f1.seek(0,0)
	        f1.write(str(bs1))
	else:        
	    with open(file_name,'w',encoding='utf-8') as f2:
	        bs2 = BeautifulSoup('<!DOCTYPE html><html><head><style>table, th, td {border: 1px solid black;border-collapse: collapse;}</style><title>Document</title></head><body><table><tr><td>Number</td><td>Source</td><td>Target</td><td>Status</td></tr></table></body></html>','html.parser')
	        bs2.find('table').append(BeautifulSoup(f'<tr><td>{segment_number}</td><td>{segment_source_text}</td><td>{segment_target_text}</td><td>{segment_status}</td></tr>','html.parser'))
	        f2.write(str(bs2))

result:

<!-- 17.jpg -->
![image](https://user-images.githubusercontent.com/56647066/173574387-cfd50edd-2aee-4e68-a211-3214c77cbd11.png)


### Under the hood

Running with <code>C:\python38\python.exe</code> as python interprter and <code>C:\my_file.py</code> as script on our sample project is like running:

	>>"C:\\python38\\python.exe" "C:\\my_file.py" "1" "This is a sample text" "Translation" "Draft"

	>>"C:\\python38\\python.exe" "C:\\my_file.py" "2" "Sample header \<a href=#>link</a>" "" "Not Translated"

	>>"C:\\python38\\python.exe" "C:\\my_file.py" "3" "これはサンプルテキストです" "" "Not Translated" "created_by" "YOUR_WINDOWS_USER_NAME" "created_on" "11/07/2020 13:31:48" "last_modified_by" "YOUR_WINDOWS_USER_NAME" "modified_on" "11/07/2020 13:52:40"
	
> **Note:**
> This plugin cannot create tag (whether formating or refrence one) when returning, just like second run it will pass text like html formatting but even returning the same text will be treated as text as shown in example 2. 
> Last run was manipulated on purpose to show you how meta data will be send. First key name then it's value.

## Snippets:

### Snippet 1

Before running your code, monkey patch sys.argv and run your script multiple times to simulate the batch task enviroment and make sure it runs correctly:
	
	import sys
	
	sys.argv = ["1","sample","translation","Draft"]
	
	... YOUR_CODE
	
> **Remember**
> Raising any error while runnig script will not be shown in trados dialogue and it will show you script ran successfully. Be careful 😮.

----------
 
### Snippet 2

Use the following code to show your message in the middle of batch task while not interupting the script:
First install new windows terminal developed by microsoft because it supports UTF-8 encoding from [windows store](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701).
then use this code in your script:

	import subprocess
	
	subprocess.Popen(['start', 'wt',"YOUR MESSAGE"], shell=True, creationflags=0x00000008)
	
	// show args:
	// subprocess.Popen(['start', 'wt','::'.join(sys.argv)], shell=True, creationflags=0x00000008)
	
result:

<!-- 18.jpg -->
![image](https://user-images.githubusercontent.com/56647066/173574428-81e1dca8-2efc-41f0-a352-cbd8d27d4f6e.png)


Because new windows terminal has no start command option, I intentionally use this code to cause an error while showing our message 😬.
