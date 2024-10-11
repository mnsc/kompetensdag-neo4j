---
marp: true
theme: default
paginate: true
footer: 'Grafdatabasworkshop 2024-10-11'
---

# Grafdatabaser

---

# Vad ska man välja?

|             | **Dokumentdatabas**       | **Grafdatabas**               | **Relationsdatabas**       |
|-------------|---------------------------|-------------------------------|----------------------------|
| **Vad**     | JSON-liknande dokument     | Noder och relationer         | Tabeller, rader, kolumner  |
| **Hur**     | Varierar (ex. MongoDB)     | Cypher (Neo4j)               | SQL                        |
| **Varför**  | Ostrukturerad data         | Nätverk, komplexa relationer | Struktur, integritet, transaktionstungt    |

---

## Grafdatabas, generellt

* DAG, directed acyclic graph
* Uppbyggnad
  - Noder, verticies
  - Kanter, relationer, edges
  - Oftast enkelriktade kanter, (från-till)
* ![w:173 h:57 alt text](image-1.png)

---


## Typer av grafdatabaser

* Property-Based Grafdatabas
  - Noder och relationer 
  - Både kan ha en lista med egenskaper som nyckel-värde-par.
  - "NoSQL" schemaless

* Triple Store
  - Noder och relationer modelleras som (subjekt-predikat-objekt)
  - Semantiska webben, ontologier
  - Strikt, närmar sig prolog :fearful:
  - Inte i fokus!

---

## Property based, neo4j

* Labels, relationships, nodes, properties

![w:500 h:375  alt text](image.png)

---

