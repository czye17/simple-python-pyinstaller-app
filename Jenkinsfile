pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                  docker {
                      image 'python:2-alpine'
                  }
            }
            steps {
                  sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Gather Parameters') {
             timeout(time: 30, unit: 'SECONDS') {
                      script {
                          // Show the select input modal
                          def INPUT_PARAMS = input message: 'Please input numbers', ok: 'Next',
                                             parameters: [
                                             text(name: 'ONE', defaultValue: 0, description: 'First Number'),
                                             text(name: 'TWO', defaultValue: 0, description: 'Second Number')]
                          env.ONE = INPUT_PARAMS.ONE
                          env.TWO = INPUT_PARAMS.TWO
                      }
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
                    sh 'echo $(env.ONE)'
                    sh 'dist/add2vals $(env.ONE) $(env.TWO)
                }
                post {
                    success {
                        archiveArtifacts 'dist/add2vals'
                    }
                }
        }
    }
}

