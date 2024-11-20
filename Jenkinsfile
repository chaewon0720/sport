pipeline {
    agent any
    environment {
        HARBOR_REPO = 'your-harbor-repo/your-image' // Harbor 이미지 경로
        IMAGE_TAG = 'latest'                      // 사용할 이미지 태그
    }
    stages {
        stage('Clone Repository') {
            steps {
                // Git에서 소스코드 클론
                git 'https://github.com/chaewon0720/sport.git'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                // SonarQube를 이용한 코드 분석
                withSonarQubeEnv('SonarQube') {
                    sh './gradlew sonarqube'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Docker 이미지 빌드
                    sh "docker build -t ${HARBOR_REPO}:${IMAGE_TAG} ."
                    // Harbor에 Docker 이미지 푸시
                    sh "docker login -u <your-harbor-username> -p <your-harbor-password> your-harbor-url"
                    sh "docker push ${HARBOR_REPO}:${IMAGE_TAG}"
                }
            }
        }
        stage('Deploy with Ansible') {
            steps {
                // Ansible Playbook 실행을 통한 배포
                ansiblePlaybook(
                    credentialsId: 'your-ansible-ssh-key',
                    playbook: 'deploy.yml',
                    extras: "-e image=${HARBOR_REPO}:${IMAGE_TAG}"
                )
            }
        }
    }
    post {
        always {
            // 파이프라인 실행 결과 확인 및 클린업
            echo 'Pipeline execution completed!'
        }
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed. Please check the logs.'
        }
    }
}
