# graph_db — Neo4j dependency graph (Person B)

## What this folder does
Holds the Neo4j side of the project's polyglot storage: a package graph
scoped to the popular packages that Signal 1 candidates imitate. It is not
a graph of the whole PyPI/npm registry.

## Schema
- Node: `Package {ecosystem, name, is_popular}`, unique on `(ecosystem, name)`
- Edge: `(Package)-[:DEPENDS_ON]->(Package)`, loaded from the PyPI and npm
  registry APIs
- `IMPERSONATES` and `INSTALLED_BY` are reserved in the schema but not
  populated

## What the graph contains today
- 49 `Package` nodes and 43 `DEPENDS_ON` edges
- Forward direction only: each popular package points to what it depends on

## Known limitation: why blast radius is not computed here
Blast radius needs the reverse direction (who depends on a package). The
registry APIs only return forward dependencies, so traversing this graph
would show 0 dependents for almost every package. That would be a false
zero, not a real one.

Signal 2 therefore uses dependent counts from ecosyste.ms (deps.dev as
backup), stored in `data/blast_radius.csv` and fetched by
`scoring/blast_radius_fetch.py`. A missing value means unknown, never zero.
15 PyPI packages returned 0, which may mean no dependents are recorded.

## Files
- `schema_design.md` — schema and scoping decision
- `graph_loader.py` — loads packages and `DEPENDS_ON` edges into Neo4j
- `load_real_candidates.py` — reads candidates for the loader
- (list any other files in this folder here)

## Setup
- `pip install neo4j requests`
- Set your own Neo4j URI and password locally. Never commit credentials.

## Next step (not done)
Load reverse dependencies (dependents) into Neo4j so blast radius can be
computed by graph traversal instead of an external index.
