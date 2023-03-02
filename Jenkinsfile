pipeline {
    agent any
    stages {
        stage('Verify git commit conventions') {
            steps {
                sh 'sh ./gitlint.sh'
            }
        }
        stage('Verify .sh files') {
            steps {
                sh 'docker build --target verify-sh .'
            }
        }
        stage('Restore') {
            steps {
                sh 'docker build --target restore .'
            }
        }
        stage('Verify Format') {
            steps {
                sh 'docker build --target verify-format .'
            }
        }
        stage('Verify Markdown') {
            steps {
                sh 'docker build --target verify-markdown .'
            }
        }
        stage('Verify Spell') {
            steps {
                sh 'docker build --target verify-spell .'
            }
        }
    }
}
