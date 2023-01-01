node {
    checkout scm

    stage('Build') {
        docker.image('shippingdocker/php-composer:7.4').inside('-u root') {
            sh 'rm composer.lock'
            sh 'composer install'
        }
    }
    stage('Test') {
        docker.image('php:7.4-cli').inside('-u root') {
            sh 'php artisan test --testsuite=Unit'
        }
    }
    stage('Deploy Dev') {
        docker.image('agung3wi/alpine-rsync:1.1').inside('-u root') {
            sshagent (credentials: ['ssh-dev']) {
                sh 'mkdir -p ~/.ssh'
                sh 'ssh-keyscan -H "13.250.179.79" > ~/.ssh/known_hosts'
                sh "rsync -rav --delete ./ ubuntu@13.250.179.79:/home/ubuntu/dev.kelasdevops.xyz/ --exclude=.env --exclude=storage --exclude=.git"        }
        }
    }

    stage('Test') {
        docker.image('php:7.4-cli').inside('-u root') {
            sh 'php artisan test --testsuite=Feature'
        }
    }

    stage('Deploy Prod') {
        docker.image('agung3wi/alpine-rsync:1.1').inside('-u root') {
            sshagent (credentials: ['ssh-prod']) {
                sh 'mkdir -p ~/.ssh'
                sh 'ssh-keyscan -H "18.142.107.246" > ~/.ssh/known_hosts'
                sh "rsync -rav --delete ./ ubuntu@18.142.107.246:/home/ubuntu/prod.kelasdevops.xyz/ --exclude=.env --exclude=storage --exclude=.git"        }
        }
    }
}
