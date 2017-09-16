#!groovy​

stage ('Checkout') {
    node ("Dockerhost") {
        sh 'rm -rf *'
        git url: 'https://github.com/paulojribp/spring-petclinic.git'
        sh 'git checkout curso && git pull origin curso'
        mvnHome = tool 'M3'
    }
}

stage ('Build') {
    node ("Dockerhost") {
        sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package pmd:pmd checkstyle:checkstyle cobertura:cobertura"
    }
}

stage ('Tests Results') {
    node ("Dockerhost") {
        junit allowEmptyResults: false, healthScaleFactor: 15.0, keepLongStdio: true, testResults: 'target/surefire-reports/*.xml'
        step([$class: 'CoberturaPublisher', autoUpdateHealth: false,
              autoUpdateStability: false,
              coberturaReportFile: '**/target/site/cobertura/coverage.xml',
              failUnhealthy: false, failUnstable: false,
              maxNumberOfBuilds: 0, onlyStable: false, zoomCoverageChart: false])
    }
}

stage ('PMD/Checkstyle') {
    node ("Dockerhost") {
        step([$class: 'hudson.plugins.checkstyle.CheckStylePublisher', pattern: '**/target/checkstyle-result.xml', unstableTotalAll:'320'])
        step([$class: 'PmdPublisher', pattern: '**/target/pmd.xml', unstableTotalAll:'0'])
    }
}

stage ('Deploy') {

    node ("Dockerhost") {
        def pom = readMavenPom file: 'pom.xml'
        archive "target/${pom.artifactId}-${pom.version}.jar"

        sh "docker-compose down"

        sh "docker-compose up"
    }
}
