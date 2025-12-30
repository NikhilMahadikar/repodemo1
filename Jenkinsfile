pipeline {
    agent any

    tools {
        nodejs 'NodeJS_18'   // Jenkins → Global Tool Configuration
    }

    environment {
        CI = 'true'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Install Playwright Browsers') {
            steps {
                sh 'npx playwright install --with-deps'
            }
        }

        stage('Run Smoke Tests (PR only)') {
            when {
                expression { env.CHANGE_ID != null }
            }
            steps {
                sh 'npx playwright test tests/smoke --grep @smoke'
            }
        }

        stage('Run Regression Tests (main branch)') {
            when {
                branch 'main'
            }
            steps {
                sh 'npx playwright test tests/regression --grep @regression'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'playwright-report/**', fingerprint: true
        }
        failure {
            echo '❌ Playwright tests failed'
        }
        success {
            echo '✅ Playwright tests passed'
        }
    }
}
