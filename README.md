# Dagster

## Your first asset

In Dagster, the main way to create data pipelines is by writing assets.

Let's explore these:
<button data-command="open:tutorial/assets.py">Open `assets.py`</button>

### Ingesting data

To get started, we fetch data from the Hacker News API, create a list of integers representing the IDs for the current top stories on Hacker News and finally, store them in a file called `data/topstory_ids.json`.

Adding the `@asset` decorator from the dagster library is all it takes to let Dagster know that this is an asset.

## Exploring the Dagster UI

Using Dagster's UI, you can explore your data assets, manually launch runs, and observe what's happening during pipeline runs.

Let's launch the dagster server:
<button data-command="run:dagster dev">Run `dagster dev`</button>

<insert-text file="tutorial/assets.py" line="15" col="0">

```python
@asset
```

</insert-text>

You should see a screen that looks similar to below. If you do not see it, go the top navigation bar, click on **Assets**, then go the top-right region and click on **View global asset lineage**. As more assets are built, this graph will show your entire data platform in one place. This page can also be referred to as the **Global Asset Lineage**.

![image info](https://docs.dagster.io/_next/image?url=%2Fimages%2Ftutorial%2Fdefault-ui.png&w=3840&q=75)

## Materialize your asset

To **materialize** an asset means to create or update it. Dagster materializes assets by executing the asset's function or triggering an integration.

To manually materialize an asset in the UI, click the **Materialize** button in the upper right corner of the screen. This will create a Dagster run that will materialize your assets.

To follow the progress of the materialization and monitor the logs, each run has a dedicated page. To find the page:

1) Click on the **Runs** tab in the upper navigation bar

2) Click the value in the **Run ID** column on the table of the Runs page

3) The top section displays the progress, and the bottom section live updates with the logs

![image info](https://docs.dagster.io//_next/image?url=%2Fimages%2Ftutorial%2Frun-status-page.png&w=3840&q=75)

## The DataFrame asset

Using the top Hacker News story IDs, we can look up each story by its ID, ingest that data, and make a DataFrame out of it. Connecting your current asset with this new asset establishes dependencies and makes an asset graph.

Let's return to our asset definitions:
<button data-command="open:tutorial/assets.py">Open `assets.py`</button>

Dependencies between assets are defined using the deps parameter of the @asset decorator. In this case, topstory_ids (the list of IDs) is a dependency of topstories (the CSV file).

You can check the asset graph to see the relationship between your assets.

## Creating an unstructured data asset

Along with structured data like tables, Dagster's assets can also be unstructured data, such as JSON files or images. The final asset takes the DataFrame of stories to create a dictionary of the most frequent words in the titles.

See the `most_frequent_words` asset:
<button data-command="open:tutorial/assets.py">Open `assets.py`</button>

## Educating users with metadata

The MaterializeResult object lets you record metadata about your asset.

Asset definitions can be enriched with different types of metadata. Anything can be used as metadata for an asset. Common details to add are:

* Statistics about the data, such as row counts or other data profiling
* Test results or assertions about the data
* Images or tabular previews of the asset
* Information about who owns the asset, where it's stored, and links to external documentation

Our code adds a row count and a preview of the topstories asset. 

The metadata can then be seen in the following places:

* In the Asset graph page, click on an asset and its metadata will be shown in the right sidebar:
* In the Asset Catalog's page for the topstories asset:
