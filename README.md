# Hands-On Introduction: Python 
This is the repository for the LinkedIn Learning course Hands-On Introduction: Python. The full course is available from [LinkedIn Learning][lil-course-url].

![1666990089517](https://user-images.githubusercontent.com/25848438/200745834-dc996102-6417-4bbe-b1e6-a9df15ebf3fa.jpeg)

If you’re an early-stage Python user looking to boost your professional game, you need to set aside the time—and bandwidth—to study up and advance your skills. Practice makes perfect, they say, so why not start right now? In this course, instructor Ronnie Sheer shows you the tools, techniques, and practical know-how of expert Python users, with twenty hands-on, interactive coding challenges to test out your skills as you go. Take your existing Python proficiency to the next level with tips on scope, strings, loops, CSV data, calculations, JSON data sets, web servers, and more. By the end of this course, you’ll be equipped with newly honed expert moves to keep learning on your upcoming projects.<br><br>The best way to learn a language is to use it in practice. That’s why this course is integrated with GitHub Codespaces, an instant cloud developer environment that offers all the functionality of your favorite IDE without the need for any local machine setup. With GitHub Codespaces, you can get hands-on practice from any machine, at any time—all while using a tool that you’ll likely encounter in the workplace. Check out the [Using GitHub Codespaces with this course][gcs-video-url] video to learn how to get started.  

### Instructor

Ronnie Sheer

Check out my other courses on [LinkedIn Learning](https://www.linkedin.com/learning/instructors/ronnie-sheer?u=104).

[lil-course-url]: https://www.linkedin.com/learning/hands-on-introduction-python
[gcs-video-url]: https://www.linkedin.com/learning/hands-on-introduction-python/using-github-codespaces-with-this-course

### Reading CSV
```
import csv
# from pprint import pprint

EINSTEIN_CSV = 'Albert,Einstein,1879-03-14,1955-04-18,Germany,"for his services to Theoretical Physics, and especially for his discovery of the law of the photoelectric effect",physics,1921'

EINSTEIN = {
    "birthplace": "Germany",
    "name": "Albert",
    "surname": "Einstein",
    "born": "1879-03-14",
    "category": "physics",
    "motivation": "for his services to Theoretical Physics...",
}

with open("laureates.csv", "r") as f:
    reader = csv.DictReader(f)
    laureates = list(reader)

for laureate in laureates:
    if laureate["surname"] == "Einstein":
        # pprint(laureate)
        print(laureate)
        break

```

### Extract Year / Month / Day from Date
```
from datetime import datetime
year_date = datetime.strptime(laureate["year"], "%Y")
born_date = datetime.strptime(laureate["born"], "%Y-%m-%d")
print("age", year_date.year - born_date.year)
```

###  JSON

Read 
```
import csv
import json
from pprint import pprint

EINSTEIN = {
    "birthplace": "Germany",
    "name": "Albert",
    "surname": "Einstein",
    "born": "1879-03-14",
    "category": "physics",
    "motivation": "for his services to Theoretical Physics...",
}

einstein_json = json.dumps(EINSTEIN) // read dictionary or json
back_to_dict = json.loads(einstein_json) // loads dictionary to json
print(einstein_json) // print
pprint(back_to_dict) // beautify print
```

### Convert CSV to JSON
```
with open("laureates.csv", "r") as f:
    reader = csv.DictReader(f) // convert each line to dictionary
    laureates = list(reader)   // append dictionary in a list


with open("laureates.json", "w") as f:     
    json.dump(laureates, f, indent=2) // write json from laureates data
```

### Requests
```
import requests

response = requests.get(
    "http://api.worldbank.org/v2/countries/USA/indicators/SP.POP.TOTL?per_page=5000&format=json")

last_twenty_years = response.json()[1][:20]

for year in last_twenty_years:
    display_width = year["value"] // 10_000_000
    print(f'{year["date"]}: {year["value"]}',"="*display_width)
    
```

### flask

```
from distutils.log import debug
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():
    return "Hello, World"


app.run(debug=True)

```

```
import csv
from flask import Flask, render_template, jsonify


app = Flask(__name__)

with open("laureates.csv", "r", encoding="utf-8") as f:
    reader = csv.DictReader(f)
    laureates = list(reader)


@app.route("/")
def index():
    # template found in templates/index.html
    return render_template("index.html")


@app.route("/laureates/")
def laureate():
    return jsonify(laureates)


app.run(debug=True)

```

```
import csv
from flask import Flask, render_template, request, jsonify


app = Flask(__name__)

with open("laureates.csv", "r") as f:
    reader = csv.DictReader(f)
    laureates = list(reader)


@app.route("/")
def index():
    # template found in templates/index.html
    return render_template("index.html")


@app.route("/laureates/")
def laureate_list():
    # template found in templates/laureate.html
    results = []
    if not request.args.get("surname"):
        return jsonify(results)

    search_string = request.args.get("surname").lower().strip()

    for laureate in laureates:
        if search_string in laureate["surname"].lower():
            results.append(laureate)
    # Your code here!
    return jsonify(results)


app.run(debug=True)

```