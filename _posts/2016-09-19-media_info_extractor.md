---
layout: post
title: media_info_collector.py
subtitle: An easy way to overview video attributes
tags:
- video
- mediainfo
- mediainfo_collection
- mediainfo_collector
- python
- python to excel
- programming
---
### What is media_info_collector.py?
I wrote a python script that could be useful for people, who are doing video clip analysis, or manual video testing, as it makes it really easy to compare video file attributes.  
It will collect all information about the video files located in the same folder as the script and then parse the data into an xml file.  
**Columns** of the excel file **will be in arbitrary order**.  
Attributes missing from file(s) (if present in at least one other file) will be substituted with "N/A"

### Here's how to use it:  
1. Just put a bunch of video files in a folder  
2. Make sure that you have **python**, **medianinfo** and **tablib** installed
   * To install mediainfo run ```sudo apt-get install mediainfo```
   * To install tablib run ```sudo pip install tablib```
3. If needed change ```filters```, if a filename contains one of these strings that file will be ignored
4. ```python media_info_collector.py```
5. Check out the beautiful result.xml file

### media_info_collector.py

```Python
import subprocess, os, copy, tablib

filters = ['.evs', '.py', '.xml']

if __name__ == '__main__':
    dir = os.getcwd()
    list_of_files = os.listdir(dir)
    new_list_of_files = []
    for file in list_of_files:
            if os.path.isfile(file) and all([filter not in file for filter in filters]):
                new_list_of_files.append(file)
    list_of_files = new_list_of_files

    media_info = {}
    for file in list_of_files:
        abs_path = dir + '/' + file
        media_info[file] = subprocess.check_output(['mediainfo %s'%file], shell=True, executable='/bin/bash').split('\n')

    result_dict = {}
    for file in media_info.keys():
        subsection = ''
        append_dict = {}
        subsection_dict = {}
        for line in media_info[file]:
            if line == '': continue
            if ':' not in line.strip():
                if subsection != '':
                    append_dict[subsection] = subsection_dict
                subsection = line.strip()
                subsection_dict = {}
            else:
                line = line.replace('  ', '')
                key, value = [x.strip() for x in line.split(': ', 1)]
                subsection_dict[key] =  value
        result_dict[file] = (append_dict)

    print "\n\n\nRESULT_DICT:" + str(result_dict) + "\n\n\n"

    keys = {}
    got_base = False
    for file in result_dict:
        if len(file) > len(keys):
            keys = copy.deepcopy(result_dict[file])
    for subsection in keys.keys():
                new_subsection = {}
                new_subsection[subsection] = keys[subsection].keys()
                keys.update(new_subsection)

    for file in result_dict.keys():
        for subsection in result_dict[file].keys():
            if subsection not in keys:
                keys[subsection] = result_dict[file][subsection].keys()


    for file in result_dict.keys():
        for subsection in keys.keys():
            for option in keys[subsection]:
                if subsection not in result_dict[file]:
                    result_dict[file][subsection] = {}
                if option not in result_dict[file][subsection].keys(): result_dict[file][subsection][option] = 'N/A'
    print "\n\n\nKEYS:" + str(keys) + "\n\n\n"

    data = tablib.Dataset()
    headers = []
    for subsection in keys.keys():
        for option in keys[subsection]:
            headers.append(subsection + ' - ' + option)
    print '\n\n\nHEADERS:' + str(headers) + '\n\n\n'
    data.headers = headers

    for file in result_dict.keys():
        file_list = []
        for subsection in keys.keys():
            for option in keys[subsection]:
                file_list.append(result_dict[file][subsection][option])
        data.append(file_list)
        print '\nFILE_LIST:' + str(file_list) + '\n'

    with open('result.xls', 'wb') as f:
        f.write(data.xlsx)
```
