SiteMesh 3: Official repository
=========

Currently maintained versions:

| 3.1.x | 3.2.x | 
| --------------- | --------------- |
| Java 8+ | Java 17+ |
| Servlet API 3.0-4.0.1 | Jakarta EE 10 |
| Spring Boot 2.x Support| Spring Boot 3.x Support |
|[3.1.x](https://github.com/sitemesh/sitemesh3/tree/3.1.x)| [Master Branch](https://github.com/sitemesh/sitemesh3)|
|[Java Docs](https://sitemesh.github.io/sitemesh3/javadoc/3.1.x/)||
| [Download](https://oss.sonatype.org/content/repositories/snapshots/org/sitemesh/sitemesh/3.1.0-SNAPSHOT/) | [Download](https://oss.sonatype.org/content/repositories/snapshots/org/sitemesh/sitemesh/3.2.0-SNAPSHOT/)|

[Website / Documentation (Built with Offline Generator)](https://sitemesh.github.io/sitemesh-website/)

## Building:
Currently, there are no maintained versions available on Maven Central. This will be change in the near future.
In the meantime, you can use the OSS snapshot repository

```groovy
repositories {
    mavenCentral() 
    maven { url "https://oss.sonatype.org/content/repositories/snapshots/" }
}

dependencies {
    runtimeOnly 'org.sitemesh:sitemesh:3.1.0-SNAPSHOT'
}
```

or you can build each respective version by checking out that particular branch as follows:

3.1.x
```
git clone https://github.com/sitemesh/sitemesh3
git checkout 3.1.x
./gradlew jar
```

3.2.x
```
git clone https://github.com/sitemesh/sitemesh3
./gradlew jar
```

a **jar** will be available in the following location

```
./sitemesh/build/libs/sitemesh-XX.XX.XX-SNAPSHOT.jar
```


## SiteMesh 3 Overview

* SiteMesh is a web-page layout and decoration framework and web- application integration framework to aid in creating sites consisting of pages for which a consistent look/feel, navigation and layout scheme is required.
* SiteMesh intercepts requests to any static or dynamically generated HTML page requested through the web-server, processes the content and then merges it with one or more decorators to build the final result.
* SiteMesh can also be used compose large pages of smaller pages and layouts.
* SiteMesh is fast. Really fast.
* SiteMesh can be used in Java based web-applications, or applied to content as an offline job.
* SiteMesh is extensible.

## What's New in SiteMesh 3?

SiteMesh 3 has been completely rebuilt from the ground up. It's more efficient, easier to use and extensible. While the code is new, it still holds the same values of SiteMesh 2, namely simplicity, robustness and performance.

### New Content Processor Architecture

The internals of how SiteMesh processes content have been radically changed. The result is typically a 3X gain in throughput and half the memory usage (based on some typical sized samples).

The new architecture also opens up new possibilities for upcoming features...

### Decorator Chaining

Decorators can now be chained together in a website. That is, content can be decorated, then decorated again (and again). The new architecture of the content processor means there is little overhead in applying multiple decorators.

### No Tie-ins to Templating Systems

No longer do you need to commit to JSP or Velocity to build your decorators. Like content, decorators can be generated by any technology - even static .html files.

### Simplified Configuration

Want to configure purely through Java code, through XML, through Spring, or maybe by convention? It's your choice. And it's easy to plug in your own configuration mechanism.

### Offline Site Generation

[Working Example Here](https://github.com/sitemesh/sitemesh-website/)

SiteMesh comes with tools to allow you decorators to content for static websites as an offline task. This can be invoked from a command line tool, an Ant task or through a Java API.

Can be used to cut the cost of hosting (no Java runtime needed on servers) or simplify distribution (e.g. include web-site as part of a downloaded package).

### Code Modernization

Under the hood, over 10 years worth of cruft has been cleaned up. This means a simpler API. And many long-standing design decisions have been revisited, which has made the things above possible, and opens the possibility of your own extensions.

The various extension APIs have been revisited to make the easier to understand, with useful hooks and clear documentation.

### Reusable Core Building Blocks

Many of the underlying building blocks of SiteMesh are useful to other applications. These have been decoupled from the code, allowing you to reuse them in your own application, even if you don't intend to use SiteMesh.

* The ContentBufferingFilter is a reusable Servlet Filter that intercepts responses and allows modifications to be made to the content.
* The HTML TagProcessor is a high-performant and extensible tag parser that allows for extraction and transformations of content.
### Relicensed under the Apache Software License v2.0

This license is used much more widely and approved by many organizations

## Getting Started with SiteMesh 3

### Introduction

This tutorial is a quick introduction to using SiteMesh3 in a web-application. It covers:

* A high level overview of how SiteMesh3 works
* Installation and configuration
* Building and applying a simple decorator
It is recommended you read the high level SiteMesh 3 Overview before this tutorial.

### SiteMesh in web applications

In a web application, SiteMesh acts as a Servler Filter. It allows requests to be handled by the Servlet engine as normal, but the resulting HTML (referred to as the content) will be intercepted before being returned to the browser.

The intercepted content has certain properties extracted (typically the contents of the <title>, <head> and <body> tags and is then passed on to a second request that should return the common look and feel for the site (referred to as the decorator). The decorator contains placeholders for where the properties extracted from the content should be inserted.

Under the hood, a key component of the SiteMesh architecture is the content processor. This is an efficient engine for transforming and extracting content from HTML content. For most users, it's fine to use it as it comes, but it is also possible to define your own transformation and extraction rules.

SiteMesh does not care what technologies are used to generate the content or the decorator. They may be static files, Servlet, JSPs, other filters, MVC frameworks, etc. So long as it's served by the Servlet engine, SiteMesh can work with it.

### Dependencies

Running SiteMesh3 requires at least:

* JDK 1.8
* A Servlet 2.5 compliant container
* The SiteMesh runtime library 
* The SiteMesh library should be downloaded and placed in /WEB-INF/lib/.

### Setup

Insert the SiteMesh Filter in /WEB-INF/web.xml:

```xml
<web-app>

  <filter>
    <filter-name>sitemesh</filter-name>
    <filter-class>org.sitemesh.config.ConfigurableSiteMeshFilter</filter-class>
  </filter>

  <filter-mapping>
    <filter-name>sitemesh</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

</web-app>
```
Deploy the web-application to your Servlet container. From this point onwards, this tutorial assumes the web-app is running at http://myserver/.

### Creating a decorator

The decorator contains the common layout and style that should be applied to the pages in the web application. It is a template that contains place holders for the content's <title>,<head> and <body> elements.

At the bare minimum, it should contain:

```html
<html>
  <head>
    <title><sitemesh:write property='title'/></title>
    <sitemesh:write property='head'/>
  </head>
  <body>
    <sitemesh:write property='body'/>
  </body>
</html>
```
The` <sitemesh:write property='...'/>` tag will be rewritten by SiteMesh to include properties extracted from the content. There are more properties that can be extracted from the content and it's possible to define your own rules - that will be covered in another tutorial.

The bare minimum decorator isn't very useful. Let's add some style and a bit of common layout.

Create the file /decorator.html in your web-app, containing:

```html
<html>
  <head>
    <title>SiteMesh example: <sitemesh:write property='title'/></title>
    <style type='text/css'>
      /* Some CSS */
     body { font-family: arial, sans-serif; background-color: #ffffcc; }
     h1, h2, h3, h4 { text-align: center; background-color: #ccffcc;
                      border-top: 1px solid #66ff66; }
     .mainBody { padding: 10px; border: 1px solid #555555; }
     .disclaimer { text-align: center; border-top: 1px solid #cccccc;
                   margin-top: 40px; color: #666666; font-size: smaller; }
    </style>
    <sitemesh:write property='head'/>
  </head>
  <body>

    <h1 class='title'>SiteMesh example site: <sitemesh:write property='title'/></h1>

    <div class='mainBody'>
      <sitemesh:write property='body'/>
    </div>

    <div class='disclaimer'>Site disclaimer. This is an example.</div>

  </body>
</html>
```
In this example, the decorator is a static .html file, but if you want the decorator to be more dynamic, technologies such as JSP, FreeMarker, etc can be used. SiteMesh doesn't care - it just needs a path that can be served content by the Servlet engine.

### Configuration

SiteMesh needs to be configured to know about this decorator and what it should do with it.

The configuration file should be created at /WEB-INF/sitemesh3.xml:

```xml
<sitemesh>
  <mapping path="/*" decorator="/decorator.html"/>
</sitemesh>
```
This tells SiteMesh that requests matching the path /* (i.e. all requests) should be decorated with /decorator.html that we just created.

If you don't like the idea of having to use XML to configure SiteMesh, don't worry - there are alternative mechanisms including directly in WEB-INF/web.xml, programatically through a Java API, through Spring, by naming convention, or any custom way you may choose to plug in. These are explained further in another article.

### Creating some content

Now to create some content. This is defined in plain HTML content. Create /hello.html:

```html
<html>
  <head>
    <title>Hello World</title>
    <meta name='description' content='A simple page'>
  </head>
  <body>
    <p>Hello <strong>world</strong>!</p>
  </body>
</html>
```
Like the decorator, the content may be static files or dynamically generated by the Servlet engine (e.g. JSP).

### The result

Pointing your browser to http://myserver/hello.html will serve the content you just created, with the decorator applied. The resulting merged HTML will look like this:

```html
<html>
  <head>
    <title>SiteMesh example: Hello World</title>
    <style type='text/css'>
      /* Some CSS */
      body { font-family: arial, sans-serif; background-color: #ffffcc; }
      h1, h2, h3, h4 { text-align: center; background-color: #ccffcc;
                       border-top: 1px solid #66ff66; }
      .mainBody { padding: 10px; border: 1px solid #555555; }
      .disclaimer { text-align: center; border-top: 1px solid #cccccc;
                    margin-top: 40px; color: #666666; font-size: smaller; }
    </style>
    <meta name='description' content='A simple page'>
  </head>
  <body>

    <h1 class='title'>SiteMesh example site: Hello World</h1>

    <div class='mainBody'>
      <p>Hello <strong>world</strong>!</p>
    </div>

    <div class='disclaimer'>Site disclaimer. This is an example.</div>

  </body>
</html>
```
As you can see, the <title>, <head> and <body> have been extracted from the content and inserted into the decorator template.

### Summary

A quick recap:

* SiteMesh is installed by dropping the library jar in /WEB-INF/lib and creating a filter (with mapping) in /WEB-INF/web.xml
* It can be configured by creating a /WEB-INF/sitemesh3.xml file, or through other configuration methods
* The filter intercepts requests to Content, runs it through the Content Processor and merges with a Decorator
* The Content is defined by an HTML page, that contains the vanilla HTML content of the site
* The Decorator is also defined by an HTML page, that contains the look and feel of the site, and placeholder <sitemesh:write> tags to indicate where the Content should be merged in
* The Content Processor contains the rules for extracting and transforming the content - it has some simple default rules and can be customized

## Configuring SiteMesh 3

SiteMesh supports two main approaches to configurations - XML or Java. It's up to you which you use. In fact, you can even use them both.

XML	
* Simplest to get started with
* Automatically reloads when config file changes
* Does not require Java programming

Java
* Allows for greater customization of SiteMesh
* Avoids yet another configuration file
* Can be used from higher level languages such as JRuby, Groovy, Scala...

### XML based configuration

The configuration file should live in /WEB-INF/sitemesh3.xml in your web-application.

Example
```xml
<sitemesh>
  <mapping path="/*" decorator="/decorator.html"/>
  <mapping path="/admin/*" decorator="/admin-decorator.html"/>
</sitemesh>
```

### Java based configuration

To use the Java based configuration, subclass org.sitemesh.config.ConfigurableSiteMeshFilter and overload the applyCustomConfiguration(SiteMeshFilterBuilder builder) method. You shall be passed an object that you can use to configure SiteMesh. You then deploy this filter in to your web-application.

#### Example

```java
public class MySiteMeshFilter extends ConfigurableSiteMeshFilter {
    @Override
    protected void applyCustomConfiguration(SiteMeshFilterBuilder builder) {
    builder.addDecoratorPath("/*", "/decorator.html")
    .addDecoratorPath("/admin/*", "/admin/decorator.html");
    }
}
```

Note: The SiteMeshFilterBuilder class supports a chainable API where each method returns an instance of itself. This is a convenience, but you don't have to use this style.

Note: If you also have an XML config file, SiteMesh will load this before calling applyCustomConfiguration(). This allows you to use XML for some configuration and Java for more advanced aspects.

### Configuring Decorator Mappings

This is the most common configuration applied to SiteMesh - mapping which decorators are applied based on the paths.

Things you can do:

* Map a default decorator to all paths
* Map a decorator to a specific path
* Map multiple decorators to a path - each decorator is applied to the result of the previous
Exclude a path from being decorated

#### XML
```xml
<sitemesh>
  <!-- Map default decorator. This shall be applied to all paths if no other paths match. -->
  <mapping decorator="/default-decorator.html"/>

  <!-- Map decorators to path patterns. -->
  <mapping path="/admin/*" decorator="/another-decorator.html"/>
  <mapping path="/*.special.jsp" decorator="/special-decorator.html"/>

  <!-- Alternative convention. This is more verbose but allows multiple decorators
       to be applied to a single path. -->
  <mapping>
    <path>/articles/*</path>
    <decorator>/decorators/article.html</decorator>
    <decorator>/decorators/two-page-layout.html</decorator>
    <decorator>/decorators/common.html</decorator>
  </mapping>

  <!-- Exclude path from decoration. -->
  <mapping path="/javadoc/*" exclude="true"/>
  <mapping path="/brochures/*" exclude="true"/>

</sitemesh>
```

#### Java
```java
public class MySiteMeshFilter extends ConfigurableSiteMeshFilter {

  @Override
  protected void applyCustomConfiguration(SiteMeshFilterBuilder builder) {
           // Map default decorator. This shall be applied to all paths if no other paths match.
    builder.addDecoratorPath("/*", "/default-decorator.html") 
           // Map decorators to path patterns. 
           .addDecoratorPath("/admin/*", "/another-decorator.html")
           .addDecoratorPath("/*.special.jsp", "/special-decorator.html")
           // Map multiple decorators to the a single path.
           .addDecoratorPaths("/articles/*", "/decorators/article.html",
                                             "/decoratos/two-page-layout.html", 
                                             "/decorators/common.html")
           // Exclude path from decoration.
           .addExcludedPath("/javadoc/*")
           .addExcludedPath("/brochures/*");
  }
}
```

### Advanced Configuration

For most users, the decorator mappings above should be enough. But if you want more options...

#### MIME Types

By default, SiteMesh will only intercept responses that set the Content-Type HTTP header to text/html.

This can be altered to allow SiteMesh to intercept responses for other types. This is only applicable for the SiteMesh Filter - it is ignored by the offline site builder.

#### XML
```xml
<sitemesh>
  <mime-type>text/html</mime-type>
  <mime-type>application/vnd.wap.xhtml+xml</mime-type>
  <mime-type>application/xhtml+xml</mime-type>
  ...
</sitemesh>
```

#### Java
```java
public class MySiteMeshFilter extends ConfigurableSiteMeshFilter {

  @Override
  protected void applyCustomConfiguration(SiteMeshFilterBuilder builder) {
    builder.setMimeTypes("text/html", "application/xhtml+xml", "application/vnd.wap.xhtml+xml");
  }

}
```
### Deploying Tag Rule Bundles
An advanced feature of SiteMesh is the ability to define custom rules that manipulate tags on a page. These are classes that implement org.sitemesh.content.tagrules.TagRuleBundle.

#### XML
```xml
<sitemesh>
  <content-processor>
    <tag-rule-bundle class="com.something.CssCompressingBundle" />
    <tag-rule-bundle class="com.something.LinkRewritingBundle"/>
  </content-processor>
  ...
</sitemesh>
```
#### Java
```java
public class MySiteMeshFilter extends ConfigurableSiteMeshFilter {


  @Override
  protected void applyCustomConfiguration(SiteMeshFilterBuilder builder) {
    builder.addTagRuleBundles(new CssCompressingBundle(), new LinkRewritingBundle());
  }


}
```

## Building Offline Websites with SiteMesh 3
A new feature in SiteMesh 3 is being able to apply decorators to content as an offline task, typically as part of a build step.

If both your content and your decorators are static, this offers a few benefits:

Allows final content to be distributed as pre-generated folder. Useful for including documentation with products without having to include a full Servlet compliant web-server.
Cuts down server loads - most web-server architectures are optimized for serving static files.
Provides more flexibility on where you can host content.
It's also possible to reuse decorators and configuration between a web application that generates decorated content on the fly, and offline generated files.

### Usage
There are a few different approaches to invoking the SiteMesh offline generator:

1. Command line interface
2. Apache Ant task
3. Java API (this can be embedded in applications, or used from higher level languages such as JRuby, Groovy or Scala)

Each of these can have the mappings of the decorators passed directly to them, or load from the SiteMesh configuration file.

Use the approach that suits your project.

### Command line interface

You can invoke the command line interface by running the executable sitemesh.jar. It requires Java 5 but no other dependencies.

Invoking on it's own will output a detailed help message:

```
java -jar sitemesh-3.x.jar
```

**Arguments**

The following arguments need to be passed to the command line:

-src	Required	Path to source directory, containing content and decorators
-dest	Required	Path to destination directory, where decorated content will be written
-dest	Required	Path to destination directory, where decorated content will be written
-config	One of these	Path to configuration file
-decoratorMapping	TODO
FILE1 FILE2 FILE3...	Required	List of content files to apply decorators to. These must be relative to the src directory

**Example**
```
java -jar sitemesh-3.x.jar -src project/src -config project/sitemesh.xml -dest project/build index.html page1.html page2.html
```

### Ant Task

#### Overview

The sitemesh.jar comes prepackaged with a custom Ant task that can be used for offline processing. In the following examples, we're going to look at how to install and use the SiteMeshTask to generate static content in the offline mode.

#### SiteMeshTask

The SiteMeshTask defines the following attributes:

1. destDir - The output folder in which all of the decorated files will be placed.
2. config - The location of the SiteMesh configuration file.
3. srcdir - The source directory which contains all of the files to be decorated.
4. includes - An Ant style filter of what files to include. 
5. excludes - An Ant style filter of what files to exclude.

The SiteMeshTask can also accept the following child nodes:

1. fileset - The standard Ant FileSet that will use the srcdir of the sitemesh node.
2. sitemeshfileset - A custom SiteMesh FileSet that supports an additional attribute called "decorator" which can be used to associate a decorator with the given FileSet.

#### Getting started!

The below provides a high level outline of what steps we're going to cover in this section.

1. Create a SiteMesh configuration file.
2. Register the SiteMeshTask with Ant
3. Define the <sitemesh/> node.
4. Execute Ant

##### 1. Creating the SiteMesh configuration file

The SiteMeshTask can be given a configuration file to tell SiteMesh how to decorate files. The power of this feature is that the configuration is then externalized from the **build.xml** file. In our first two examples, we're going to show how to use this form of the SiteMeshTask. Below is a very simple SiteMesh configuration file that applies the main.html decorator to all pages.

```xml
<sitemesh>
  <mapping path="/*" decorator="/decorators/main.html"/>
</sitemesh>
```

##### 2. Registering SiteMeshTask with Ant
In order to use SiteMesh from within Ant, the first thing you will need to do is register the SiteMeshTask with Ant using the following **<taskdef/>** declaration.

```xml
<project name="my-ant-project">

  <taskdef name="sitemesh" 
           classname="org.sitemesh.ant.SiteMeshTask"
           classpath="path/to/sitemesh-3.x.jar"/>

  ...

</project>
```

For more information on registering custom tasks within Ant, please see Writing Ant Tasks.

##### 3. Define the <sitemesh/> node.

Now that we've created the SiteMesh configuration file and registered the SiteMeshTask with Ant, it's time to start using SiteMesh within our tasks.

**Using the sitemesh task without a fileset.**

Let's look at how to provide **<sitemesh/>** with a configuration file and tell it what directories to include or exclude.

In this example, we're going to process all of the files stored in "project/src" and place the decorated files into "project/build".
```xml
<project name="my-ant-project">

  <target name="my-target">
    <sitemesh srcdir="project/src"
              config="project/sitemesh.xml"
              destdir="project/build"
              includes="*/.html"
              excludes="decorators/*"/>
  </target>

</project>
```

The benefit to the above is that all configuration is externalized from the **build.xml** file.

**Using the sitemesh task with the sitemeshfileset**

In the below example, multiple source folders are used by provided a sitemeshfileset. This provides greater control over what folders should be included or excluded, but still leverage a common destination folder and configuration file.

```xml
<project name="my-ant-project">


  <target name="my-target">

    <sitemesh destdir="site/documentation"
              config="config/sitemesh.xml">

      <sitemeshfileset dir="documentation">
        <include name="*/.html"/>
        <exclude name="private/*"/>
      </sitemeshfileset>

      <sitemeshfileset dir="presentation">
        <include name="*/.html"/>
      </sitemeshfileset>

    </sitemesh>
  </target>

</project>
```

**Using the sitemesh task with the sitemeshfileset with an associated decorator**

In our final example, a decorator will be used on each sitemeshfileset.

```xml
<project name="my-ant-project">

  <target name="my-target">
    <sitemesh destdir="site/documentation">
      <sitemeshfileset dir="documentation"
                       decorator="decorators/private.html">
        <include name="private/*.html"/>
      </sitemeshfileset>
    </sitemesh>
  </target>

</project>
```
