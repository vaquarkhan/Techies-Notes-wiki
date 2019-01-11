https://github.com/saagie/example-java-read-and-write-from-hive

1) POM 

`<?xml version="1.0" encoding="UTF-8"?>`
`<project xmlns="http://maven.apache.org/POM/4.0.0"`
         `xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"`
         `xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">`
    `<modelVersion>4.0.0</modelVersion>`

    `<groupId>io.saagie</groupId>`
    `<artifactId>example-java-read-and-write-from-hive</artifactId>`
    `<version>1.0-SNAPSHOT</version>`

    `<properties>`
        `<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>`
        `<hadoop.version>2.6.0</hadoop.version>`
        `<log4j.version>1.2.17</log4j.version>`
        `<hive-jdbc.version>1.1.0</hive-jdbc.version>`
    `</properties>`

    `<dependencies>`
        `<!-- Hadoop main client artifact -->`
        `<dependency>`
            `<groupId>org.apache.hadoop</groupId>`
            `<artifactId>hadoop-client</artifactId>`
            `<version>${hadoop.version}</version>`
        `</dependency>`
        `<dependency>`
            `<groupId>org.apache.hive</groupId>`
            `<artifactId>hive-jdbc</artifactId>`
            `<version>${hive-jdbc.version}</version>`
        `</dependency>`
        `<dependency>`
            `<groupId>log4j</groupId>`
            `<artifactId>log4j</artifactId>`
            `<version>${log4j.version}</version>`
        `</dependency>`
    `</dependencies>`

    `<build>`
        `<plugins>`
            `<plugin>`
              `<groupId>io.saagie</groupId>`
               `<artifactId>saagie-maven-plugin</artifactId>`
               `<version>1.0.2</version>`
               `<configuration>`
                 `<platformId>1</platformId>`
                 `<jobName>example-java-read-and-write-from-hive</jobName>`
                 `<jobCategory>extract</jobCategory>`
               `</configuration>`
            `</plugin>`
            `<plugin>`
                `<groupId>org.apache.maven.plugins</groupId>`
                `<artifactId>maven-compiler-plugin</artifactId>`
                `<configuration>`
                    `<source>1.8</source>`
                    `<target>1.8</target>`
                `</configuration>`
            `</plugin>`
            `<plugin>`
                `<artifactId>maven-assembly-plugin</artifactId>`
                `<configuration>`
                    `<archive>`
                        `<manifest>`
                            `<mainClass>io.saagie.example.hive.Main</mainClass>`
                        `</manifest>`
                    `</archive>`
                    `<descriptorRefs>`
                        `<descriptorRef>jar-with-dependencies</descriptorRef>`
                    `</descriptorRefs>`
                `</configuration>`
                `<executions>`
                    `<execution>`
                        `<id>make-assembly</id> <!-- this is used for inheritance merges -->`
                        `<phase>package</phase> <!-- bind to the packaging phase -->`
                        `<goals>`
                            `<goal>single</goal>`
                        `</goals>`
                    `</execution>`
                `</executions>`
            `</plugin>`
        `</plugins>`
    `</build>`

`</project>`

Note : Each hive version has diffrent jars so make sure add proper jar as per Hive version 