

# Built-in guides

Property graph model concepts

```
:guide concepts
```
Cypher basics - create, match, delete

```
:guide cypher
```

Movie graph, rekommenderas

```
:guide movie-graph
```

Ladda massa csv. Kul om "northwind" väcker minnen.

```
:guide northwind-graph
```

# Random exempel

Massa data med interaktioner mellan karaktärer i GoT. 


```
:play https://guides.neo4j.com/got
```
OBS: fick lite syntax errors som behövde fixas. Ex `CREATE CONSTRAINT FOR (c:Character) REQUIRE  c.name IS UNIQUE;`


# Exempel från Designing Data-Intensive Applications boken

```

// Create continent nodes
CREATE (northAmerica:Location {name: 'North America', type: 'Continent'})
CREATE (europe:Location {name: 'Europe', type: 'Continent'})


// US nodes
CREATE (usa:Location {name: 'United States', type: 'Country'})
CREATE (idaho:Location {name: 'Idaho', abbreviation: 'ID', type: 'State'})

// US connect
CREATE (usa)-[:WITHIN]->(northAmerica)
CREATE (idaho)-[:WITHIN]->(usa)


// UK nodes
CREATE (uk:Location {name: 'United Kingdom', type: 'Country'})
CREATE (england:Location {name: 'England', type: 'CountrySubdivision'})
CREATE (london:Location {name: 'London', type: 'City'})

// UK connect
CREATE (uk)-[:WITHIN]->(europe)
CREATE (england)-[:WITHIN]->(uk)
CREATE (london)-[:WITHIN]->(england)


// France nodes
CREATE (france:Location {name: 'France', type: 'Country'})
CREATE (bourgogne:Location {name: 'Burgundy', name_fr: 'Bourgogne', type: 'Region'})
CREATE (cotedor:Location {name: "Côte-d'Or", type: 'Department'})
CREATE (beaune:Location {name: 'Beaune', type: 'City'})

// France connect
CREATE (france)-[:WITHIN]->(europe)
CREATE (bourgogne)-[:WITHIN]->(france)
CREATE (cotedor)-[:WITHIN]->(bourgogne)
CREATE (beaune)-[:WITHIN]->(cotedor)


// Persons

CREATE (lucy:Person {name: 'Lucy'})
CREATE (alain:Person {name: 'Alain'})


// Person relationships
CREATE (lucy)-[:BORN_IN]->(idaho)
CREATE (lucy)-[:LIVES_IN]->(london)
CREATE (alain)-[:LIVES_IN]->(london)
CREATE (alain)-[:BORN_IN]->(beaune)
CREATE (lucy)-[:MARRIED]->(alain)

```

Query som visar personer som migrerat från US till Europa

```

MATCH
  (person) -[:BORN_IN]->  () -[:WITHIN*0..]-> (us:Location {name:'United States'}),
  (person) -[:LIVES_IN]-> () -[:WITHIN*0..]-> (eu:Location {name:'Europe'})
RETURN person


```

### Side note - jämförelse med relationsdatabas

Det här är motsvarande query i hypotetisk relationsdatabas med generella edges och vertices.


```sql 


WITH RECURSIVE

  -- in_usa is the set of vertex IDs of all locations within the United States
  in_usa(vertex_id) AS (
      SELECT vertex_id FROM vertices WHERE properties->>'name' = 'United States' 
    UNION
      SELECT edges.tail_vertex FROM edges 
        JOIN in_usa ON edges.head_vertex = in_usa.vertex_id
        WHERE edges.label = 'within'
  ),

  -- in_europe is the set of vertex IDs of all locations within Europe
  in_europe(vertex_id) AS (
      SELECT vertex_id FROM vertices WHERE properties->>'name' = 'Europe' 
    UNION
      SELECT edges.tail_vertex FROM edges
        JOIN in_europe ON edges.head_vertex = in_europe.vertex_id
        WHERE edges.label = 'within'
  ),

  -- born_in_usa is the set of vertex IDs of all people born in the US
  born_in_usa(vertex_id) AS ( 
    SELECT edges.tail_vertex FROM edges
      JOIN in_usa ON edges.head_vertex = in_usa.vertex_id
      WHERE edges.label = 'born_in'
  ),

  -- lives_in_europe is the set of vertex IDs of all people living in Europe
  lives_in_europe(vertex_id) AS ( 
    SELECT edges.tail_vertex FROM edges
      JOIN in_europe ON edges.head_vertex = in_europe.vertex_id
      WHERE edges.label = 'lives_in'
  )

SELECT vertices.properties->>'name'
FROM vertices
-- join to find those people who were both born in the US *and* live in Europe
JOIN born_in_usa     ON vertices.vertex_id = born_in_usa.vertex_id 
JOIN lives_in_europe ON vertices.vertex_id = lives_in_europe.vertex_id;

```


Databasschemat... Extremt simpelt och helt generellt, allt som behövs för att implementera en grafdatabas i en relationsdatabas.

```sql
CREATE TABLE vertices (
    vertex_id   integer PRIMARY KEY,
    properties  json
);

CREATE TABLE edges (
    edge_id     integer PRIMARY KEY,
    tail_vertex integer REFERENCES vertices (vertex_id),
    head_vertex integer REFERENCES vertices (vertex_id),
    label       text,
    properties  json
);

CREATE INDEX edges_tails ON edges (tail_vertex);
CREATE INDEX edges_heads ON edges (head_vertex;
```