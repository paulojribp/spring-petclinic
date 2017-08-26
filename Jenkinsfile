node {
    def mvnHome
    stage('Checkout') {
        git url: 'https://github.com/paulojribp/spring-petclinic.git'
    }
    stage ('Build') {
        mvnHome = tool 'M3'
        sh "${mvnHome}/bin/mvn clean package"
        archiveArtifacts 'target/*.jar'
    }
}