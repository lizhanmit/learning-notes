# Maven Note

## Maven Commands 

`mvn clean`: Cleans up any previously generated artifacts from a prior build.

`mvn compile`: Compiles the source code of the project (by default in a generated `target` folder).

`mvn test`: Tests the compiled source code.

`mvn package`: Packages the compiled code in a suitable format such as JAR.

`mvn release`: After executing this command, the version in pom.xml will be incremented. 

To skip maven test, add `-Dmaven.test.skip=true`, e.g. `mvn clean deploy -Dmaven.test.skip=true`.