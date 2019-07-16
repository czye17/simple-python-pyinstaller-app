pipeline {
    agent none
    options {
        skipStagesAfterUnstable()
    }
    parameters {
        text(name: 'ONE', defaultValue: '0', description: 'First Number')
        text(name: 'TWO', defaultValue: '0', description: 'Second Number')
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                sh "python hello.py ${params.ONE}"
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') { 
            agent {
                docker {
                    image 'cdrx/pyinstaller-linux:python2' 
                }
            }
            steps {
                sh 'pyinstaller --onefile sources/add2vals.py' 
                echo "Selected Environment: ${params.ONE}"
                sh "dist/add2vals ${params.ONE} ${params.TWO}"
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals' 
                }
            }
        }
    }
}
