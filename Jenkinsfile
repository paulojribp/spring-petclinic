node {
    stage ('Checkout') {
        git url: 'https://github.com/paulojribp/spring-petclinic.git'
    }

    stage ('Build') {
        sh 'mvn clean install'
    }
}