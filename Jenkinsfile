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

    stage('Plot Code Coverage Report') {
            steps {
                script {
                    // Plot various metrics from the phploc.csv file
                    plot csvFileName: 'plot-loc.csv', 
                         csvSeries: [[displayTableFlag: false, exclusionValues: 'Lines of Code (LOC),Comment Lines of Code (CLOC),Non-Comment Lines of Code (NCLOC),Logical Lines of Code (LLOC)', 
                                      file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], 
                         group: 'phploc', numBuilds: '100', style: 'line', title: 'A - Lines of code', yaxis: 'Lines of Code'
                    
                    plot csvFileName: 'plot-structures.csv', 
                         csvSeries: [[displayTableFlag: false, exclusionValues: 'Directories,Files,Namespaces', 
                                      file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], 
                         group: 'phploc', numBuilds: '100', style: 'line', title: 'B - Structures Containers', yaxis: 'Count'
                    
                    plot csvFileName: 'plot-average-length.csv', 
                         csvSeries: [[displayTableFlag: false, exclusionValues: 'Average Class Length (LLOC),Average Method Length (LLOC),Average Function Length (LLOC)', 
                                      file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], 
                         group: 'phploc', numBuilds: '100', style: 'line', title: 'C - Average Length', yaxis: 'Average Lines of Code'
                    
                    plot csvFileName: 'plot-cyclomatic-complexity.csv', 
                         csvSeries: [[displayTableFlag: false, exclusionValues: 'Cyclomatic Complexity / Lines of Code,Cyclomatic Complexity / Number of Methods', 
                                      file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], 
                         group: 'phploc', numBuilds: '100', style: 'line', title: 'D - Relative Cyclomatic Complexity', yaxis: 'Cyclomatic Complexity by Structure'
                    
                    plot csvFileName: 'plot-class-types.csv', 
                         csvSeries: [[displayTableFlag: false, exclusionValues: 'Classes,Abstract Classes,Concrete Classes', 
                                      file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], 
                         group: 'phploc', numBuilds: '100', style: 'line', title: 'E - Types of Classes', yaxis: 'Count'
                    
                    plot csvFileName: 'plot-method-types.csv', 
                         csvSeries: [[displayTableFlag: false, exclusionValues: 'Methods,Non-Static Methods,Static Methods,Public Methods,Non-Public Methods', 
                                      file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], 
                         group: 'phploc', numBuilds: '100', style: 'line', title: 'F - Types of Methods', yaxis: 'Count'
                    
                    plot csvFileName: 'plot-constants-types.csv', 
                         csvSeries: [[displayTableFlag: false, exclusionValues: 'Constants,Global Constants,Class Constants', 
                                      file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], 
                         group: 'phploc', numBuilds: '100', style: 'line', title: 'G - Types of Constants', yaxis: 'Count'
                    
                    plot csvFileName: 'plot-testing.csv', 
                         csvSeries: [[displayTableFlag: false, exclusionValues: 'Test Classes,Test Methods', 
                                      file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], 
                         group: 'phploc', numBuilds: '100', style: 'line', title: 'I - Testing', yaxis: 'Count'
                    
                    plot csvFileName: 'plot-code-structure.csv', 
                         csvSeries: [[displayTableFlag: false, exclusionValues: 'Logical Lines of Code (LLOC),Classes Length (LLOC),Functions Length (LLOC),LLOC outside functions or classes', 
                                      file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], 
                         group: 'phploc', numBuilds: '100', style: 'line', title: 'AB - Code Structure by Logical Lines of Code', yaxis: 'Logical Lines of Code'
                    
                    plot csvFileName: 'plot-functions-types.csv', 
                         csvSeries: [[displayTableFlag: false, exclusionValues: 'Functions,Named Functions,Anonymous Functions', 
                                      file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], 
                         group: 'phploc', numBuilds: '100', style: 'line', title: 'H - Types of Functions', yaxis: 'Count'
                    
                    plot csvFileName: 'plot-structure-objects.csv', 
                         csvSeries: [[displayTableFlag: false, exclusionValues: 'Interfaces,Traits,Classes,Methods,Functions,Constants', 
                                      file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], 
                         group: 'phploc', numBuilds: '100', style: 'line', title: 'BB - Structure Objects', yaxis: 'Count'
                }
            }
        }
        stage ('Package Artifact') {
            steps {
              sh 'zip -qr php-todo.zip ${WORKSPACE}/*'
            }
        }

  
  }
}