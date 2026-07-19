pipeline {
    agent any
    stages {
        stage('拉取GitHub代码') {
            steps {
                // 这一步你现在已经跑通了
                git url: 'git@github.com:Aquarius200/Jenkins-Demo.git', branch: 'main'
            }
        }
        stage('执行Postman接口自动化测试') {
            steps {
                // 切换到postman子文件夹,ok
                dir('postman') {
                    sh '''
                    # 清理旧报告、新建目录
                    rm -rf test_report
                    mkdir -p test_report
                    # 执行newman，注意文件名带空格要加双引号
                    newman run "Demo User API.postman_collection.json" \
                    -e "New Environment.postman_environment.json" \
                    -r cli,html,junitfull \
                    --reporter-html-export test_report/api_report.html \
                    --reporter-junitfull-export test_report/junit_result.xml
                    '''
                }
            }
        }
    }
    post {
        always {
            // 1.解析JUnit用例结果（统计成功/失败接口数）
            junit allowEmptyResults: true, testResults: 'postman/test_report/junit_result.xml'
            // 2.发布HTML可视化报告
            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'postman/test_report',
                reportFiles: 'api_report.html',
                reportName: '接口测试详细报告'
            ])
        }
    }
}
