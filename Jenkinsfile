pipeline {
    agent any
    stages {
        stage('拉取代码') {
            steps { echo '代码从GitHub SSH拉取完成' }
        }
        stage('项目构建') {
            steps { sh 'mvn clean package -DskipTests' }
        }
        stage('自动化测试') {
            steps { sh 'mvn test' }
        }
    }
    post {
        success { echo '流水线全部执行成功' }
        failure { echo '流水线执行异常，请查看日志' }
    }
}
