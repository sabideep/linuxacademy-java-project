pipeline {
	agent none
	environment {
		MAJOR_VERSION = 1
	}

	stages {
		stage('Unit test'){
			agent {
				label 'apache'
			}
			
			steps{
				sh 'ant -f test.xml -v'
				junit 'reports/result.xml'
			}
		}
		stage('build') {
			agent {
				label 'apache'
			}
			steps {
				sh 'ant -f build.xml -v'
			}

			post {
				success {
					archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
				}
			}
			
		}

		stage('deploy'){
			agent {
				label 'apache'
			}

			steps{        
				sh "if ![ -d '/var/www/html/rectangles/all/${env.BRANCH_NAME}' ]; then mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}; fi"
        		sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"

			}

		}

		stage('Runningfdd on Centos'){
			agent {
				label 'Centos'
			}

			steps {
        		sh "wget http://54.190.20.181/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        		sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
			}

		}

		stage('test of Debian'){
			agent {
				docker 'openjdk:8u121-jre'
			}
			steps{
        		sh "wget http://54.190.20.181/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        		sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
			}
		}

		stage('Promote to green'){
			agent {
				label 'apache'
			}
			when {
				branch 'master'
			}
			steps{
				sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
				
			}
		}

		stage('Promote from dev to master'){
			agent {
				label 'apache'
			}
			when {
				branch 'dev'

			}

			steps{
        		echo "Staddshing Any Local Changes"
        		sh 'git stash'
        		echo "Checkdding Out Development Branch"
        		sh 'git checkout dev'
        		echo 'Checking Out Master Branch'
        		sh 'git pull origin'
        		sh 'git checkout master'
        		echo 'Merging Development into Master Branch'
        		sh 'git merge dev'
        		echo 'Pushing to Origin Master'
        		sh 'git push origin master'
        		echo 'Tagging the Release'
        		sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
        		sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
			}

		}

	}
}


