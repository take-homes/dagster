# Dagster

## Intro to Dagster

Dagster is an orchestrator that's designed for developing and maintaining data assets, such as tables, data sets, machine learning models, and reports.

In this tutorial, you'll analyze activity on the popular news aggregation website, Hacker News. You'll fetch data from the website, clean it up, and build a report that summarizes some findings. You'll then tell Dagster to occasionally update the data and the report, which Dagster calls assets.

## Introduction to asset definitions

An asset is an object in persistent storage that captures some understanding of the world. Assets can be any type of object, such as:

* A database table or view
* A file, such as in your local machine or blob storage like Amazon S3
* A machine learning model
  
If you have an existing data pipeline, you likely already have assets.

Asset definitions are a Dagster concept that allows you to write data pipelines in terms of the assets that they produce.

**How they work:** you write code that describes an asset that you want to exist, along with any other assets that the asset is derived from, and a function that can be run to compute the contents of the asset.

## Building a DAG of assets

A set of assets forms a [DAG (directed acyclic graph)](https://en.wikipedia.org/wiki/Directed_acyclic_graph), where the edges correspond to data dependencies between assets. This DAG helps you to:

* Understand how your assets relate to each other
* Empower you and your teammates to act, learn, and debug your pipelines

## Your first asset

### Ingesting data

To get started, let's explore some code:
<button data-command="open:tutorial/assets.py">Open `assets.py`</button>

In the function `topstory_ids()`, we fetch data from the Hacker News API, create a list of integers representing the IDs for the current top stories on Hacker News and finally, store them in a file called `data/topstory_ids.json`.

Adding the `@asset` decorator from the `dagster` library is all it takes to let Dagster know that this is an asset.

<insert-text file="./tutorial/assets.py" line="15" col="0">
```python
@asset
```
</insert-text>

### Exploring the Dagster UI

Using Dagster's UI, you can explore your data assets, manually launch runs, and observe what's happening during pipeline runs.

Let's launch the dagster server:
<button data-command="run:dagster dev">Run `dagster dev`</button>

You should see a screen that looks similar to below. If you do not see it, go the top navigation bar, click on **Assets**, then go the top-right region and click on **View global asset lineage**. As more assets are built, this graph will show your entire data platform in one place. This page can also be referred to as the **Global Asset Lineage**.

![image info](https://docs.dagster.io/_next/image?url=%2Fimages%2Ftutorial%2Fdefault-ui.png&w=3840&q=75)

### Materialize your asset

To **materialize** an asset means to create or update it. Dagster materializes assets by executing the asset's function or triggering an integration.

To manually materialize an asset in the UI, click the **Materialize** button in the upper right corner of the screen. This will create a Dagster run that will materialize your assets.

To follow the progress of the materialization and monitor the logs, each run has a dedicated page. To find the page:

1) Click on the **Runs** tab in the upper navigation bar

2) Click the value in the **Run ID** column on the table of the Runs page

3) The top section displays the progress, and the bottom section live updates with the logs

![image info](https://docs.dagster.io//_next/image?url=%2Fimages%2Ftutorial%2Frun-status-page.png&w=3840&q=75)

### The DataFrame asset

Using the top Hacker News story IDs, we can look up each story by its ID, ingest that data, and make a DataFrame out of it. Connecting your current asset with this new asset establishes dependencies and makes an asset graph.

Let's return to our code and check out the function `topstories()`:
<button data-command="open:tutorial/assets.py">Open `assets.py`</button>

Dependencies between assets are defined using the deps parameter of the @asset decorator. In this case, `topstory_ids` (the list of IDs) is a dependency of `topstories` (the CSV file).

Let's update the code to reflect this:

<insert-text file="tutorial/assets.py" line="30" col="0">
```python
@asset(deps=[topstory_ids])  # this asset is dependent on topstory_ids
```
</insert-text>

After clicking the **Reload Definitions** button in the UI, you can check the asset graph to see the relationship between your assets.

### Creating an unstructured data asset

Along with structured data like tables, Dagster's assets can also be unstructured data, such as JSON files or images. The final asset takes the DataFrame of stories to create a dictionary of the most frequent words in the titles.

See `most_frequent_words`:
<button data-command="open:tutorial/assets.py">Open `assets.py`</button>

Lets update the mark this function as an asset:

<insert-text file="tutorial/assets.py" line="45" col="0">
```python
@asset(deps=[topstory_ids])
```
</insert-text>

### Educating users with metadata

The `MaterializeResult` object lets you record metadata about your asset.

Asset definitions can be enriched with different types of metadata. Anything can be used as metadata for an asset. Common details to add are:

* Statistics about the data, such as row counts or other data profiling
* Test results or assertions about the data
* Images or tabular previews of the asset
* Information about who owns the asset, where it's stored, and links to external documentation

Our code adds a row count and a preview of the `topstories` asset. 

The metadata can then be seen in the following places:

* In the **Asset graph** page, click on an asset and its metadata will be shown in the right sidebar:
  ![image info](https://docs.dagster.io/_next/image?url=%2Fimages%2Ftutorial%2Fasset-metadata.png&w=3840&q=75)
* In the **Asset Catalog's** page for the `topstories` asset:
  ![image info](https://docs.dagster.io/_next/image?url=%2Fimages%2Ftutorial%2Fasset-catalog-metadata.png&w=3840&q=75)

### Metadata and Markdown

The DataFrame was embedded into the asset's metadata with Markdown. Any valid Markdown snippet can be stored and rendered in the Dagster UI, including images. By embedding a bar chart of the most frequently used words as metadata, you and your team can visualize and analyze the `most_frequent_words` asset without leaving the Dagster UI.

The bar chart will be visible with the rest of your materialization metadata for the `most_frequent_words` asset. The `path` key in the metadata will contain a link that says **[Show Markdown]**. Clicking on the link will open the preview in the Dagster UI. The bar chart will change throughout the day as the top stories change. Here's an example of what `most_frequent_words` looked like at the time we wrote this tutorial:
![image info](https://docs.dagster.io/_next/image?url=%2Fimages%2Ftutorial%2Fbar-chart-preview.png&w=3840&q=75)

## By now, you've:

* Written and materialized three assets
* Previewed the data in Dagster's UI
* Empowered stakeholders and your future self with metadata and logging


