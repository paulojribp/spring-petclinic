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
        mensagem = input(message: 'Seguir para Produção?', parameters: [
                [$class: 'TextParameterDefinition', description: '', name: 'Mensagem']
        ])
    }

    node ("Dockerhost") {
        print "Motivo do deploy: ${mensagem}"


        //pipeline utility steps
        def pom = readMavenPom file: 'pom.xml'
        archive "target/${pom.artifactId}-${pom.version}.jar"
        
        sh "docker-compose down"
        sh "docker-compose up"
    }
}








