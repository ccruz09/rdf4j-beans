# rdf4j-beans
[![Build Status](https://travis-ci.org/kburger/rdf4j-beans.svg?branch=develop)](https://travis-ci.org/kburger/rdf4j-beans)
[![Coverage Status](https://coveralls.io/repos/github/kburger/rdf4j-beans/badge.svg?branch=develop)](https://coveralls.io/github/kburger/rdf4j-beans?branch=develop)
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/05b1b8037272416a8d925ec2ce160d54)](https://www.codacy.com/app/burger/rdf4j-beans?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=kburger/rdf4j-beans&amp;utm_campaign=Badge_Grade)
[![Dependency Status](https://www.versioneye.com/user/projects/58c674c662d60200434c7ef9/badge.svg?style=flat-square)](https://www.versioneye.com/user/projects/58c674c662d60200434c7ef9)
[![Maven Central](https://img.shields.io/maven-central/v/com.github.kburger/rdf4j-beans.svg)](https://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.github.kburger%22%20a%3A%22rdf4j-beans%22)
[![Javadocs](https://javadoc.io/badge/com.github.kburger/rdf4j-beans.svg)](https://javadoc.io/doc/com.github.kburger/rdf4j-beans)

Java beans IO library for RDF4J.

# usage
Add the `rdf4j-beans` dependency in your POM:
```xml
<dependency>
    <groupId>com.github.kburger</groupId>
    <artifactId>rdf4j-beans</artifactId>
    <version>0.4.0-SNAPSHOT</version>
</dependency>
```

Annotate your beans:
```java
@Type("http://example.com/Type")
public class MyBean {
    @Predicate(value = "http://purl.org/dc/terms/title", isLiteral = true)
    private String title;
    
    public MyBean(String title) {
        this.title = title;
    }
    
    public String getTitle() {
        return title;
    }
}
```

Invoke the mapper:
```java
BeanMapper mapper = new BeanMapper();
mapper.write(new OutputStreamWriter(System.out), new MyBean("example"),
        "http://example.com/subject", RDFFormat.TURTLE);
```

Or read from source:
```
@prefix ex: <http://example.com/> .
@prefix dc: <http://purl.org/dc/terms> .

ex:subject a ex:Type ;
    dc:title "Hello world!" .
```

```java
MyBean bean = mapper.read(new StringReader(content), MyBean.class,
        "http://example.com/subject", RDFFormat.TURTLE);
assert bean.getTitle().equals("Hello world!");
```

# mix-ins
Mix-in annotations allow you to enrich non-annotated classes with rdf4j-beans annotations. 

Given the following example class:
```java
public class ExampleBean {
    private String value;
    
    public ExampleBean(String value) {
        this.value = value;
    }
    
    public String getValue() {
        return value;
    }
}
```

A `@Predicate` annotation can be mixed in using an interface:
```java
public interface MixIn {
    @Predicate(value = "http://example.com/value", isLiteral = true)
    String getValue();
}
```

The effect applied using the following snippet:
```java
BeanMapper mapper = new BeanMapper();
mapper.getAnalyzer().registerMixIn(ExampleBean.class, MixIn.class);

mapper.write(new OutputStreamWriter(System.out), new ExampleBean("value"),
    "http://example.com/", RDFFormat.TURTLE);
```

The snippet above will produce this result:
```
<http://example.com/> <http://example.com/value> "value" .
```

# changelog
0.4.0
- Support for mix-ins.

0.3.0
- Support for inherited properties.

0.2.0
- Support for bean deserialization.
- Fix for handling property null values. 

0.1.0
- Initial release.
- Support for bean serialization.
