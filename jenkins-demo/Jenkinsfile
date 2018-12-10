node('jnlp') {
    stage('Prepare') {
        echo "1.Prepare Stage"
        checkout scm
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            if (env.BRANCH_NAME != 'master') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
            }
        }
    }
    stage('Test') {
      echo "2.Test Stage"
    }
    stage('Build') {
        echo "3.Build Docker Image Stage"
        sh "docker build -t http://192.168.20.197:5000/jenkins-demo:${build_tag} ."
    }
    stage('Push') {
        echo "4.Push Docker Image Stage"
        #withCredentials([usernamePassword(credentialsId: 'aliHub', passwordVariable: 'aliHubPassword', usernameVariable: 'aliHubUser')]) {
           # sh "docker login -u ${aliHubUser} -p ${aliHubPassword}"
           # sh "docker push registry.cn-shenzhen.aliyuncs.com/pandamir/jenkins-demo:${build_tag}"
        #}
        sh "docker push 192.168.20.197:5000/jenkins-demo:${build_tag}"
    }
    stage('Deploy') {
        echo "5. Deploy Stage"
        if (env.BRANCH_NAME == 'master') {
            input "确认要部署线上环境吗？"
        }
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' k8s.yaml"
        sh "kubectl apply -f k8s.yaml --record"
    }
}
