pipeline {

  agent any



  environment {

    IMAGE = readMavenPom().getArtifactId()

    VERSION = readMavenPom().getVersion()

  }

    stages {

        stage('Checkout') {

            steps {

			    cleanWs()

				// Cloning the repository.

				checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[credentialsId: 'jenkins-user-github', url: 'https://github.com/buildpacks/sample-java-app.git']]])

            }

        }

        stage('Build and Unit test') {

            steps {

                sh 'mvn -B clean install'

            }

			post {

                always {

                    junit 'target/surefire-reports/*.xml'

                }

            }

        }

		stage('Build and Publish Image') {

          steps {

            sh """

			  docker login

              docker build -t ${IMAGE} .

              docker tag ${IMAGE} ${IMAGE}:${VERSION}

              docker push ${IMAGE}:${VERSION}

            """

          }

        }

		stage('Deploy to OCP') {

          steps {

            sh """

			  

            """

          }

        }

        post {

          failure {

            emailext(

              subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Failed!",

              body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Failed!":</p>

              <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",

              to: "@.com"

            )

          }

        }

    }

}

