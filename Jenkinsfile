#!groovy​

stage ('Checkout')
node ("Dockerhost") {
    sh 'rm -rf *'
    git url: 'https://github.com/paulojribp/spring-petclinic.git'
    sh 'git checkout curso && git pull origin curso'
    mvnHome = tool 'M3'
}

stage ('Build')
node ("Dockerhost") {
    sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package pmd:pmd checkstyle:checkstyle"
}
    
stage ('Tests Results')
node ("Dockerhost") {
    junit allowEmptyResults: false, healthScaleFactor: 15.0, keepLongStdio: true, testResults: 'target/surefire-reports/*.xml'
}

stage ('PMD/Checkstyle')
node ("Dockerhost") {
    step([$class: 'hudson.plugins.checkstyle.CheckStylePublisher', pattern: '**/target/checkstyle-result.xml', unstableTotalAll:'320'])
    step([$class: 'PmdPublisher', pattern: '**/target/pmd.xml', unstableTotalAll:'0'])
}
    
stage ('Deploy')
node ("Dockerhost") {
    def pom = readMavenPom file: 'pom.xml'
    archive "target/${pom.artifactId}-${pom.version}.jar"
    
    sh "docker run --rm -it -d --name ${JOB_NAME} -p 8080:8080 -p 8778:8778 -v \$(pwd)/target:/app -e JAVA_APP_JAR=/app/${pom.artifactId}-${pom.version}.jar fabric8/java-alpine-openjdk8-jdk"
}

//docker run --rm -it -d --name petclinic --link mysql:mysql -p 8080:8080 -p 8778:8778 -v $(pwd)/target:/app -e JAVA_APP_JAR=/app/spring-petclinic-1.5.1.jar -e MYSQL_SERVER=mysql fabric8/java-alpine-openjdk8-jdk