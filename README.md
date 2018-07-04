# VTD Adjacency Graphs
A repository of rook- and queen-adjacency graphs of state VTDs,  made from 2012 Tiger/Line data.

## What's inside
All of these graphs were created using [v0.1.0](https://github.com/gerrymandr/graphmaker/releases/tag/v0.1.0) of [graphmaker](https://github.com/gerrymandr/graphmaker). This is the exact version that we used to build these graphs. Up to configuring the dependencies, the results should be exactly reproducible using that version of the graphmaker code.

### Folder structure
In the root directory, you'll find a folder for each[*](#asterisk) state, named by the 2-digit FIPS code for that state. You can find a matching of state names to FIPS codes [here](https://www.mcc.co.mercer.pa.us/dps/state_fips_code_listing.htm).

Within each state's directory you'll find three files:
- `rook.json`: The rook-adjacency graph of the state VTDs.
- `queen.json`: The queen-adjacency graph of the state VTDs.
- `report.json`: A JSON document containing some notable statistics (e.g. the distribution of the degrees of the nodes), for each of the graphs, along with a brief comparison of the queen- and rook-adjacency graphs.

### The data included in the graphs
As of now, each graph contains the following data:
- __Identifying data__
  - Nodes are named by their GEOIDs, read from the GEOID10 column in the original shapefile.
- __Boundary data__
  - Each node has a `'boundary_node'` attribute that is `True` if the node touches the border of the state, and `False` if it does not. If it does, the node also has a `'boundary_perim'` attribute specifying the length in meters of the boundary shared by the state and VTD corresponding to that node.
  - Each edge has a `'shared_perim'` attribute. This gives the length in meters of the shared boundary of the adjacent VTDs connected by the edge.

#### How we computed lengths
The `'boundary_perim'` and `'shared_perim'` lengths are in meters, computed in an appropriate UTM projection. For each state, we chose the UTM zone that contained the majority of the VTDs and computed the lengths in that projection. The code for this process is in the `graphmaker/geospatial.py` module.

#### What's missing?
We will soon be adding:
- Area in square meters for each VTD.
- 2010 Congressional District assignments for each VTD.
- 2010 Population counts for each VTD.

### Source shapefiles
The source shapefiles used to construct the graphs can be found at [https://www2.census.gov/geo/tiger/TIGER2012/VTD/](https://www2.census.gov/geo/tiger/TIGER2012/VTD/).

### Asterisk
The [TIGER2012/VTD/](https://www2.census.gov/geo/tiger/TIGER2012/VTD/) folder on the Census website did not have a shapefile for Rhode Island, so we are currently missing Rhode Island. We have another source for Rhode Island shapefiles that we will add to the repository soon.

## Loading a graph
These graphs can be parsed into a [NetworkX](https://networkx.github.io/documentation/stable/index.html) Graph object using the `networkx.readwrite.json_graph.adjacency_graph` method. Here's an example of how to load the rook-adjacency graph for Alaska (FIPS code 02):

```python
import networkx
import json

with open('./02/rook.json') as f:
    data = json.load(f)

graph = networkx.readwrite.json_graph.adjacency_graph(data)
```

## Usage with RunDMCMC
Our [RunDMCMC](https://github.com/gerrymandr/RunDMCMC) module gives you the power to run a Markov chain on the space of districting plans for a given state. You can use a graph from this repository with RunDMCMC to conduct a local sampling of districting plans.

Once we add (#whats-missing)[some more data] to these graphs, you will have the power to run a Markov chain on one of these graphs using RunDMCMC _without any additional set-up_.
