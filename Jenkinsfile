pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Validate Composer') {
            steps {
                script {
                    def composerValidationResult = sh(script: 'composer validate --no-check-all --quiet', returnStatus: true)
                    if (composerValidationResult == 0) {
                        echo 'Composer validation passed.'
                        currentBuild.result = 'SUCCESS'
                        updateGitHubStatus('success', 'Composer validation passed')
                    } else {
                        echo 'Composer validation failed.'
                        currentBuild.result = 'FAILURE'
                        updateGitHubStatus('failure', 'Composer validation failed')
                        error("Composer validation failed")
                    }
                }
            }
        }
        
    }

}

def updateGitHubStatus(status, message) {
    script {
        def accessToken = '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'
        def baseUrl = 'https://api.github.com'
        def context = 'Jenkins/Composer-Validate'
        
        def commit = sh(script: "git rev-parse HEAD", returnStdout: true).trim()
        
        def payload = """
        {
            "state": "$status",
            "target_url": "${env.BUILD_URL}",
            "description": "$message",
            "context": "$context"
        }
        """
        
        def apiUrl = "${baseUrl}/repos/${env.GITHUB_REPO_OWNER}/${env.GITHUB_REPO_NAME}/statuses/${commit}"
        
        def response = httpRequest(
            url: apiUrl,
            httpMode: 'POST',
            authentication: accessToken,
            contentType: 'APPLICATION_JSON',
            requestBody: payload
        )
        
        echo "GitHub Status Update Response: ${response}"
    }
}

