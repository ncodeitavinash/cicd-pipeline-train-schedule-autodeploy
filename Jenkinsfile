pipeline {
    agent any
    environment {
        //be sure to replace "willbla" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "ncodeit1234/train-schedule"
        CANARY_REPLICAS = 0
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    pipeline {
2
    agent any
3
    environment {
4
        //be sure to replace "willbla" with your own Docker Hub username
5
        DOCKER_IMAGE_NAME = "willbla/train-schedule"
6
    }
7
    stages {
8
        stage('Build') {
9
            steps {
10
                echo 'Running build automation'
11
                sh './gradlew build --no-daemon'
12
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
13
            }
14
        }
        
15
        stage('Build Docker Image') {
16
            when {
17
                branch 'master'
18
            }
19
            steps {
20
                script {
21
                    app = docker.build(DOCKER_IMAGE_NAME)
22
                    app.inside {
23
                        sh 'echo Hello, World!'
24
                    }
25
                }
26
            }
27
        }
28
        stage('Push Docker Image') {
29
            when {
30
                branch 'master'
31
            }
32
            steps {
33
                script {
34
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
35
                        app.push("${env.BUILD_NUMBER}")
36
                        app.push("latest")
37
                    }
38
                }
39
            }
40
        }
41
        stage('CanaryDeploy') {
42
            when {
43
                branch 'master'
44
            }
45
            environment { 
46
                CANARY_REPLICAS = 1
47
            }
48
            steps {
49
                kubernetesDeploy(}
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('CanaryDeploy') {
            when {
                branch 'master'
            }
            environment { 
                CANARY_REPLICAS = 1
            }
            steps {
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube-canary.yml',
                    enableConfigSubstitution: true
                )
            }
        }
        stage('SmokeTest') {
            when {
                branch 'master'
            }
            steps {
                script {
                    sleep (time: 5)
                    def response = httpRequest (
                        url: "http://$KUBE_MASTER_IP:8081/",
                        timeout: 30
                    )
                    if (response.status != 200) {
                        error("Smoke test against canary deployment failed.")
                    }
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                milestone(1)
                kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'train-schedule-kube.yml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
    post {
        cleanup {
            kubernetesDeploy(
                kubeconfigId: 'kubeconfig',
                configs: 'train-schedule-kube-canary.yml',
                enableConfigSubstitution: true
            )
        }
    }
}
