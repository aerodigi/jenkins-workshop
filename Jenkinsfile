pipeline {
    agent any

    stages {
        stage('Debug Info') {
            steps {
                echo "Building branch '${params.BRANCH}'"
                sh 'pwd'
                sh 'whoami'
                sh 'docker info'
            }
        }

        stage('Build') {
            steps {
                sh './build.sh'
            }
        }

        stage('Test') {
            steps {
                sh './dockertest.sh'
            }
        }

        // If the branch is master...
        stage('Deploy') {
            when {
                expression { params.BRANCH == 'master' }
            }
            steps {
                sh '''
                    eval `ssh-agent -s` && ssh-add
                    if ! grep "$(ssh-keyscan github.com 2>/dev/null)" ~/.ssh/known_hosts > /dev/null; then ssh-keyscan github.com >> ~/.ssh/known_hosts; fi
                    git remote set-url origin-ssh git@github.com:WillGibson/jenkins-workshop.git || git remote add origin-ssh git@github.com:WillGibson/jenkins-workshop.git
                    git fetch --all
                    git checkout master
                    git pull
                    git checkout heroku-deploy
                    git merge master
                    git push origin-ssh heroku-deploy
                '''
            }
        }

        stage('Smoke Tests') {
            when {
                expression { params.BRANCH == 'master' }
            }
            steps {
                echo 'ToDo: Run some smoke tests on the deployed app'
                echo 'ToDo: Post smoke test failure alert (e.g. in Slack)'
            }
        }

        stage('Rollback on Smoke Test Failure') {
            when {
                expression { params.BRANCH == 'master' }
            }
            steps {
                echo 'ToDo: Run some smoke tests on the deployed app'
                echo 'ToDo: Post rollback alert (e.g. in Slack)'
            }
        }
    }
}
