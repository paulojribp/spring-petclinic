#!groovy

stage ('Checkout') {
    node ("Dockerhost") {
        git url: 'https://github.com/paulojribp/spring-petclinic.git'
    }
}
stage ('Build') {
    node ("Dockerhost") {
        def mvnHome = tool 'M3'
        sh "${mvnHome}/bin/mvn -Dmaven.test.failure.ignore clean package pmd:pmd checkstyle:checkstyle cobertura:cobertura"
    }
}
stage ('Tests') {
    node ("Dockerhost") {
        junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml', keepLongStdio: true
        step([$class: 'CoberturaPublisher',
            coberturaReportFile: '**/target/site/cobertura/coverage.xml',
            failUnhealthy: false, maxNumberOfBuild: 0,
            onlyStable: false, failUnstable: false])
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

    def mensagem
    timeout(time:30, unit:'MINUTES') {
        mensagem = input message: 'Porque Deployar?',
            parameters: [$class: 'TextParameterDefinition', description: 'Porque Deployar?']
    }

    node ("Dockerhost") {
        print "Motivo do deploy: ${mensagem}"

        //pipeline utility steps
        def pom = readMavenPom file: 'pom.xml'
        try {
            sh "docker kill ${JOB_NAME}"
        } catch(Exception e) {
            print ">>>>>> Container não estava rodando! <<<<<<<<"
        }

        sh "docker run --rm -d -p 8088:8088 --name ${JOB_NAME} -v \$(pwd)/target:/app -e JAVA_APP_JAR=/app/${pom.artifactId}-${pom.version}.jar --link mysql:mysql_server -e JAVA_OPTIONS=\"-Dspring.datasource.host=mysql_server -Dspring.profiles.active=mysql\" fabric8/java-alpine-openjdk8-jdk"
    }
}








