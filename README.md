# Transport Network Pathfinding Datasets

A collection of sample transportation-network datasets for students, educators, and developers working with graph theory, shortest-path algorithms, route planning, and network analysis.

The repository provides three dataset sizes—small, medium, and large—so implementations can be explored on networks with different numbers of stations and connections. All datasets are supplied as CSV files and can be used with common programming languages and data-analysis tools.

> **Important:** These are illustrative sample datasets for educational, testing, and demonstration purposes. They are not official transit records. Station coordinates, travel times, distances, fares, and service modes should not be treated as verified real-world information.

## Dataset Overview

| Dataset | Stations | Connections |
|---|---:|---:|
| Small | 22 | 33 |
| Medium | 144 | 306 |
| Large | 2,025 | 4,550 |
| **Total** | **2,191** | **4,889** |

Each dataset has a station file and a connection file.

## Repository Structure

```text
.
├── small_stations.csv
├── small_connections.csv
├── medium_stations.csv
├── medium_connections.csv
├── large_stations.csv
├── large_connections.csv
├── README.md
└── LICENSE
```

## Dataset Files

### Station files

Each `*_stations.csv` file contains the stations for its corresponding dataset.

| Column | Description |
|---|---|
| `station_id` | Unique identifier for a station within that dataset |
| `name` | Station name or sample stop label |
| `latitude` | Latitude coordinate |
| `longitude` | Longitude coordinate |

### Connection files

Each `*_connections.csv` file contains the connections between stations for its corresponding dataset.

| Column | Description |
|---|---|
| `from_id` | ID of the first station |
| `to_id` | ID of the connected station |
| `travel_time_min` | Illustrative travel time in minutes |
| `distance_km` | Illustrative distance in kilometres |
| `fare_pkr` | Illustrative fare value in Pakistani rupees (PKR) |
| `mode` | Sample transport mode, such as `bus`, `metro`, or `rail` |
| `bidirectional` | Indicates whether the connection is intended to work in both directions (`yes` or `no`) |

Station IDs are scoped to their dataset. For example, load `small_stations.csv` with `small_connections.csv`; do not mix files from different sizes unless your application explicitly handles their separate ID spaces.

## Choosing a Dataset

- **Small:** Useful for learning, debugging, classroom exercises, and manually checking algorithm results. It uses named locations associated with the Karachi area.
- **Medium:** A larger network with numbered stop labels, suitable for comparing algorithm behaviour as the graph grows.
- **Large:** A network with 2,025 stations and 4,550 connection records, useful for performance experiments and larger graph visualizations.

The medium and large datasets use generic stop names (for example, `Stop 1`) rather than a verified list of real station names. Coordinates in all files are provided for sample spatial and mapping experiments only.

## Getting Started with Python

Install the required libraries:

```bash
pip install pandas networkx
```

The following example loads the small dataset and builds an undirected weighted graph. Since the connection records in these files currently have `bidirectional` set to `yes`, each connection can be traversed in either direction.

```python
import pandas as pd
import networkx as nx

stations = pd.read_csv("small_stations.csv")
connections = pd.read_csv("small_connections.csv")

graph = nx.Graph()

# Add stations as nodes, including their sample coordinates.
for _, station in stations.iterrows():
    graph.add_node(
        station["station_id"],
        name=station["name"],
        latitude=station["latitude"],
        longitude=station["longitude"],
    )

# Add connections as weighted edges.
for _, connection in connections.iterrows():
    graph.add_edge(
        connection["from_id"],
        connection["to_id"],
        travel_time_min=connection["travel_time_min"],
        distance_km=connection["distance_km"],
        fare_pkr=connection["fare_pkr"],
        mode=connection["mode"],
    )

# Find a route using distance as the edge weight.
route = nx.shortest_path(
    graph,
    source="S01",
    target="S22",
    weight="distance_km",
)

print("Route:", " -> ".join(route))
```

To try another dataset size, load the matching pair of files—for example, `medium_stations.csv` and `medium_connections.csv`, or `large_stations.csv` and `large_connections.csv`.

### Handling connection direction

The `bidirectional` column describes how a connection should be interpreted. For these datasets, its value is `yes` for every listed record. An undirected graph (`nx.Graph`) is therefore a convenient choice.

If you need a directed graph, add the forward edge and add the reverse edge only when `bidirectional` is `yes`:

```python
graph = nx.DiGraph()

for _, connection in connections.iterrows():
    attributes = {
        "travel_time_min": connection["travel_time_min"],
        "distance_km": connection["distance_km"],
        "fare_pkr": connection["fare_pkr"],
        "mode": connection["mode"],
    }

    source = connection["from_id"]
    destination = connection["to_id"]

    graph.add_edge(source, destination, **attributes)

    if str(connection["bidirectional"]).strip().lower() == "yes":
        graph.add_edge(destination, source, **attributes)
```

## Algorithms and Project Ideas

These datasets can support exercises and experiments involving:

- **Dijkstra's algorithm:** Find a minimum-cost route using travel time, distance, or fare.
- **A* search:** Explore heuristic-based pathfinding using the coordinate fields.
- **Breadth-First Search (BFS):** Explore reachability and minimum-hop paths.
- **Depth-First Search (DFS):** Traverse the network.
- **Bellman-Ford:** Practice weighted shortest-path algorithms.
- **Floyd-Warshall:** Calculate shortest paths between all pairs of stations.
- **Minimum Spanning Tree (MST):** Explore minimum-cost network connectivity.
- **Network analysis:** Examine connected components, node degree, and network structure.
- **Visualization:** Plot stations and connections using mapping or graph-visualization libraries.
- **Performance comparison:** Compare algorithm execution across the small, medium, and large datasets.

For weighted shortest-path tasks, select the appropriate edge attribute (`travel_time_min`, `distance_km`, or `fare_pkr`) as the weight. The resulting route may differ depending on the selected attribute.

## Data Notes and Limitations

- The three sizes are separate datasets. Their station identifiers are not intended to form one combined network.
- Each station ID referenced by a connection should be looked up in the matching station file.
- The coordinates are sample values and should not be assumed to represent precise station entrances, actual routes, or surveyed locations.
- Named locations in the small dataset do not imply that the associated connections, modes, times, distances, or fares are official or currently operating.
- Medium and large station names are generic labels, not verified transit-stop names.
- Travel times and distances are illustrative. Fare values are sample PKR values, not published fares.
- The `mode` field provides sample categories and does not establish the existence of a real service.
- The datasets are intended for learning, prototyping, and controlled experiments—not operational navigation, passenger information, or transport planning.

## Contributing

Contributions are welcome. You can help by:

- Adding examples in other programming languages.
- Demonstrating additional graph algorithms.
- Improving documentation and usage examples.
- Providing validation scripts or visualization examples.
- Suggesting improvements to the dataset structure.

When modifying the CSV files, preserve the column names and ensure that every `from_id` and `to_id` references a station in the corresponding station file.

## License

This repository is distributed under the license included in the `LICENSE` file. Please review its terms before using or redistributing the contents.

---

If this repository helps with a class assignment, experiment, or project, consider starring it and sharing it with other students and developers.
