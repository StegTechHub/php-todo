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
                        composer require --dev phpunit/phpunit:~4.0 --no-scripts
                        composer install --no-scripts
                    '''

                    // Refresh Laravel caches and configurations
                    sh '''
                        php artisan cache:clear
                        php artisan config:clear
                        php artisan view:clear
                        php artisan route:clear
                        
                        # Optionally rebuild caches
                        php artisan config:cache
                        
                    '''                  
                    
                    // Run Laravel artisan commands
                    sh '''                        
                        php artisan clear-compiled
                        php artisan migrate --force
                        php artisan db:seed --force
                    '''
                }
            }
        }
        stage ('Execute Unit Tests') {
            steps {
                sh './vendor/bin/phpunit'                
            }
        } 
    }
}
