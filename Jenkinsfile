pipeline {
agent any


tools {
    maven 'Maven3'
}

stages {

    stage('Checkout') {
        steps {
            checkout scm
        }
    }

    stage('Build & Deploy') {
        steps {
            withCredentials([usernamePassword(
                credentialsId: 'anypoint-creds',
                usernameVariable: 'ANYPOINT_USER',
                passwordVariable: 'ANYPOINT_PASS'
            )]) {

                // Create settings.xml
                writeFile file: 'settings.xml', text: """


<settings>
  <servers>
    <server>
      <id>exchange</id>
      <username>${ANYPOINT_USER}</username>
      <password>${ANYPOINT_PASS}</password>
    </server>
    <server>
      <id>anypoint-exchange-v3</id>
      <username>${ANYPOINT_USER}</username>
      <password>${ANYPOINT_PASS}</password>
    </server>
  </servers>
</settings>
"""


                // Build + Publish + Deploy
                bat """
                    mvn clean deploy mule:deploy -s settings.xml ^
                        -DskipTests ^
                        -Duser=%ANYPOINT_USER% ^
                        -Dpass=%ANYPOINT_PASS% ^
                        -DapplicationName=ffff
                """
            }
        }
    }
}

post {
    always {
        bat 'del settings.xml'
    }
    success {
        echo 'Deployment Successful'
    }
    failure {
        echo 'Deployment Failed'
    }
}


}