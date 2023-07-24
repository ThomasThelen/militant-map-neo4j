# Militant Map Knowledge Graph

Stanford's Militant Mapping Project in Neo4j

## Data Model

The LPG data model is used to represent the different militant groups; relations between groups are represented by edges, which in turn have a property based on the date the relation is relevant for.

Militant groups are represented by nodes of type `MilitantGroup` and are connected to other groups though relationships which hold the bulk of the data.

## Data

The data from MMP is currently broken (invalid csv format) and requires several cleaning steps. The cleaned data files used by this project can be found in the `data/` folder.

The cleaning steps are

1. Remove empty columns from `Militant Group Key.csv`
2. Remove empty rows from `Militant Group Relationships.csv`
3. Replace `\"` characters with `'` from `Militant Group Relationships.csv`
4. Remove the non-csv header from `Militant Group Relationships.csv`
5. Replace spaces in the filenames with dashes

## Creating the Knowledge Graph

1. Create a new Neo4j project running 10.0.0.
2. Copy the data out of `data/` to the import directory

3. Loading the militant groups into the graph with

```CYPHER
LOAD CSV with headers FROM 'file:///Militant Group Key.csv' as row
CREATE (:MilitantGroup {name: row.name, group_id: row.group_id})
```

4. Add the relations between groups

```CYPHER
LOAD CSV WITH HEADERS FROM 'file:///Militant-Group-Relationships-clean.csv' as row
MATCH (group1:MilitantGroup {group_id:row.group1_id}), (group2:MilitantGroup {group_id:row.group2_id})
CREATE (group1)-[r:hasRelation]->(group2)
SET r.date=row.date, r.description=row.description, r.relation_type=row.type
```

## CYPHER Queries

Several queries and visuals that can be zoomed in if opened in a new tab.

### View all Groups & Relations

```CYPHER
MATCH(nodes) return (nodes)
```

<img src="images/full-graph.svg"  width="550">

### Query Relations to the Islamic State

```CYPHER
match(ISIS {name: 'The Islamic State'})-[:hasRelation]-(Range) return ISIS, Range
```

<img src="images/islamic-state.svg" width="550">
