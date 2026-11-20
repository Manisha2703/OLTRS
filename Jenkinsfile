
node {
    stage('Checkout') {
        git branch: 'master',
            url: 'https://github.com/Manisha2703/OLTRS.git'
    }

    stage('Maven Build and Test') {
        bat 'mvn -B clean verify'

        junit allowEmptyResults: true,
      testResults: 'target/surefire-reports/*.xml'

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
            credentialsId: 'dockerhub-token',
            usernameVariable: 'DOCKERHUB_USER',
            passwordVariable: 'DOCKERHUB_TOKEN'
        )
    ]) {
        bat '@echo off\necho %DOCKERHUB_TOKEN%| docker login --username %DOCKERHUB_USER% --password-stdin'

        bat 'docker push %DOCKERHUB_USER%/oltrs:%BUILD_NUMBER%'

        bat 'docker push %DOCKERHUB_USER%/oltrs:latest'

        bat 'docker logout'
    }
}



    echo 'OLTRS CI/CD Pipeline completed successfully'
}
