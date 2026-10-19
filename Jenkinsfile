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
        stage('db-check'){
            steps{
                sh 'docker run -d --name pg-check -e POSTGRES_PASSWORD=test -e  POSTGRES_DB=checkdb postgres:16'
                sh 'until docker exec pg-check pg_isready -U postgres; do sleep 1; done'
                sh '''docker exec pg-check psql -U postgres -d checkdb -c "CREATE TABLE items (id INT, name TEXT) ; INSERT INTO items VALUES (1, 'item1'), (2, 'item2'), (3, 'item3'), (4, 'item4'),(5, 'item5');"'''      
                sh '''test "$(docker exec pg-check psql -U postgres -d checkdb -t -A -c "SELECT COUNT(*) FROM items;" )" = "6"'''
            }   
        }
    
    
    }
    post{
        always{
            sh 'docker rm -f pg-check || true'
        }
    }
}
