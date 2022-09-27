# Reproducer for LOG4J2-3609

The project provides a minimal scenario for reproducing the issue reported under [LOG4J2-3609](https://issues.apache.org/jira/browse/LOG4J2-3609).

## Steps to reproduce

Create the necessary directories to hold the java binaries which compose the classpath.
```
mkdir -p target/log4j
mkdir -p target/annotation
mkdir -p target/core
mkdir -p target/bug
```
Download any log4j-core jar with an annotation processor and put it in target/lib directory.
```
wget -c https://repo1.maven.org/maven2/org/apache/logging/log4j/log4j-core/2.19.0/log4j-core-2.19.0.jar -P target/log4j
```
Compile the annotation and keep the class in a dedicated directory.
```
javac -d target/annotation src/MyAnnotation.java
```
Compile the annotated class and save it in a dedicated directory.
```
javac -d target/core -cp target/annotation src/MyAnnotatedClass.java
```
Compile the empty subclass, which depends transitively on `MyAnnotation`, without including `MyAnnotation.class` in the classpath.
``` 
javac -d target/bug -cp target/core:target/log4j/* src/MyEmptySubClass.java
```
When the log4j-core jar is in the classpath (i.e., target/lib) the compilation fails with the following error.
```
error: cannot access MyAnnotation
  class file for MyAnnotation not found
1 error
```
When the log4j-core jar is not in the classspath the compilation finishes sucessfully.
