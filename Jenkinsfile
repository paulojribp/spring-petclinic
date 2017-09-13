#!groovy

stage ('Checkout') {
    node ("Dockerhost") {
        git url: 'https://github.com/paulojribp/spring-petclinic.git'
    }
}
stage ('Build') {
    node ("Dockerhost") {
        def mvnHome = tool 'M3'
        sh "${mvnHome}/bin/mvn -Dmaven.test.failure.ignore clean package pmd:pmd checkstyle:checkstyle"
    }
}
stage ('Tests') {
    node ("Dockerhost") {
        junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml', keepLongStdio: true
    }
}
stage ('Code Analysis') {
    node ("Dockerhost") {
        step([$class: 'hudson.plugins.checkstyle.CheckStylePublisher', 
            pattern: '**/target/checkstyle-result.xml', unstableTotalAll: '320']);
        step([$class: 'PmdPublisher',
            pattern: '**/target/pmd.xml', unstableTotalAll: '1']);
    }
}
stage ('Deploy') {
    node ("Dockerhost") {
        //pipeline utility steps
        def pom = readMavenPom file: 'pom.xml'
        sh "docker kill ${JOB_NAME}"
        sh "docker run --rm -d -p 8088:8088 --name ${JOB_NAME} -v \$(pwd)/target:/app -e JAVA_APP_JAR=/app/${pom.artifactId}-${pom.version}.jar fabric8/java-alpine-openjdk8-jdk"
    }
}








