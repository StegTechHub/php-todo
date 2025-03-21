pipeline {
    agent any
    stages {
        stage('Initial Cleanup') {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                }
            }
        }
        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/laraadeboye/php-todo-app.git'
            }
        }
        stage('Prepare Dependencies') {
            steps {
                script {
                    // Move .env.sample to .env and set environment variables
                    sh '''
                        mv .env.sample .env
                        echo "DB_HOST=${DB_HOST}" >> .env
                        echo "DB_PORT=${DB_PORT}" >> .env
                        echo "DB_DATABASE=${DB_DATABASE}" >> .env
                        echo "DB_USERNAME=${DB_USERNAME}" >> .env
                        echo "DB_PASSWORD=${DB_PASSWORD}" >> .env
                        echo "APP_ENV=${APP_ENV}" >> .env
                        echo "APP_DEBUG=${APP_DEBUG}" >> .env
                        echo "LOG_LEVEL=${LOG_LEVEL}" >> .env
                        echo "APP_KEY=${APP_KEY}" >> .env
                        echo "APP_URL=${APP_URL}" >> .env
                        echo "CACHE_DRIVER=${CACHE_DRIVER}" >> .env
                        echo "SESSION_DRIVER=${SESSION_DRIVER}" >> .env
                        echo "QUEUE_DRIVER=${QUEUE_DRIVER}" >> .env
                    '''
                    
                    // Create storage and bootstrap directories with appropriate permissions
                    sh '''
                        mkdir -p bootstrap/cache
                        mkdir -p storage/framework/sessions
                        mkdir -p storage/framework/views
                        mkdir -p storage/framework/cache                        
                        chown -R jenkins:jenkins bootstrap storage 
                        chmod -R 775 bootstrap storage 
                    '''

                    // Install Composer dependencies with error handling
                    sh '''
                        set -e                        
                        composer install 
                    '''                                    
                    
                    // Run Laravel artisan commands
                    sh '''                 
                        php artisan migrate --force
                        php artisan db:seed --force
                    '''
                }
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
        stage('Plot Code Coverage report') {
            steps {
                script {
                    // Plot phploc metrics
                    plot(
                        csvFileName: 'phploc.csv', 
                        group: 'Code Metrics', 
                        numBuilds: '100',
                        title: 'A- Lines of Code', 
                        style: 'line',
                        yaxis: 'Lines of Code',
                        csvSeries: [
                            [
                                displayTableFlag: false,
                                exclusionValues: 'Lines of Code (LOC), Comment Lines of Code (CLOC), Non-Comment Lines of Code (NLOC), Logical Lines of Code (LLC)',
                                file: 'build/logs/phploc.csv', 
                                inclusionFlag: 'INCLUDE_BY_STRING', 
                                url: ''
                            ]
                        ]
                    )
                    
                    plot(
                        csvFileName: 'phploc.csv', 
                        group: 'Code Metrics', 
                        numBuilds: '100',
                        title: 'B - Structures Containers', 
                        style: 'line',
                        yaxis: 'Count',
                        csvSeries: [
                            [
                                displayTableFlag: false,
                                exclusionValues: 'Directories, Files, Namespaces',
                                file: 'build/logs/phploc.csv', 
                                inclusionFlag: 'INCLUDE_BY_STRING', 
                                url: ''
                            ]
                        ]
                    )
                    
                    plot(
                        csvFileName: 'phploc.csv', 
                        group: 'Code Metrics', 
                        numBuilds: '100',
                        title: 'C - Average Length', 
                        style: 'line',
                        yaxis: 'Count',
                        csvSeries: [
                            [
                                displayTableFlag: false,
                                exclusionValues: 'Average Class Length (LLOC), Average Method Length (LLOC), Average Function Length (LLOC)',
                                file: 'build/logs/phploc.csv', 
                                inclusionFlag: 'INCLUDE_BY_STRING', 
                                url: ''
                            ]
                        ]
                    )
                    
                    plot(
                        csvFileName: 'phploc.csv', 
                        group: 'Code Metrics', 
                        numBuilds: '100',
                        title: 'D - Relative Cyclomatic Complexity', 
                        style: 'line',
                        yaxis: 'Cyclomatic Complexity  by Structure',
                        csvSeries: [
                            [
                                displayTableFlag: false,
                                exclusionValues: 'Cyclomatic Complexity / Lines of Code, Cyclomatic Complexity / Number of Methods',
                                file: 'build/logs/phploc.csv', 
                                inclusionFlag: 'INCLUDE_BY_STRING', 
                                url: ''
                            ]
                        ]
                    )
                    
                    plot(
                        csvFileName: 'phploc.csv', 
                        group: 'Code Metrics', 
                        numBuilds: '100',
                        title: 'E - Types of Classes', 
                        style: 'line',
                        yaxis: 'Count',
                        csvSeries: [
                            [
                                displayTableFlag: false,
                                exclusionValues: 'Classes, Abstract Classes, Concrete Classes',
                                file: 'build/logs/phploc.csv', 
                                inclusionFlag: 'INCLUDE_BY_STRING', 
                                url: ''
                            ]
                        ]
                    )
                    
                    plot(
                        csvFileName: 'phploc.csv', 
                        group: 'Code Metrics', 
                        numBuilds: '100',
                        title: 'F - Types of Methods', 
                        style: 'line',
                        yaxis: 'Count',
                        csvSeries: [
                            [
                                displayTableFlag: false,
                                exclusionValues: 'Methods, Non-Static Methods, Public Methods, Non-Public Methods',
                                file: 'build/logs/phploc.csv', 
                                inclusionFlag: 'INCLUDE_BY_STRING', 
                                url: ''
                            ]
                        ]
                    )
                    
                    plot(
                        csvFileName: 'phploc.csv', 
                        group: 'Code Metrics', 
                        numBuilds: '100',
                        title: 'G - Types of Constants', 
                        style: 'line',
                        yaxis: 'Count',
                        csvSeries: [
                            [
                                displayTableFlag: false,
                                exclusionValues: 'Constants, Global Constants, Class Constants',
                                file: 'build/logs/phploc.csv', 
                                inclusionFlag: 'INCLUDE_BY_STRING', 
                                url: ''
                            ]
                        ]
                    )
                    
                    plot(
                        csvFileName: 'phploc.csv', 
                        group: 'Code Metrics', 
                        numBuilds: '100',
                        title: 'H - Types of Functions', 
                        style: 'line',
                        yaxis: 'Count',
                        csvSeries: [
                            [
                                displayTableFlag: false,
                                exclusionValues: 'Functions, Named Functions, Anonymous Functions',
                                file: 'build/logs/phploc.csv', 
                                inclusionFlag: 'INCLUDE_BY_STRING', 
                                url: ''
                            ]
                        ]
                    )
                    
                    plot(
                        csvFileName: 'phploc.csv', 
                        group: 'Code Metrics', 
                        numBuilds: '100',
                        title: 'I - Testing', 
                        style: 'line',
                        yaxis: 'Count',
                        csvSeries: [
                            [
                                displayTableFlag: false,
                                exclusionValues: 'Test Classes, Test Methods',
                                file: 'build/logs/phploc.csv', 
                                inclusionFlag: 'INCLUDE_BY_STRING', 
                                url: ''
                            ]
                        ]
                    )
                    
                    plot(
                        csvFileName: 'phploc.csv', 
                        group: 'Code Metrics', 
                        numBuilds: '100',
                        title: 'AB - Code Structure by Logical Lines of Code (LLOC)', 
                        style: 'line',
                        yaxis: 'Logical Lines of Code',
                        csvSeries: [
                            [
                                displayTableFlag: false,
                                exclusionValues: 'Logical Lines of Code (LLOC), Classes Length, Functions Length, LLOC outside functions or classes',
                                file: 'build/logs/phploc.csv', 
                                inclusionFlag: 'INCLUDE_BY_STRING', 
                                url: ''
                            ]
                        ]
                    )
                    
                    plot(
                        csvFileName: 'phploc.csv', 
                        group: 'Code Metrics', 
                        numBuilds: '100',
                        title: 'BB - Structure Objects', 
                        style: 'line',
                        yaxis: 'Count',
                        csvSeries: [
                            [
                                displayTableFlag: false,
                                exclusionValues: 'Interfaces, Traits, Classes, Methods, Functions, Constants',
                                file: 'build/logs/phploc.csv', 
                                inclusionFlag: 'INCLUDE_BY_STRING', 
                                url: ''
                            ]
                        ]                       
                    )
                    
                    // Plot code coverage
                    plot(
                        csvFileName: 'coverage.csv', 
                        group: 'Code Coverage', 
                        title: 'Test Coverage', 
                        style: 'bar',
                        csvSeries: [
                            [
                                file: 'build/logs/clover.xml', 
                                inclusionFlag: 'OFF', 
                                url: ''
                            ]
                        ]
                    )
                }
            }
        }
stage('Package Artifact') {
    steps {
        sh 'zip -qr php-todo.zip ${WORKSPACE}/*'                
    }
} 

stage('Upload Artifact to Artifactory') {
    steps {
        script {
            def server = Artifactory.server 'artifactory-server'
            def uploadSpec = """{
                "files": [
                    {
                        "pattern": "php-todo.zip",
                        "target": "todo-artifact-local/php-todo",
                        "props": "type=zip;status=ready"
                    }
                ]
            }
            """
            server.upload(uploadSpec)
        }
    }
}
stage('Deploy to Dev environment') {
            steps {
                build job: 'ansible-config-mgt/main', parameters: [
                    [
                        $class: 'StringParameterValue', name: 'inventory', value: 'dev'                        
                    ],
                    [
                        $class: 'StringParameterValue', name: 'tags', value: 'todo'                        
                    ]                   
                ], propagate:false, wait:true
                                
            }
        }
        stage('SonarQube Quality Gate') {
        environment {
            scannerHome = tool 'SonarQubeScanner'
        }
        steps {
            withSonarQubeEnv('sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner"
            }

        }
    }
    }
}
