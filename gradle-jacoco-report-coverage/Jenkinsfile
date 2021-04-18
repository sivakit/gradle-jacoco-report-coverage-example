pipeline {
    agent any
    tools {
        
        gradle "gradle6.5.1"
        jdk "jdk8"
    }
    environment{
        
        GRADLE_USER_HOME="${WORKSPACE}/.gradle"
        
		M2_REPO="${WORKSPACE}/.m2/repository"
		
    }

    stages {
        stage('checkout') {
            steps {
                echo 'checkout'
                git credentialsId: 'git-creds', url: 'https://github.com/aparnadevops/gradle-jacoco-report-coverage-example.git'
            }
        }
        stage(' Compile microservice') {
                                  
            steps {
                script{
                    
                    echo 'Compile serviceName'
                    sh"""
                    ./gradlew --version
                   ./gradlew -Dmaven.repo.local=$M2_REPO clean compileJava assemble
                    """
                }
            }
        }  
        stage('Stage: Run Unit tests') {
            steps {
                script{
                    
                    echo 'Run Unittests'
                    sh"""
                    ./gradlew -Dmaven.repo.local=$M2_REPO test
                    """
                }
            }
        
        post {
				always{
					echo "Publish Junit jacoco HTML report"
					script {
					    publishHTML([
					        allowMissing: false,
					        alwaysLinkToLastBuild: false, 
					        keepAll: false,
					        reportDir: 'build/reports/tests', 
					        reportFiles: 'index.html',
					        reportName: 'jacoco Report', reportTitles: ''
					        ])
					    
					}
				}
            }
        }  
        
        stage('Stage: Code Coverage Report') {
            steps {
                script {
                   
                    echo 'Run code coverage'
                    sh  "./gradlew -Dmaven.repo.local=$M2_REPO jacocoTestReport"
                }
            }
        }
        stage('Stage: Publish Junit jacoco HTML report') {
            
            steps {
                script {					
                    
							publishHTML target: [
								allowMissing         : false,
								alwaysLinkToLastBuild: false,
								keepAll              : true,
								reportDir            : 'build/reports/jacoco-html',
								reportFiles          : 'index.html',
								reportName           : 'Junit Test Coverage Report'
							]
						}
					}
                
            
            post {
                failure {
                    echo "Publishing Junit Test coverage failed: ${currentBuild.result}"
                    sh 'exit 1'
                }
            }
        }
        stage(' Code Coverage - Gate') {
            
            steps {
                script {
                    
                    def jacoco_cov= "jacoco changeBuildStatus: true, classPattern: 'build/classes/main', deltaBranchCoverage: '10', deltaClassCoverage: '10', deltaComplexityCoverage: '10', deltaInstructionCoverage: '10', deltaLineCoverage: '10', deltaMethodCoverage: '10', maximumBranchCoverage: '30', maximumClassCoverage: '30', maximumComplexityCoverage: '30', maximumInstructionCoverage: '30', maximumLineCoverage: '30', maximumMethodCoverage: '30', minimumBranchCoverage: '30', minimumClassCoverage: '30', minimumComplexityCoverage: '30', minimumInstructionCoverage: '30', minimumLineCoverage: '30', minimumMethodCoverage: '30', sourcePattern: 'src/main/java'"
					lib = evaluate ("def cov() { $jacoco_cov }; return this")
					lib.cov()
				echo jacoco_cov
                }
            }
            post {
                failure {
                    echo 'Code Coverage RESULT'
                    sh 'exit 1'
                }
            }
        }
        stage('Stage: Run build') {
            
            steps {
                script{
                   
                    echo 'Build'
                   sh "./gradlew -Dmaven.repo.local=$M2_REPO build"
                }
            }
		}
		stage('Stage: Publish artifact to Nexus') {
            
            steps {
                script {
				  withCredentials([usernamePassword(credentialsId: 'nexus-credentials', usernameVariable: 'nexusUser', passwordVariable: 'nexusPassword')]) 
		          {
                    
                    echo "Publish artifacts to Nexus "
                     sh "./gradlew -Dmaven.repo.local=$M2_REPO publish"
				  }
                }
            }
        }
					
    }
}
