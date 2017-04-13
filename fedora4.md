# Fedora 4

## Running a test Fedora

See [Fedora Quick Start](https://wiki.duraspace.org/display/FEDORA4x/Quick+Start) for link to download and some initial instrauctions. Having downloaded the "one-click-run" version of 4.7.2 as `fcrepo-webapp-4.7.2-jetty-console.jar` I have found it convenient to run with:

```
> rm -rf /tmp/fedora-data; mkdir /tmp/fedora-data; java -Dfcrepo.home=/tmp/fedora-data -jar fcrepo-webapp-4.7.2-jetty-console.jar --port 8080 --headless
```

which then starts a Fedora instance up on port 8080 and logs to console. (Without `--headless` there is a manual confirmation before starting.)

## Command line use of Fedora API

See [API documentation](https://wiki.duraspace.org/display/FEDORA4x/RESTful+HTTP+API).

`POST` a LDP-RS:

```
> curl -XPOST -H"Content-Type: text/turtle" --data '<> <http://example.org/p1> <http://example.org/r1>.' http://localhost:8080/rest
http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464(py3) simeon@Cider ~>

> curl -s -XGET -H"Accept: application/n-triples" http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://fedora.info/definitions/v4/repository#Container> .
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://fedora.info/definitions/v4/repository#Resource> .
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://fedora.info/definitions/v4/repository#lastModifiedBy> "bypassAdmin"^^<http://www.w3.org/2001/XMLSchema#string> .
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://fedora.info/definitions/v4/repository#createdBy> "bypassAdmin"^^<http://www.w3.org/2001/XMLSchema#string> .
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://fedora.info/definitions/v4/repository#created> "2017-04-13T01:40:48.741Z"^^<http://www.w3.org/2001/XMLSchema#dateTime> .
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://fedora.info/definitions/v4/repository#lastModified> "2017-04-13T01:40:48.741Z"^^<http://www.w3.org/2001/XMLSchema#dateTime> .
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://example.org/p1> <http://example.org/r1> .
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.w3.org/ns/ldp#RDFSource> .
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://www.w3.org/ns/ldp#Container> .
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://fedora.info/definitions/v4/repository#writable> "true"^^<http://www.w3.org/2001/XMLSchema#boolean> .
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://fedora.info/definitions/v4/repository#hasParent> <http://localhost:8080/rest/> .

> curl -s -XGET -H"Accept: application/n-triples" http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464 | grep example.org
<http://localhost:8080/rest/d8/84/e2/f8/d884e2f8-4c1b-4e93-a69d-f053dc85b464> <http://example.org/p1> <http://example.org/r1> .
```

Now with a `Slug`:

```
> curl -XPOST -H"Slug: a" -H"Content-Type: text/turtle" --data '<> <http://example.org/p2> <http://example.org/r2>.' http://localhost:8080/rest
http://localhost:8080/rest/a
> curl -s -XGET -H"Accept: application/n-triples" http://localhost:8080/rest/a | grep example.org
<http://localhost:8080/rest/a> <http://example.org/p2> <http://example.org/r2> .
```

Can also use subjects that are fragments of resource created:

```
> curl -XPOST -H"Slug: b" -H"Content-Type: text/turtle" --data '<> <http://example.org/p3> <http://example.org/r3>. <#frag> <http://example.org/p4> <http://example.org/r4>.' http://localhost:8080/rest
http://localhost:8080/rest/b
> curl -s -XGET -H"Accept: application/n-triples" http://localhost:8080/rest/b | grep example.org
<http://localhost:8080/rest/b> <http://example.org/p3> <http://example.org/r3> .
<http://localhost:8080/rest/b#frag> <http://example.org/p4> <http://example.org/r4> .
```

Or specify based on assumption that `Slug` is used:

```
> curl -XPOST -H"Slug: c" -H"Content-Type: text/turtle" --data '<http://localhost:8080/rest/c> <http://example.org/p6> <http://example.org/r6>.' http://localhost:8080/rest
http://localhost:8080/rest/c
> curl -s -XGET -H"Accept: application/n-triples" http://localhost:8080/rest/c | grep example.org
<http://localhost:8080/rest/c> <http://example.org/p6> <http://example.org/r6> .
```

But, requests to add triples where the subjects are not the resource or a fragment of the resource that contains them:

```
> curl -XPOST -H"Content-Type: text/turtle" --data '<http://example.org/s1> <http://example.org/p1> <http://example.org/r1>.' http://localhost:8080/rest
RDF Stream contains subject(s) (http://example.org/s1) not in the domain of this repository.

> curl -XPOST -H"Content-Type: text/turtle" --data '<http://localhost:8080/rest/a> <http://example.org/p1> <http://example.org/r1>.' http://localhost:8080/rest
http://localhost:8080/rest/a is not in the topic of this RDF, which is http://localhost:8080/rest/de/49/b7/d4/de49b7d4-6e24-4f37-9536-1286347d8242
```