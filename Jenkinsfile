pipeline {
  agent any
  stages {
    stage('Initial cleanup') {
      steps {
        dir(path: "${WORKSPACE}") {
          deleteDir()
        }

      }
    }

    stage('Checkout SCM') {
      steps {
        git(branch: 'main', url: 'https://github.com/ilyesbenothmen/php-todo.git')
      }
    }


    stage('Prepare Dependencies') {
      steps {
        sh 'php -v'
        sh 'mkdir -p bootstrap/cache storage/framework/cache storage/framework/sessions storage/framework/views storage/logs'
        sh 'chmod -R 775 bootstrap/cache storage'
        sh 'mv .env.sample .env'
        sh 'composer install --no-interaction --prefer-dist'
        sh 'php artisan key:generate'
        sh 'php artisan migrate --force'
        sh 'php artisan db:seed --force'
      }
    }
    stage('Execute Unit Tests') {
      steps {
             sh './vendor/bin/phpunit'
      } 
    } 
    stage('Code Analysis') {
      steps {
            sh 'phploc app/ --log-csv build/logs/phploc.csv'
      }
    }   
  }
}