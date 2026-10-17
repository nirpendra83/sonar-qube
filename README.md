# sonar-qube


read -rsp "SonarQube token: " SONAR_TOKEN
echo
export SONAR_TOKEN

cd ~
git clone https://github.com/SonarSource/sonar-scanning-examples.git
cd sonar-scanning-examples/sonar-scanner-maven/maven-basic


mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
  -Dsonar.projectKey=Test01 \
  -Dsonar.projectName='Test01' \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.token=sqp_49227dbccb3fa2505f6dc64f0d0f08f6fd7718e5

https://github.com/monbostest/java-sonar-runner-simple



 docker run -d   --name sonarqube   -p 9000:9000   -e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true   -v sonarqube_data:/opt/sonarqube/data   -v sonarqube_extensions:/opt/sonarqube/extensions   -v sonarqube_logs:/opt/sonarqube/logs   sonarqube:community




read -rsp "SonarQube token: " SONAR_TOKEN
echo
export SONAR_TOKEN
