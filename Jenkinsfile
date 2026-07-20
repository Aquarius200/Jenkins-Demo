pipeline {
    agent any
    stages {
        stage('拉取代码') {
            steps {
                git url: 'git@github.com:Aquarius200/Jenkins-Demo.git', branch: 'main'
            }
        }
        
        stage('环境调试') {
            steps {
                echo "===== 打印当前系统PATH ====="
                sh 'echo $PATH'

                echo "===== 检查sh解释器位置 ====="
                sh 'which sh'

                echo "===== 检查node是否可调用 ====="
                sh '/usr/local/bin/node -v'

                echo "===== 直接用绝对路径校验newman版本 ====="
                sh '/Users/xyc/.npm-global/bin/newman -v'

                echo "===== 进入postman目录查看文件列表 ====="
                dir('postman'){
                    sh 'ls -l'
                }
            }
        }
        
        stage('执行Postman接口测试') {
            steps {
                dir('postman') {
                    sh '''
                    # 清理旧报告
                    rm -rf test_report allure-results
                    mkdir -p test_report
                    
                    # 生成所有需要的报告：原有报告 + Allure 报告
                    /Users/xyc/.npm-global/bin/newman run "Demo User API.postman_collection.json" \
                    -e "New Environment.postman_environment.json" \
                    -r cli,html,junitfull,allure \
                    --reporter-html-export test_report/api_report.html \
                    --reporter-junitfull-export test_report/junit_result.xml \
                    --reporter-allure-export allure-results
                    '''
                }
            }
        }
        
        // 新增：生成 Allure 报告的 Stage
        stage('生成 Allure 报告') {
            steps {
                dir('postman') {
                    allure([
                        includeProperties: false,
                        jdk: '',
                        properties: [],
                        reportBuildPolicy: 'ALWAYS',
                        results: [[path: 'allure-results']]
                    ])
                }
            }
        }
    }
    
    post {
        always {
            // 保留原有的 JUnit 报告
            junit allowEmptyResults: true, testResults: 'postman/test_report/junit_result.xml'
            
            // 保留原有的 HTML 报告
            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'postman/test_report',
                reportFiles: 'api_report.html',
                reportName: '接口测试报告'
            ])
            
            // 新增：发布 Allure 报告
            dir('postman') {
                allure([
                    includeProperties: false,
                    jdk: '',
                    properties: [],
                    reportBuildPolicy: 'ALWAYS',
                    results: [[path: 'allure-results']]
                ])
            }
        }
    }
}
