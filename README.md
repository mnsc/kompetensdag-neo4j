# Start

Kör `docker compose up`

Surfa till [http://localhost:7474/](http://localhost:7474/)

Logga in med neo4j och Password123

Strular det testa följande, men då kanske inte gui:t kan ladda ner från internet.  

`docker run --publish=7474:7474 --publish=7687:7687 --env NEO4J_AUTH=neo4j/Password123 --volume=./data:/data neo4j:5.23.0`

