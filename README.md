# Trados Studio Interpreter Caller

This plugin is a batch task plugin for trados studio that help you to use other languages like python, julia, R and other interpreting languages to manipulate translation segments.

> The Batch task was compiled with Trandos Studio 2021 SDK and all the examples shown below used Trados Studio 2021.
> You may recieve error for using previous versions of Trados Studio.

## Installation
Just download above file and copy <kbd>.sdlplugin</kbd> file to

	C:\Users\YOUR_USER_NAME\AppData\Roaming\SDL\SDL Trados Studio\16\Plugins\Packages

## How it works?

To show the plugin workflow I use a simple translation file like below:

![](https://img.imageupload.net/2020/11/11/5.png)

Trados studio automatically split a file into diffrent parts called segments. Each segment has some property:

![](https://img.imageupload.net/2020/11/11/2.jpg)

Plugin will get a interpreter and file_script path and call each of these properties as argument like:

![](https://img.imageupload.net/2020/11/11/3.jpg)

and base on given settings may use the returned text as <code>Segment Source **(2)**</code> or <code>Segment Translation **(3)**</code> or <code>Segment Status **(4)**</code>

----------
## Plugin Interface

![](https://img.imageupload.net/2020/11/11/15.jpg)

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

![](https://i.postimg.cc/CMQGjHyY/1.jpg)

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

![](https://img.imageupload.net/2020/11/11/13.jpg)

and config for plugin is:

![](https://img.imageupload.net/2020/11/11/10.jpg)

This config means insert the return text (which is segment source) from script to every segment that don't have translation (like copy source to translation but treat tag structures as text).

> **Note**
> Since we are dealing with japanese characters, we use 
	<code>sys.stdout.buffer.write(bytes(segment_source_text,'utf-8'))</code> which supports UTF-8 characters instead of simple <code>print(segment_source_text)</code>

and the result:

![](https://img.imageupload.net/2020/11/11/14.jpg)

### Warning:

Just like segment 2 in this example, tags will not work as you passed them as return text, so for this reason there are two options in <code>Plugin Interface (6) - Apply only to</code> that has <code>Plain Text</code> in them which means have no tags.

#### Example 2

In this example we apply one line code to our first sample project.
config:

![](https://i.postimg.cc/6pdCJk85/20.jpg)

<code>my_file.py</code> contains following code:

	print('Translated')
	
	# you could also use
	# print('2') OR print('translated')
	
result:

![](https://i.postimg.cc/hPQ9Mzgm/21.jpg)

changes all segments to "Translated" status.

#### Example 3

For this example we will use julia language script.
<code>my_file.j</code> contain following code:

	print(ARGS[2])

config:

![](https://i.postimg.cc/3wYmKtGj/22.jpg)

result:

![](https://i.postimg.cc/nh8qWFcP/24.jpg)

Just like Example 1, it will copy source to target (treating tag structures as text).

#### Example 4

This is a diffrent usage, in this example I create a report in html containing segment number, source, target, status with help of third-party library called [BeautifulSoup4 (bs4)](https://pypi.org/project/beautifulsoup4/).

Plugin config:

![](https://img.imageupload.net/2020/11/11/19.jpg)

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

![](https://img.imageupload.net/2020/11/11/17.jpg)

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

Use following code to show your message in the middle of batch task while not interupting the script:
First install new windows terminal developed by microsoft because it supports UTF-8 encoding from [windows store](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701).
then use this code in your script:

	import subprocess
	
	subprocess.Popen(['start', 'wt',"YOUR MESSAGE"], shell=True, creationflags=0x00000008)
	
	// show args:
	// subprocess.Popen(['start', 'wt','::'.join(sys.argv)], shell=True, creationflags=0x00000008)
	
result:

![](https://img.imageupload.net/2020/11/11/18.jpg)

Because new windows terminal has no start command option, I intentionally use this code to cause an error while showing our message 😬.
