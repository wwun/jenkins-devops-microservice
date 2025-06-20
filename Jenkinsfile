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
    agent any // cualquier agente disponible, esto puede ser un docker, un nodo, etc.
	//agent {	// se especifica el agente que se va a usar, en este caso, un agente Docker, por esto, todo lo qe está dentro de stages se ejecutará dentro de un contenedor Docker
		//docker { // se especifica que el agente es un docker, esto es exclusivo de declarative
			//image 'maven:3.9.9' // se especifica la imagen de docker que se va a usar, en este caso, una imagen de node.js, si no se encuentra la imagen, se descargará desde Docker Hub
			//image 'node:20.16.0'
		//}
		//Jenkins usará Docker para crear un contenedor basado en la imagen maven:3.9.9
		//label 'docker' // se puede especificar un agente por etiqueta, en este caso, un agente con la etiqueta docker
	//}
	environment { // se pueden definir variables de entorno, en este caso, se definen las rutas de las herramientas que se van a usar, como Docker y Maven
		dockerHome = tool 'myDocker' // se puede usar la herramienta de Jenkins para definir la ruta de la herramienta, en este caso, la ruta de Docker, la qe fue creada desde el mismo jenkins
		mavenHome = tool 'myMaven' // se puede usar la herramienta de Jenkins para definir la ruta de la herramienta, en este caso, la ruta de Maven
		// la diferencia de este dockerHome con docker es que este es un alias, y se puede usar en cualquier parte del pipeline, mientras que docker es una variable de entorno que se define en el agente
		PATH = "$dockerHome/bin:$mavenHome/bin:$PATH" // se puede modificar la variable PATH para incluir las rutas de las herramientas que se van a usar, en este caso, se incluye la ruta de Docker y Maven
	}
    stages { // en declarative, un pipeline tiene etapas, por esto se necesita el bloque stages, es un must
        stage('Checkout'){ // se puede usar una etapa para hacer un checkout del código, en este caso, se hace un checkout del código desde el repositorio
            steps {
				//echo "mvn --version" // se puede ejecutar un comando de shell, en este caso, se ejecuta el comando mvn --version
				sh 'mvn --version'
				//sh 'node --version'
				sh 'docker --version' // se puede ejecutar un comando de shell, en este caso, se ejecuta el comando docker --version
                echo "Build"
				echo "PATH - $PATH" // se puede acceder a las variables de entorno, en este caso, se imprime la variable PATH
				echo "BUILD_NUMBER - $env.BUILD_NUMBER" // se puede acceder a las variables de entorno de Jenkins, en este caso, se imprime el número de construcción
				echo "BUILD_ID = $env.BUILD_ID"
				echo "BUILD_NAME = $env.BUILD_NAME"
				echo "BUILD_TAG = $env.BUILD_TAG"
				echo "BUILD_URL = $env.BUILD_URL"
				//la diferencia entre sh y echo es que sh ejecuta un comando de shell, mientras que echo imprime un mensaje en la consola de Jenkins
            }
        }
		stage('Build') { // cada etapa tiene un nombre, y dentro de cada etapa hay pasos, que también son un must
			steps {
				sh "mvn clean compile" // se puede ejecutar un comando de shell, en este caso, se ejecuta el comando mvn clean compile
			}
		}
        stage('Test') {
            steps {
                echo "mvn test"
            }
        }
        stage('Integration Test') {
            steps {
                sh "mvn failsafe:integration-test failsafe:verify" // ambos comandos se usan para ejecutar pruebas de integración, el primero ejecuta las pruebas y el segundo verifica los resultados
				//estas pruebas se ejecutan después de las pruebas unitarias, y se usan para verificar que el sistema funciona correctamente en un entorno de integración
				//para ejecutar las pruebas de integración, se usa el plugin failsafe de Maven, que se encarga de ejecutar las pruebas de integración y verificar los resultados
				//esto a su vez usa cucumber, que es un framework de pruebas de integración que se usa para escribir pruebas de aceptación en lenguaje natural
				//cucumber se agrega en el pom.xml del proyecto, y se configura para que se ejecute después de las pruebas unitarias
				//la configuración y las clases de prueba se encuentran en el directorio src/test/java, y las pruebas se ejecutan en el directorio target/failsafe-reports
            }
        }
		stage("Build Docker image") {
			steps {
				//docker build -t wwun/currency-exchange-devops:$env.BUILD_TAG //esta línea se usa para construir una imagen de Docker, en este caso, se construye una imagen de Docker con el nombre wwun/currency-exchange-devops y la etiqueta del número de construcció
				script{
					dockerImage = docker.build("wwun/currency-exchange-devops:${env.BUILD_TAG}") //esta es otra manera de construir una imagen de Docker, en este caso, se construye una imagen de Docker con el nombre wwun/currency-exchange-devops y la etiqueta del número de construcción
				}

			}
		}
		stage("Push Docker image") {
			steps {
				script {
					docker.withRegistry('', 'docker.hub') { // se usa para autenticar el usuario de Docker Hub, en este caso, se usa el usuario wwun y la contraseña que se encuentra en las credenciales de Jenkins, fueron agregadas manualmente, el primer parámetro es la URL del registro, y el segundo es el ID de las credenciales de Jenkins
					dockerImage.push() // se usa para subir la imagen de Docker al repositorio, en este caso, se sube la imagen de Docker al repositorio de Docker Hub
					dockerImage.push("latest");
				}
			}
		}
    }
	//Todo lo que está dentro del bloque stages (como Build, Test, Integration Test) se ejecutará dentro del contenedor Docker
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
