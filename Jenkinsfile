pipeline {
    agent any
    
    stages {
        stage('Hola Mundo') {
            steps {
                echo '¡Hola! Este es mi primer pipeline de Jenkins'
                echo 'Repositorio: jualvarez9/prueba1'
            }
        }
        
        stage('Información') {
            steps {
                echo "Número de build: ${env.BUILD_NUMBER}"
                echo "Nombre del job: ${env.JOB_NAME}"
                echo "Fecha y hora: ${new Date()}"
            }
        }
        
        stage('Explorar') {
            steps {
                echo 'Viendo qué archivos tengo:'
                sh 'ls -la'
                sh 'pwd'
            }
        }
        
        stage('Crear archivo') {
            steps {
                echo 'Creando un archivo de prueba...'
                sh 'echo "¡Hola desde Jenkins!" > saludo.txt'
                sh 'cat saludo.txt'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline terminado - siempre se ejecuta'
        }
        success {
            echo '¡Éxito! Todo funcionó correctamente'
        }
        failure {
            echo 'Ups, algo falló'
        }
    }
}