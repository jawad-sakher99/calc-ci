pipeline{
    agent any 
    stages{
        stage('lint'){
            agent{
                docker{
                    image 'python:3.12'
                }

            }
            environment{
                HOME = '/tmp'
            }
            steps{
                sh 'pip install --user ruff==0.17.1' 
                sh 'python -m ruff check .'
            }
        }
    
        stage('test'){
            matrix{
                axes{
                    axis{
                        name 'PY'
                        values '3.11', '3.12'

                    }

                }
               stages{
                stage ('pytest'){
                    agent{
                        docker{
                            image "python:${PY}"
                        }
                    }
                    environment{
                        HOME = '/tmp'
                    }
                    steps{
                        sh 'pip install --user -r requirements.txt'
                        sh 'python -m pytest'                        
                    }
                }
               } 
            }
        }
    
    
    
    }
}