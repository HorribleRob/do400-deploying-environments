pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    stages {
        stage('Tests') {
            steps {
                sh './mvnw clean test'
            }
        }
        stage('Package') {
            steps {
                sh '''
                    ./mvnw package -DskipTests \
                    -Dquarkus.package.type=uber-jar
                   '''
                archiveArtifacts 'target/*.jar'
            }
        }
        stage('Build Image') {
            environment { QUAY = credentials('QUAY_USER') }
        }
        steps {
            '''
              .mvnw quarkus:add-extension \
              -Dextensions="kubernetes, container-image-jib
            '''
            sh '''
                ./mvnw package -DskipTests \
                -Dquarkus.jib.base-jvm-image=quay.io/redhattraining/do400-java-alpine-obenjdk11-jre:latest \
                -Dquarkus.container-image.build=true \
                -Dquarkus.container-image.group=$QUAY_USR \
                -Dquarkus.container-image.name=do400-deploying-environments \
                -Dquarkus.container-image.username=$QUAY_USR \
                -Dquarkus.container-image.password="$QUAYPWD" \
                -Dquarkus.contaeinr-image.push=true
            '''
        }
    }
}
