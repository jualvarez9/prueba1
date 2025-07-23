pipeline {
    agent any
    
    triggers {
        // Se activa con push a la rama main
        githubPush()
    }
    
    environment {
        // Variables de entorno
        PYTHON_VERSION = 'python3'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Obteniendo código del repositorio...'
                checkout scm
            }
        }
        
        stage('Información') {
            steps {
                echo "Número de build: ${env.BUILD_NUMBER}"
                echo "Nombre del job: ${env.JOB_NAME}"
                echo "Rama: ${env.BRANCH_NAME ?: 'main'}"
                echo "Fecha y hora: ${new Date()}"
            }
        }
        
        stage('Preparar entorno Python') {
            steps {
                echo 'Verificando Python y preparando entorno...'
                sh '''
                    python3 --version
                    pip3 --version
                    # Crear entorno virtual si no existe
                    if [ ! -d "venv" ]; then
                        python3 -m venv venv
                    fi
                    # Activar entorno virtual
                    . venv/bin/activate
                    # Instalar dependencias si existen
                    if [ -f "requirements.txt" ]; then
                        pip install -r requirements.txt
                    fi
                '''
            }
        }
        
        stage('Ejecutar tests') {
            steps {
                echo 'Ejecutando tests de Python...'
                script {
                    try {
                        sh '''
                            . venv/bin/activate
                            # Ejecutar el archivo Python principal
                            python3 main.py
                            
                            # Si tienes tests específicos, descomenta la siguiente línea
                            # python3 -m pytest tests/ -v
                            
                            # O si usas unittest
                            # python3 -m unittest discover -s tests -v
                        '''
                        
                        // Si llegamos aquí, los tests pasaron
                        env.TESTS_PASSED = 'true'
                        echo '✅ Todos los tests pasaron correctamente'
                        
                    } catch (Exception e) {
                        env.TESTS_PASSED = 'false'
                        echo '❌ Los tests fallaron'
                        error("Tests fallaron: ${e.getMessage()}")
                    }
                }
            }
        }
        
        stage('Validación adicional') {
            when {
                environment name: 'TESTS_PASSED', value: 'true'
            }
            steps {
                echo 'Ejecutando validaciones adicionales...'
                sh '''
                    . venv/bin/activate
                    # Verificar sintaxis Python
                    python3 -m py_compile main.py
                    echo "✅ Sintaxis de Python válida"
                    
                    # Aquí puedes agregar más validaciones
                    # como linting, análisis de código, etc.
                '''
            }
        }
        
        stage('Deploy/Merge') {
            when {
                environment name: 'TESTS_PASSED', value: 'true'
            }
            steps {
                echo '✅ Tests exitosos - El código es válido para merge'
                echo 'En un entorno real, aquí se haría el deploy o merge automático'
                
                // Si quisieras hacer un merge automático (opcional):
                /*
                script {
                    if (env.BRANCH_NAME != 'main') {
                        sh '''
                            git config user.email "jenkins@yourcompany.com"
                            git config user.name "Jenkins CI"
                            git checkout main
                            git merge ${BRANCH_NAME}
                            git push origin main
                        '''
                    }
                }
                */
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline terminado - limpiando...'
            // Limpiar archivos temporales si es necesario
            sh 'rm -rf venv || true'
        }
        
        success {
            echo '🎉 ¡Éxito! Pipeline completado correctamente'
            echo 'El código pasó todos los tests y está listo'
        }
        
        failure {
            echo '💥 Pipeline falló'
            echo '❌ El código NO pasó los tests - NO se debe hacer merge'
            
            // Opcional: notificar por email, Slack, etc.
            /*
            emailext (
                subject: "❌ Build Failed: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                body: "El build falló. Revisa los logs en: ${env.BUILD_URL}",
                to: "tu-email@ejemplo.com"
            )
            */
        }
        
        unstable {
            echo '⚠️ Build inestable - revisar warnings'
        }
    }
}