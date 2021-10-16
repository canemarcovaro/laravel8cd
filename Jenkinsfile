pipeline {
    agent any
    stages {
        stage("Build") {
            environment {
                DB_HOST = '127.0.0.1'
                DB_DATABASE = 'alfred'
                DB_USERNAME = 'root'
                DB_PASSWORD = 'root'
            }
            steps {
                sh 'php --version'
                sh 'composer install'
                sh 'composer --version'
                sh 'cp .env.example .env'
                sh 'echo DB_HOST=${DB_HOST} >> .env'
                sh 'echo DB_USERNAME=${DB_USERNAME} >> .env'
                sh 'echo DB_DATABASE=${DB_DATABASE} >> .env'
                sh 'echo DB_PASSWORD=${DB_PASSWORD} >> .env'
                sh 'php artisan key:generate'
                sh 'cp .env .env.testing'
                sh 'php artisan migrate'
            }
        }
        stage("Unit test") {
            steps {
                sh 'php artisan test'
            }
            post{
                success{
                    slackSend (color: 'good', message: "Test success")
                }
                failure{
                    slackSend (color: 'red', message: "Test failed")
                }
            }
        }
        stage("Code coverage") {
            steps {
                sh "vendor/bin/phpunit --coverage-html 'reports/coverage'"
            }
        }
        stage("Static code analysis larastan") {
            steps {
                sh "vendor/bin/phpstan analyse --memory-limit=2G"
            }
        }
        stage("Static code analysis phpcs") {
            steps {
                sh "vendor/bin/phpcs"
            }
        }
//         stage("Docker build") {
//             steps {
//                 sh "docker build -t zwenger/alfred ."
//             }
//         }
//         stage("Docker push") {
//             environment {
//                 DOCKER_USERNAME = credentials("docker-user")
//                 DOCKER_PASSWORD = credentials("docker-password")
//             }
//             steps {
//                 sh "docker login --username ${DOCKER_USERNAME} --password ${DOCKER_PASSWORD}"
//                 sh "docker push zwenger/alfred"
//             }
//         }
//         stage("Deploy to staging") {
//             steps {
//                 sh "docker run -d --rm -p 80:80 --name alfred zwenger/alfred"
//             }
//         }
//         stage("Acceptance test curl") {
//             steps {
//                 sleep 20
//                 sh "chmod +x acceptance_test.sh && ./acceptance_test.sh"
//             }
//         }
//         stage("Acceptance test codeception") {
//             steps {
//                 sh "vendor/bin/codecept run"
//             }
//             post {
//                 always {
//                     sh "docker stop alfred"
//                 }
//             }
//         }
    }
}
