pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('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') // Use the ID of your GitHub credentials
        GITHUB_API_URL = 'https://api.github.com'
        REPO_OWNER = 'nikhilP-addweb'
        REPO_NAME = 'nikhilP-addweb/status-new'
    }

    stages {
        stage('Checkout') {
            steps {
                // Check out your code from Git repository
                checkout scm
            }
        }

        stage('Composer Validate') {
            steps {
                script {
                    // Install Composer
                    sh 'cd /var/lib/jenkins/jobs/status-new/workspace'
                    sh 'composer install'
                    
                    // Run composer validate command
                    def validateResult = sh(returnStatus: true, script: 'composer validate')

                    // Update GitHub status check based on validation result
                    def status = validateResult == 0 ? 'success' : 'failure'
                    def description = validateResult == 0 ? 'Composer validation passed' : 'Composer validation failed'

                    sh """
                        curl -X POST \\
                        -H "Authorization: token ${GITHUB_TOKEN}" \\
                        -d '{
                            "state": "${status}",
                            "context": "Composer Validation",
                            "description": "${description}",
                            "target_url": "${env.BUILD_URL}"
                        }' \\
                        ${GITHUB_API_URL}/repos/${REPO_OWNER}/${REPO_NAME}/statuses/${env.GIT_COMMIT}
                    """
                }
            }
        }
    }

}

