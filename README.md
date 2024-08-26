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

You should see a screen that looks similar to below. If you do not see it, go the top navigation bar, click on Assets, then go the top-right region and click on View global asset lineage. As more assets are built, this graph will show your entire data platform in one place. This page can also be referred to as the Global Asset Lineage.

![image info](https://docs.dagster.io/_next/image?url=%2Fimages%2Ftutorial%2Fdefault-ui.png&w=3840&q=75)

## Materialize your asset
To materialize an asset means to create or update it. Dagster materializes assets by executing the asset's function or triggering an integration.

To manually materialize an asset in the UI, click the Materialize button in the upper right corner of the screen. This will create a Dagster run that will materialize your assets.

To follow the progress of the materialization and monitor the logs, each run has a dedicated page. To find the page:

1) Click on the Runs tab in the upper navigation bar

2) Click the value in the Run ID column on the table of the Runs page

3) The top section displays the progress, and the bottom section live updates with the logs

![image info](https://docs.dagster.io//_next/image?url=%2Fimages%2Ftutorial%2Frun-status-page.png&w=3840&q=75)
