# Manual de criação OpenStreetMap local

## Exporta do banco open street map para um .osm

```bash
osmosis --read-apidb host="localhost:54321" database="openstreetmap" user="openstreetmap" password="openstreetmap" validateSchemaVersion=no --write-xml file="saida.osm"
```

## Converte o .osm para .pbf, se tiver erro removar o compressionMethod
```bash
osmosis --read-xml file="saida.osm" --write-pbf file="saida.pbf"
```

## Cria um servidor tile do .osm exportado do banco do open street map

https://switch2osm.org/serving-tiles/using-a-docker-container/

## Se der um na role renderer é só apagar todos container do overv de import e run e criar novamente o volume
## Pare todos container que tem relacionamento com o volume osm-data e depois remova

```bash
sudo docker ps -q --filter volume=osm-data | xargs -r sudo docker stop
sudo docker ps -aq --filter volume=osm-data | xargs -r sudo docker rm
```

## Remova o volume osm-data se existir
```bash
sudo docker volume remove osm-data
```

## Agora crie novamente o volume

```bash
sudo docker volume create osm-data
```

```bash
sudo docker run \
    -v "/home/zeldris/Área de trabalho/open_street_map/saida.pbf:/data/region.osm.pbf" \
    -v osm-data:/data/database/ \
    overv/openstreetmap-tile-server import
```

## Depois é só ativar o container
```bash
sudo docker run -p 80:80 -v osm-data:/data/database -d overv/openstreetmap-tile-server run
```
### Alterar no openstreetmap web o para usar o tile acima

## Configurar o OSRM

## Configurar arquivo para uso no OSMand:

https://www.osmand.net/docs/technical/map-creation/create-offline-maps-yourself

```bash
wget -N http://download.osmand.net/latest-night-build/OsmAndMapCreator-main.zip
unzip OsmAndMapCreator-main.zip -d OsmAndMapCreator
```

```bash
OsmAndMapCreator/utilities.sh generate-poi saida.pbf --chars-build-poi-nameindex=3
```

## Pode acontecer problema com a memoria se o arquivo for muito grande, para evitar isso altere a limitação de memoria no arquivo:

```bash
nano OsmAndMapCreator/utilities.sh
```

```java
JAVA_OPTS="-Xms64M -Xmx512M" -> padrão
JAVA_OPTS="-Xms2G -Xmx8G"
```
