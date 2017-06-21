# Solr workshop

[Solr](http://lucene.apache.org/solr/) application workshop para [nerdear.la 2017](https://nerdear.la).

En este workshop vamos a crear una simple Search API para http://www.meetup.com

El workshop esta diseñado como una serie de ejercicios a completar/implementar.

## instalar previamente

* Java 8
* Solr 6.x
* Maven 3
* Git
* IDE: Intellij, Eclipse con Kotlin plugin o editor de texto.

## workshop

### prototipo utilizando schema-less

* Descargar e instalar solr: `${solrhome}`
* Iniciar Solr: `${solrhome}/bin/solr start`
* Abrir Solr dashboard: http://localhost:8983
* Crear core/collection: `${solrhome}/bin/solr create -c nerdearla`
* Visualizar nuevo core en dashboard
* Indexar documentos `${solrhome}/bin/post -c nerdearla meetup/*.json`
* Verificar documentes desde Solr dashboard: http://localhost:8983

### schema.xml y solrconfig.xml

* Inicialmente Solr utiliza un **managed-schema** (tambien conocido como schema-less) util para prototipar rapidamente Search applications.

* Una vez finalizado el prototipo, conviene administratar manualmente tanto el esquema (schema.xml) como la consulta de datos (solrconfig.xml). Para ello: 

  * Detenemos Solr: `${solrhome}/bin/solr stop`

  * Reemplazemos `${solrhome}/server/solr/nerdearla` por el directorio del mismo nombre localizada en la raiz de este proyecto.

  * Iniciamos Solr: `${solrhome}/bin/solr start`

  * Indexamos nuevamente data: `${solrhome}/bin/post -c nerdearla meetup/*.json`

  * Analisis del nuevo esquema de datos (`schema.xml`) y consulta de datos (`solrconfig.xml`) utilizando el dashboard: http://localhost:8983/solr/#/nerdearla/files

### ejercicios

> Si modificaste `solrconfig.xml` o `schema.xml` es necesario *recargar* Solr. Esto se puede realizar mediante `start/stop` (por supuesto) o [dinamicamente](http://localhost:8983/solr/admin/cores?action=RELOAD&core=nerdearla&wt=json)

* Modificar el [request handler](http://localhost:8983/solr/nerdearla/select?q=*:*) de modo que sea JSON el formato utilizado por defecto y no `XML`.
  * [Solución](solucion/solrconfig.xml#L28)

* Utilizando [filter queries](https://cwiki.apache.org/confluence/display/solr/Common+Query+Parameters#CommonQueryParameters-Thefq(FilterQuery)Parameter) listar:
  * Todos los miembros.
    * [Solución](http://localhost:8983/solr/nerdearla/select?q=*:*&fq=type:member)
  * Todos los eventos.
    * [Solución](http://localhost:8983/solr/nerdearla/select?q=*:*&fq=type:event)
  * Todos los grupos.
    * [Solución](http://localhost:8983/solr/nerdearla/select?q=*:*&fq=type:group)
  * Todos los eventos de [JUG Argentina](https://www.meetup.com/jugargentina).
    * [Solución](http://localhost:8983/solr/nerdearla/select?q=*:*&fq=type:group&fq=group:jugargentina)

* Modificar [request handler](http://localhost:8983/solr/nerdearla/select?q=*:*) para que retorne facets results para:
  * Grupos.
    * [Solución](http://localhost:8983/solr/nerdearla/select?q=*:*&facet=true&facet.field=group)
  * Topicos.
    * [Solución](http://localhost:8983/solr/nerdearla/select?q=*:*&facet=true&facet.field=topics)

* Implementar un *type ahead* [request handler](http://localhost:8983/solr/nerdearla/members?q=Ed) para miembros. De manera que:
  * Si tengo como entrada `Ed` devuelve todas las personas cuyo nombre comience con `Ed`
  * Solo retorne los siguientes campos/atributos: `name`, `link` y `photo`
    * [Solución](solucion/schema.xml#L60-L76)
    * [Solución](solucion/solrconfig.xml#L33-L42)
