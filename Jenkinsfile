
node {
    stage('Checkout') {
        git branch: 'master',
            url: 'https://github.com/Manisha2703/OLTRS.git'
    }

    stage('Maven Build and Test') {
        bat 'mvn -B clean verify'

        junit 'target/surefire-reports/*.xml'

        archiveArtifacts artifacts: 'target/*.war',
                         fingerprint: true
    }

    stage('Docker Build') {
        withCredentials([
            usernamePassword(
                credentialsId: 'dockerhub-creds',
                usernameVariable: 'DOCKERHUB_USER',
                passwordVariable: 'DOCKERHUB_TOKEN'
            )
        ]) {
            bat 'docker build -t %DOCKERHUB_USER%/oltrs:%BUILD_NUMBER% -t %DOCKERHUB_USER%/oltrs:latest .'
        }
    }

    stage('Docker Push') {
        withCredentials([
            usernamePassword(
                credentialsId: 'dockerhub-creds',
                usernameVariable: 'DOCKERHUB_USER',
                passwordVariable: 'DOCKERHUB_TOKEN'
            )
        ]) {
            bat '@echo off\n docker login --username %DOCKERHUB_USER% --password-stdin <nul'
            bat 'docker push %DOCKERHUB_USER%/oltrs:%BUILD_NUMBER%'
            bat 'docker push %DOCKERHUB_USER%/oltrs:latest'
        }
    }

    echo 'OLTRS CI/CD Pipeline completed successfully'
}
