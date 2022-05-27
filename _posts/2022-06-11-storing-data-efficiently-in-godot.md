---
title: Storing Data Efficiently using Test-Driven-Development in Godot
date: 2022-06-11 00:00:00 +0900
categories: [Programming]
tags: [data structures]     # TAG names should always be lowercase
pin: true
image:
  path: /assets/img/posts/20220611/naming-the-resource.png
---

# Intro
Decoupling is a software design term for removing dependencies between modules. According to this concept, the fewer modules depend on other modules, the better the overall management of the codebase.<br />
I will try to show my approach to storing data efficiently in Godot using this concept.<br />
<u>My goal:</u> load data from a JSON file and store it for easy access in the game.

> Wikipedia about <a href="https://en.wikipedia.org/wiki/Coupling_%28computer_programming%29" target="_blank">Coupling</a>.
{: .prompt-tip } 


---
# The Process
<h2>Creating a new resource</h2>
First, I created a new resource and gave it the name <b>JSON_DB.tres</b>
In Godot, resources are great for storing data since the engine only loads them once. Any copy resource will point to the same original resource and will not create a new one in memory.

![Creating a new resource.](/assets/img/posts/20220611/creating-new-resource.png)
_Creating a new resource._


![Giving the resource a name.](/assets/img/posts/20220611/naming-the-resource.png)
_Giving the resource a name._


Now, adding a script to the resource:

![Adding a script to the resource.](/assets/img/posts/20220611/adding-a-script.png)
_Adding a script to the resource._

---
<h2>The Script</h2>

```gdscript
extends Resource
class_name JSON_DB

# ======== PRIVATE ========
var _database : Dictionary = {}
var _string_to_var : Array = []


# ======== Constructor ======== 
func _init(json_path="", json_strings_to_var=[]):
	# Load JSON from path
	var file = File.new()
	file.open(json_path, file.READ)
	_database = parse_json(file.get_as_text())
	file.close()
	
	# Which JSON's strings should be converted to variables
	_string_to_var = json_strings_to_var


# ======== PUBLIC ========
func get_dict():
	return _database


func get_values_first_key():
	var dict_all_keys = _database.keys()
	return _database.get(dict_all_keys.front())


func get_values(key):
	if _database.keys().has(key):
		return _database[key]
	return null


func get_nested_values(key, val):
	if _database.keys().has(key):  # Key (country) exists?
		if _database[key].has(val):  # Value exists?
			# If variable, convert from string
			for variable in _string_to_var:
				if String(_database[key][val]).find(variable) != -1:
					return str2var(_database[key][val])
			return _database[key][val]
	return null

```
Some points:

1) The database is a dictionary. It will contain unique values for each country.

2) The Constructor loads the JSON to this dictionary and converts variables specified in `_string_to_var`, such as `Color()`, to variables, as I will show below.

3) In the <b>Public</b> section there are several methods to access this dictionary. For now, I assume all the data I need is in this JSON file. Later I will add functions to set and change data.


Since <b>JSON_DB</b> will be used as a base for various data structures, I will create a dedicated database for the countries. Here's my <b>Countries_DB.gd</b>:
```gdscript
extends "res://Resources/Database/JSON_DB/JSON_DB.gd"
class_name Countries_DB

func _init(json_path="", json_strings_to_var=[]).(json_path, json_strings_to_var):
	pass
```
I will add dedicated functions that are not necessarily related to <b>JSON_DB</b> in the future.


---
<h2>The JSON File (countries.json)</h2>
```json
{ 
    "Brazil": {
      "color": "Color(0,1,0)",
      "stat": 1
    }, 
    "Chile": {
      "color": "Color(1,1,1)",
      "stat": 2
     }
}
```
In this JSON file, there's a `color` variable. In the script earlier, `get_nested_values` will check if we specified any variables in `_string_to_var` (actually, the constructor does it) and if so, it will convert them to variables using the built-in `str2var`.


---
<h2>Testing using test-driven-development.</h2>
<a href="https://github.com/bitwes/Gut/wiki/" target="_blank">GUT</a> is a test-driven development tool for Godot. It works well, and I will use it to test this script.

The test-driven script:
```gdscript
extends "res://addons/gut/test.gd"

var _file_path = "res://JSON_FILES/countries.json"
var _countries_DB = null

var _values_first_line = null
var _key_first_country = null
var _key_second_country = null
var _values_second_country = null
var _color_first_country = null
var _color_second_country = null

func before_each():	
	_values_first_line = "{color:Color(0,1,0), stat:1}"
	_key_first_country = "Brazil"
	_key_second_country = "Chile"
	_values_second_country = "{color:Color(1,1,1), stat:2}"
	_color_first_country = Color(0,1,0)
	_color_second_country = Color(1,1,1)
	
        # Here I specifies the variables which should be converted (Color)
	_countries_DB = Countries_DB.new(_file_path, ["Color",])
	
func after_each():
	pass



func test_creating_Countries_instance():
	assert_not_null(_countries_DB, "Should not be null")


func test_get_dict_not_null():
	assert_not_null(_countries_DB.get_dict())


func test_get_dict_returns_dict():
	assert_typeof(_countries_DB.get_dict(), TYPE_DICTIONARY)


func test_DB_first_line():
	var values_first_line = String(_countries_DB.get_values_first_key())
	assert_eq(values_first_line, _values_first_line)


func test_DB_second_line():
	var values_second_country = _countries_DB.get_values(_key_second_country)
	values_second_country = String(values_second_country)
	assert_eq(values_second_country, _values_second_country)


func test_DB_first_line_nested_value():
	var color_first_country = _countries_DB.get_nested_values(_key_first_country, "color")
	assert_eq(color_first_country, _color_first_country)


func test_DB_second_line_nested_value():
	var color_second_country = _countries_DB.get_nested_values(_key_second_country, "color")
	assert_eq(color_second_country, _color_second_country)

```

Brb.


