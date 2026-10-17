# SonarQube Demo on Linux with Docker and Maven

This guide starts SonarQube in Docker and runs a Maven-based code analysis from a Linux terminal.

> **Security:** A SonarQube token was included in the original notes. Revoke that token in SonarQube and generate a new one. Do not store tokens in this Markdown file or commit them to Git.

## 1. Start SonarQube in Docker

```bash
docker run -d \
  --name sonarqube \
  -p 9000:9000 \
  -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true \
  -v sonarqube_data:/opt/sonarqube/data \
  -v sonarqube_extensions:/opt/sonarqube/extensions \
  -v sonarqube_logs:/opt/sonarqube/logs \
  sonarqube:community
```

Check the container and logs:

```bash
docker ps
docker logs -f sonarqube
```

Wait for SonarQube to finish starting, then open:

- On the Linux server: <http://localhost:9000>
- From another computer: `http://<LINUX_SERVER_IP>:9000`

On a fresh installation, sign in with `admin` / `admin` and change the initial password.

## 2. Generate a SonarQube token

In the SonarQube UI, generate a project analysis token. Keep it private.

Enter the token into your current shell without displaying it:

```bash
read -rsp "SonarQube token: " SONAR_TOKEN
echo
export SONAR_TOKEN
```

The token remains available to commands in this shell session. Do not paste the token directly into command history or source files.

## 3. Clone the official SonarSource Maven example

Install Git on the Linux host if needed, then run:

```bash
cd ~
git clone https://github.com/SonarSource/sonar-scanning-examples.git
cd sonar-scanning-examples/sonar-scanner-maven/maven-basic
```

Confirm that this directory contains a Maven project:

```bash
ls -l pom.xml
```

If `pom.xml` is not present, check the repository's current directory structure and use the Maven example directory that contains it.

## 4. Run Maven analysis

If Maven is installed on the Linux host, run:

```bash
mvn clean verify \
  org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
  -Dsonar.projectKey=Test01 \
  -Dsonar.projectName='Test01' \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token="$SONAR_TOKEN"
```

### Optional: run Maven inside Docker

If you do not want to install Maven on the Linux host, run this from the Maven project directory:

```bash
docker run --rm \
  --network host \
  -v "$PWD:/usr/src/mymaven" \
  -w /usr/src/mymaven \
  -e SONAR_TOKEN \
  maven:3.9-eclipse-temurin-17 \
  mvn clean verify \
  org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
  -Dsonar.projectKey=Test01 \
  -Dsonar.projectName='Test01' \
  -Dsonar.host.url=http://127.0.0.1:9000 \
  -Dsonar.token="$SONAR_TOKEN"
```

`--network host` assumes a Linux Docker host and that SonarQube is listening on port 9000 on that same host. If SonarQube is on another server, use its reachable URL instead.

## 5. Alternative sample repository

You also provided this repository:

<https://github.com/monbostest/java-sonar-runner-simple>

Clone it with:

```bash
cd ~
git clone https://github.com/monbostest/java-sonar-runner-simple.git
cd java-sonar-runner-simple
ls -la
```

Before running Maven, verify that the directory contains `pom.xml`. If it does not, Maven will fail with `MissingProjectException`.

## 6. Verify the results

After a successful analysis:

1. Open SonarQube at `http://localhost:9000` or the server URL.
2. Open project `Test01`.
3. Review the analysis overview, issues, and Quality Gate status.

If analysis fails, check the SonarQube container logs:

```bash
docker logs --tail=200 sonarqube
```

## Useful commands

```bash
# Check container status
docker ps

# Follow SonarQube logs
docker logs -f sonarqube

# Stop SonarQube
docker stop sonarqube

# Start it again
docker start sonarqube
```

The Docker volumes preserve SonarQube data when the container is stopped or removed. This single-container setup is for a demo/evaluation, not production.
