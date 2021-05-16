pipeline {
    agent any
    tools {
        maven 'maven3.6.1'
    }
    stages {
        stage('拉取gitlab代码'){
            steps {
                git credentialsId: 'ttt_test', url: 'https://github.com/jishunjun/spring-boot-test.git'
            }
        }
		stage('Test') {
			steps {
				sh "mvn clean test"
				junit '**/target/surefire-reports/*.xml'
			}
		}
        stage('构建') {
            steps {
                sh "mvn clean package -Dmaven.test.skip=true"
            }
        }
		stage('移动至代码仓库') {
			steps {
				sh "mkdir -p /usr/local/src/demo/0.0.1-SNAPSHOT/"
				sh "cp /data/jenkins_data/workspace/boot_demo/target/demo-0.0.1-SNAPSHOT.jar /usr/local/src/demo/0.0.1-SNAPSHOT/"
				sh "cp /root/kkb/javademo/startup.sh /usr/local/src/demo/0.0.1-SNAPSHOT/"
				sh "cp /root/kkb/javademo/stop.sh /usr/local/src/demo/0.0.1-SNAPSHOT/"
			}
		}

		stage('发布服务') {
			steps {
				sh "sshpass -p root ssh -p 22 root@192.168.225.136 'rm -rf /usr/local/src/demo/latest/*'"
				sh "sshpass -p root scp -P 22 /usr/local/src/demo/0.0.1-SNAPSHOT/demo-0.0.1-SNAPSHOT.jar root@192.168.225.136:/usr/local/src/demo/latest/"
				sh "sshpass -p root scp -P 22 /usr/local/src/demo/0.0.1-SNAPSHOT/startup.sh root@192.168.225.136:/usr/local/src/demo/latest/"
				sh "sshpass -p root scp -P 22 /usr/local/src/demo/0.0.1-SNAPSHOT/stop.sh root@192.168.225.136:/usr/local/src/demo/latest/"
			}
		}

		stage('启动服务') {
			steps {
				sh "sshpass -p root ssh -p 22 root@192.168.225.136 'chmod 755 /usr/local/src/demo/latest/stop.sh'"
				sh "sshpass -p root ssh -p 22 root@192.168.225.136 '/usr/local/src/demo/latest/stop.sh'"
				sh "sshpass -p root ssh -p 22 root@192.168.225.136 'chmod 755 /usr/local/src/demo/latest/startup.sh'"
				sh "sshpass -p root ssh -p 22 root@192.168.225.136 '/usr/local/src/demo/latest/startup.sh'"
			}
		}
	}
}



http://192.168.225.134/generic-webhook-trigger/invoke?token=EZ5A3XYaJZiI6MRbogaY


linux更新时间
yum install -y ntpdate
ntpdate 0.asia.pool.ntp.org












