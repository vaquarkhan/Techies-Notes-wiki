**Maven Compile Scope**

This is maven default scope. Dependencies with compile scope are needed to build, test, and run the project.

Scope compile is to be required in most of the cases to resolve the import statements into your java classes sourcecode.

`<dependencies>`
    `<dependency>`
        `<groupId>log4j</groupId>`
        `<artifactId>log4j</artifactId>`
        `<version>1.2.14</version>`
        `<!-- You can ommit this because it is default -->`
        `<scope>compile</scope>`
    `</dependency>`
`</dependencies>`

-------------------------------------------------------------------

**Maven Provided Scope**

Maven scope provided is used during build and test the project. They are also required to run, but should not exported, because the dependency will be provided by the runtime, for instance, by servlet container or application server.

`<dependency>`
    `<groupId>javax.servlet</groupId>`
    `<artifactId>servlet-api</artifactId>`
    `<version>3.0.1</version>`
    `<scope>provided</scope>`
`</dependency>`

-------------------------------------------------------------------

**Maven Runtime Scope**

Dependencies with maven scope runtime are not needed to build, but are part of the classpath to test and run the project.

`<dependency>`
    `<groupId>com.thoughtworks.xstream</groupId>`
    `<artifactId>xstream</artifactId>`
    `<version>1.4.4</version>`
    `<scope>runtime</scope>`
`</dependency>`


-------------------------------------------------------------------

**Maven Test Scope**

Dependencies with maven scope test are not needed to build and run the project. They are needed to compile and run the unit tests.

`<dependency>`
    `<groupId>junit</groupId>`
    `<artifactId>junit</artifactId>`
    `<version>4.12</version>`
    `<scope>test</scope>`
`</dependency>`

-------------------------------------------------------------------

**Maven System Scope**

Dependencies with system are similar to ones with scope provided. The only difference is system dependencies are not retrieved from remote repository. They are present under project’s subdirectory and are referred from there. See external dependency for more detail.

`<dependency>`
  `<groupId>extDependency</groupId>`
  `<artifactId>extDependency</artifactId>`
  `<scope>system</scope>`
  `<version>1.0</version>`
  `<systemPath>${basedir}\war\WEB-INF\lib\extDependency.jar</systemPath>`
`</dependency>`

**Maven Import Scope**

import scope is only supported on a dependency of type pom in the dependencyManagement section. It indicates the dependency to be replaced with the effective list of dependencies in the specified POM’s dependencyManagement section.

`<dependencyManagement>`
    `<dependencies>`
        `<dependency>`
            `<groupId>other.pom.group.id</groupId>`
            `<artifactId>other-pom-artifact-id</artifactId>`
            `<version>SNAPSHOT</version>`
            `<scope>import</scope>`
            `<type>pom</type>`
        `</dependency>   `
    `</dependencies>`
`</dependencyManagement>`

**Transitivity Resolution**

When you include a maven dependency and it has it’s own other dependencies (i.e. transitive dependencies) then you may want to be clear about the scope of these transitive dependencies as well.

Let’s understand about transitive dependency scope with a simple table. In this table, if a dependency is set to the scope in the left column, transitive dependencies at top row will result in a dependency with the scope listed at their intersection.

------------------------------------------------------------------------------------

**compile	   -        provided	runtime	      test**

------------------------------------------------------------------------------------

compile	  compile	–	runtime   	–

------------------------------------------------------------------------------------

provided  provided	–	provided	–

------------------------------------------------------------------------------------

runtime	  runtime	–	runtime	        –

------------------------------------------------------------------------------------

test	  test	        –	test	        –

------------------------------------------------------------------------------------