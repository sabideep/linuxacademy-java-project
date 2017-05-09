pipeline {
	
	agent any

	stages{
		stage('Build'){
			steps{
				sh 'javac -d . src/Rectangle.java src/Rectangulator.java'
				sh 'echo Main-Class: Rectangulator > MANIFEST.MF'
				sh 'jar -cvmf MANIFEST.MF rectangle.jar *.class'
			}
		}

		stage('Run Build'){
			steps{
				sh 'java -jar rectangle.jar 7 9'
			}
		}

		post('Archiving'){
			success{
				archiveArtifacts artifacts: 'rectangle.jar', fingerprint: true
			}
		}


	}
}
