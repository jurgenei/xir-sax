# gradle-xml-plugin: Minimal S-Expression Support MVP

## Objective

Add first-class S-expression input/output support to the existing Saxon infrastructure with the smallest possible implementation.

Do not add:

- Semantic MathML rewriting
- GraphML rewriting
- Neo4j integration
- URI schemes
- Custom Saxon tree models
- iXML support

Only implement lossless S-expression serialization/deserialization and Gradle integration.

---

## Scope

### Read

```text
.xir
   ↓
xiressionParser
   ↓
SAXSource
   ↓
Saxon
```

### Write

```text
Saxon XDM
     ↓
xiressionSerializer
     ↓
.xir
```

---

## Canonical Format

### Elements

XML

```xml
<book>
  <title>XML</title>
</book>
```

S-expression

```lisp
(book
  (title "XML"))
```

### Attributes

XML

```xml
<book id="b1"/>
```

S-expression

```lisp
(book
  [id "b1"])
```

### Mixed example

XML

```xml
<book id="b1">
  <title>XML</title>
</book>
```

S-expression

```lisp
(book
  [id "b1"]
  (title "XML"))
```

---

## Java Components

### xiressionParser

Input:

```lisp
(book [id "b1"] (title "XML"))
```

Output:

SAX events.

Public API:

```java
class xiressionParser {
    void parse(Reader reader, ContentHandler handler);
}
```

---

### xiressionXmlReader

Adapter from parser to SAXSource.

```java
class xiressionXmlReader implements XMLReader
```

Usage:

```java
Source source = new SAXSource(
    new xiressionXmlReader(),
    new InputSource(reader)
);
```

---

### xiressionSerializer

Consumes SAX events.

```java
class xiressionSerializer
    implements ContentHandler
```

Output:

```lisp
(book
  [id "b1"]
  (title "XML"))
```

---

## Gradle Integration

### New Document Type

```kotlin
enum class XmlDocumentType {
    XML,
    xir
}
```

---

### Input

Allow:

```kotlin
xslt {
    input.set(file("input.xir"))
}
```

When extension is:

```text
.xir
```

create:

```java
SAXSource(xiressionXmlReader)
```

instead of XML parser.

---

### Output

Allow:

```kotlin
xslt {
    output.set(file("output.xir"))
}
```

When extension is:

```text
.xir
```

attach:

```java
xiressionSerializer
```

instead of XML serializer.

---

## Acceptance Criteria

Roundtrip succeeds:

```text
input.xml
   ↓
xmlToxir
   ↓
a.xir
   ↓
xirToXml
   ↓
b.xml
```

And:

```text
canonicalize(input.xml)
==
canonicalize(b.xml)
```

No semantic loss.

---

## Deliverables

1. xiressionParser
2. xiressionXmlReader
3. xiressionSerializer
4. Gradle file-extension based Source selection
5. Gradle file-extension based Result selection
6. Roundtrip integration test

Everything else is out of scope.
