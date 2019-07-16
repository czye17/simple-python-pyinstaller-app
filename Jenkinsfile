pipeline {
    agent none
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Gather_Parameters') {
            agent any
            input {
                    message "Should we continue?"
                    ok "Yes, we should."
                    parameters {
                        text(name: 'ONE', defaultValue: '0', description: 'First Number')
                        text(name: 'TWO', defaultValue: '0', description: 'Second Number')
                    }
                }
            steps {
                script {
                    env.ONE = ONE
                }
                echo "Selected Environment: ${ONE}"
             }
        }
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                sh "python hello.py ${env.ONE}"
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
                echo "Selected Environment: ${env.ONE}"
                sh "dist/add2vals ${env.ONE} ${env.TWO}"
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals' 
                }
            }
        }
    }
}
