/*
 GitHub → Jenkins Webhook Trigger → Jenkins Pipeline 실행
     → Stage 1: Checkout (Git repo clone)
     → Stage 2: Prepare (Gradle Wrapper 준비)
     → Stage 3: Build (Gradle 빌드 → compile + test 자동 실행)
     → post {
           always → 테스트 결과 저장, Artifact 저장
           success → 성공 메시지 출력
           failure → 실패 메시지 출력
       }
*/

pipeline {
    agent any

    environment {
        CLASS_DIR = 'classes'
        REPORT_DIR = 'test-reports'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // Git clone
            }
        }

        stage('Prepare') {  // Gradle Wrapper 실행 권한 부여
            steps {
                sh '''
                    echo "[+] Preparing Gradle Wrapper..."
                    chmod +x ./gradlew
                '''
            }
        }

        stage('Build') {  // Gradle 빌드 실행 (compile + test)
            steps {
                sh './gradlew clean build'
            }
        }
    }

    post {
        always {
            echo "[*] Archiving test results..."
            // Gradle test 결과는 build/test-results/test 디렉토리에 저장됨
            junit 'build/test-results/test/*.xml'
            archiveArtifacts artifacts: 'build/test-results/test/*', allowEmptyArchive: true
        }

        // env.JOB_NAME, env.BUILD_NUMBER 는 젠킨스에서 제공하는 환경변수
       failure {
               echo "Build or test failed!"
               emailext (
                   subject: "Jenkins Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                   body: """Build failed.
                   Check console output at ${env.BUILD_URL}""",
                   to: "rmsghchl0@gmail.com"
               )
           }

           success {
               echo "Build and test succeeded!"
               emailext (
                   subject: "Jenkins Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                   body: """Build succeeded!
                   Check details at ${env.BUILD_URL}""",
                   to: "rmsghchl0@gmail.com"
               )
           }
    }
}

// finish to setting smtpConfiguration: email test2