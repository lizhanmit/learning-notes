# SonarQube Note

## Architecture 

![sonarqube-architecture.png](img/sonarqube-architecture.png)

![sonarqube-integration-architecture.png](img/sonarqube-integration-architecture.png)

1. Developers code in their IDEs and use **SonarLint** to run local analysis.
2. Developers push their code into their favourite SCM : git, SVN, TFVC, ...
3. The Continuous Integration Server triggers an automatic build, and the execution of the **SonarScanner** required to run the SonarQube analysis.
4. The analysis report is sent to the **SonarQube Server** for processing.
5. SonarQube Server processes and stores the analysis report results in the **SonarQube Database**, and displays the results in the UI.
6. Developers review, comment, challenge their Issues to manage and reduce their Technical Debt through the **SonarQube UI**.
7. Managers receive Reports from the analysis. Ops use APIs to automate configuration and extract data from SonarQube. Ops use JMX to monitor SonarQube Server.

The SonarQube Platform can have **only one** SonarQube Server and **one** SonarQube Database.

For **optimal performance**, each component (server, database, scanners) should be installed on a separate machine, and the server machine should be dedicated.

**SonarScanners scale** by adding machines.

All machines must be **time synchronized**.

The SonarQube Server and the SonarQube Database must be located in the **same network**.

---

## Basics 

### Principles

Focus on the **new code**.

### Snapshot

A set of measures and issues on a given project at a given time. A snapshot is generated for each analysis.

### Issue

When a piece of code does not comply with a rule, an issue is logged on the snapshot. 

Three types of issue: 

- bugs
- code smells: maintainability-related issues
- vulnerabilities