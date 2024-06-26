## Free Exercise DB 💪  &nbsp; [![Test, Lint & Deploy Site to Github Pages](https://github.com/yuhonas/free-exercise-db/actions/workflows/ci.yaml/badge.svg)](https://github.com/yuhonas/free-exercise-db/actions/workflows/ci.yaml) [![License: Unlicense](https://img.shields.io/badge/license-Unlicense-blue.svg)](http://unlicense.org/)

Open Public Domain Exercise Dataset in `JSON` format, 800+ exercises with a browsable public searchable frontend

### Why?

I started building another fitness related app and was looking for free/open source exercise lists and imagery I stumbled upon
[exercises.json](https://github.com/wrkout/exercises.json) which was amazing though the data wasn't structured the way I wanted it and I also wanted a browsable/searchable frontend to the data inspired by [this issue](https://github.com/wrkout/exercises.json/issues/5) so I restructured the data and built a simple frontend to it :)

### What do they look like?

All exercises are stored as seperate `JSON` documents and conform to the following [JSON Schema](./schema.json) eg.

```json
{
  "id": "Alternate_Incline_Dumbbell_Curl",
  "name": "Alternate Incline Dumbbell Curl",
  "force": "pull",
  "level": "beginner",
  "mechanic": "isolation",
  "equipment": "dumbbell",
  "primaryMuscles": [
    "biceps"
  ],
  "secondaryMuscles": [
    "forearms"
  ],
  "instructions": [
    "Sit down on an incline bench with a dumbbell in each hand being held at arms length. Tip: Keep the elbows close to the torso.This will be your starting position.",
  ],
  "category": "strength",
  "images": [
    "Alternate_Incline_Dumbbell_Curl/0.jpg",
    "Alternate_Incline_Dumbbell_Curl/1.jpg"
  ]
}
```
See [Alternate_Incline_Dumbbell_Curl.json](./exercises/Alternate_Incline_Dumbbell_Curl.json)

To further explore the data, you can use [lite.datasette.io](https://lite.datasette.io/?json=https://github.com/yuhonas/free-exercise-db/blob/main/dist/exercises.json#/data/exercises?_facet_array=primaryMuscles&_facet=force&_facet=level&_facet=equipment)

### How do I use them?

You can check the repo out and use the `JSON` files and images locally

#### Alternatively

You can leverage github's hosting and access the single or combined [exercises.json](https://raw.githubusercontent.com/yuhonas/free-exercise-db/main/dist/exercises.json) and prefix any of image path's contained in the `JSON` with `https://raw.githubusercontent.com/yuhonas/free-exercise-db/main/dist/exercises/` to get a hosted version of the image eg. [Air_Bike/0.jpg](https://raw.githubusercontent.com/yuhonas/free-exercise-db/main/exercises/Air_Bike/0.jpg) or leverage something like [imagekit.io](https://imagekit.io/) for dynamic image resizing which is utlized on the frontend [ example site ](https://github.com/yuhonas/free-exercise-db/blob/main/site/src/components/PhotoGallery.vue#L44-L54)

### Build tasks
There are a number of helpful [Makefile](./Makefile) tasks that you can utilize

#### Linting
To lint all the `JSON` files against the [schema.json](./schema.json) use

```
make lint
```

#### Combining into a single JSON file
If you make changes to any of the exercises or add new ones, to recombine all single `JSON` files into a single `JSON` containing an array of objects using the following make task

```sh
make dist/exercises.json
```
_Note: requires [jq](https://stedolan.github.io/jq/)_

#### Importing into PostgreSQL
To combine all `JSON` files into [Newline Delimeted JSON](http://ndjson.org/) suitable for import into PostgreSQL use the following make task

```sh
make dist/exercises.nd.json
```
_Note: requires [jq](https://stedolan.github.io/jq/)_

See also [Importing JSON into PostgreSQL using COPY](https://konbert.com/blog/import-json-into-postgres-using-copy)

### Browsable frontend

<img src="./site/public/screenshot.png" alt="Screenshot of browsable frontend" width="500">

There is a simple searchable/browsable frontend to the data written in [Vue.js](https://vuejs.org/)  available at [yuhonas.github.io/free-exercise-db](https://yuhonas.github.io/free-exercise-db/) all related code is in the [site](./site) directory


#### Setup

```sh
npm install
```

#### Compile and Hot-Reload for Development

```sh
npm run dev
```

#### Compile and Minify for Production

```sh
npm run build
```

#### Run Unit Tests with [Vitest](https://vitest.dev/)

```sh
npm run test:unit
```

#### Run End-to-End Tests with [Cypress](https://www.cypress.io/)

```sh
npm run test:e2e:dev
```

This runs the end-to-end tests against the Vite development server.
It is much faster than the production build.

But it's still recommended to test the production build with `test:e2e` before deploying (e.g. in CI environments):

```sh
npm run build
npm run test:e2e
```

#### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```

### Translation guide

This database can be translated using the `llm_translator.py` script. To get started, follow these steps:

#### Install ollama and choose your model

Visit the ollama website (https://ollama.com/) and install it. Download the desired model. Currently, the `aya 35b` model is provide high-quality translations in french.

Adjust the model size or quantization degree according to your system configuration. For reference, it took around 30 hours to complete on a system with an Nvidia 3070, 40GB RAM, and an i7-11700 processor, utilizing 66% of the CPU and 34% of the GPU, as reported by `ollama ps`.

#### Configure the Script and run it

In the `llm_translator.py` script, update the configuration section to match your desired translation language.

Once you've completed these steps, you're ready to translate the database to your favorite language using following command :

```sh
python3 llm_translator.py
```

### WEBP and GIF builder

Adapt the configuration part of the script `animated_img_builder.py` to use your favorite timing.

The script will combine jpg images into one animated one.
```sh
python3 animated_img_builder.py
```

### SQL insert generator for wger app

#### Wger Exercise Database Builder
The script `wger_insert_builder.py`, was created to generate SQL insertions for the Wger project, a comprehensive exercise management system.

#### Configuration
Before running the script, please update the configuration at the top of the file to match your specific use case.

#### Multilingual Support
If you plan to insert exercises in multiple languages, note that the current script will keep the same main name in English and only change the instructions. However, you could easily adapt `llm_translator.py` and `wger_insert_builder.py` to translate both the exercise names and instructions.

#### Database Preparation
To use this script, you'll need to provide the current maximum IDs for the relevant tables. To do this, run a `SELECT MAX(id) from my_table` query on each table. However, be aware that history table PK is history_key.

Before running the script, you could clear the database by running the `clear_exercises.sql` script to remove any existing data, ensuring a clean state for the import.


### Special help for Java devs
Just kidding, I'm one of them :)
```sh
python3 wger_insert_builder.py
```

#### Image Management
During script execution, dynamic WebP images will be dispatched in the /media/exercise_images/ directory. Make sure to move the entire folder to the location used by your application. If you're using Wger's Docker Compose setup, this refers to the volume referenced as media.

#### Acknowledgments
Huge thanks to the Wger project and the Free Exercise DB project, along with their amazing contributors, for creating such powerful tools for exercise management and providing a comprehensive database of exercises ! 
&hearts;
&hearts;
&hearts;

I hope my utility scripts can be helpful to others as well! :)

### TODO

#### Incomplete fields

The following fields are incomplete in _some_ `JSON` files and in such have had to allow `null` in [schema.json](./schema.json)

* force
* mechanic
* equipment

#### Images

There are also a small number of duplicate images eg.

```sh
jdupes --summarize --recurse .

Scanning: 2620 files, 874 items (in 1 specified)
25 duplicate files (in 22 sets), occupying 809 KB
```

### Contributors

<a href="https://github.com/yuhonas/free-exercise-db/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=yuhonas/free-exercise-db" />
</a>

Made with [contrib.rocks](https://contrib.rocks).

Contributions are always welcome! Please read the contribution guidelines first.

### Special Thanks 🙇
* [Ollie Jennings](https://github.com/OllieJennings) for the original dataset at [exercises.json](https://github.com/wrkout/exercises.json)
* flaticon for the favicon see [Sports-and-competition icons created by Dragon Icons - Flaticon](https://www.flaticon.com/free-icons/sports-and-competition)
