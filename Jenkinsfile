node {
    stage ('Checkout') {
        git url: 'https://github.com/paulojribp/spring-petclinic.git'
        sh 'git checkout curso'
    }

    stage ('Build') {
        sh 'mvn clean install'
    }
}