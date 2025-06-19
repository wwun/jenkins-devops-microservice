/*//scripted
node {	// node es el bloque que se ejecuta en un agente, es exclusivo de scripted, en scripted, un pipeline no tiene etapas, pero se pueden crear bloques de código que
	echo "Build"
	echo "Test"
	echo "Test"
}

*/
// hay 2 tipos de scripts en Jenkins: Declarative y Scripted
// Declarative Pipeline es más fácil de leer y escribir, y es más adecuado para la mayoría de los casos de uso
// Scripted Pipeline es más flexible y poderoso, pero es más difícil de leer y escribir
// Declarative Pipeline es la forma recomendada de escribir pipelines en Jenkins

//declarative
pipeline { // exclusivo de declarative, un pipeline es un bloque que contiene todo el pipeline, es un must
    //agent any // cualquier agente disponible, esto puede ser un docker, un nodo, etc.
	agent {
		docker { // se especifica que el agente es un docker, esto es exclusivo de declarative
			image 'maven:3.9.9' // se especifica la imagen de docker que se va a usar, en este caso, una imagen de node.js
		}
		//label 'docker' // se puede especificar un agente por etiqueta, en este caso, un agente con la etiqueta docker
	}
    stages { // en declarative, un pipeline tiene etapas, por esto se necesita el bloque stages, es un must
        stage('Build') { // cada etapa tiene un nombre, y dentro de cada etapa hay pasos, que también son un must
            steps {
				echo "mvn --version" // se puede ejecutar un comando de shell, en este caso, se ejecuta el comando mvn --version
				sh 'mvn --version'
                echo "Build"
            }
        }
        stage('Test') {
            steps {
                echo "Test"
            }
        }
        stage('Integration Test') {
            steps {
                echo "Integration Test"
            }
        }
    }
    post { // post es un bloque que se ejecuta al final del pipeline, y puede tener diferentes condiciones
        always {
            echo "This will always run"
        }
        success {
            echo "This will run only if the pipeline is successful"
        }
        failure {
            echo "This will run only if the pipeline fails"
        }
    }
}
