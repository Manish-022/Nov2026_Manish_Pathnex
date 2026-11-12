Day 12 - Scaling Applications and Automation
* Ansible - Setup Auto-Scaling Group (ASG)
- name: Setup Auto Scaling Group
  hosts: all
  become: yes
  tasks:
    - name: Create Launch Configuration
      ec2_asg:
        name: pathnex-asg 
        region: us-east-1
        launch_config_name: pathnex-launch-config 
        min_size: 3
        desired_capacity: 2
        vpc_zone_identifier: subnet-12345678
*Terraform - Create a Load Balancer with EC2 Instances
resource "aws_lb" "pathnex_lb" {
    name             = "pathnex-lb"
    internal         = "false"
    load_balancer_type = "application"
    security_groups    = [aws_security_group.allow_ssh_http.id]
    subnets            = [aws_subnet.id.public.id]
}


resource "aws_instance" "pathnex_ec2" {
    ami         = "ami-0abcd1234abcd1234"
    instance_type = "t2.micro"
    security_groups = [aws_security_group.allow_ssh_http.name]
    tags = {
        Name = "pathnex-EC2"
    }
}
*Kubernetes - Horizontal Pod Autoscaler (HPA) with Load Balancer
apiVersion: apps/v1
kind :Deployment
metadata:
   name: pathnex-web-application
   spec:
     replica: 2
     selector:
       matchLabels:
         app: pathnex-web-app
     template:
       metadata:
          labels:
             app: pathnex-web-app
       spec:
         containers: 
            -name: web
            image: nginx
            ports:
               - containerPort: 80
------
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
   name: pathnex-web-app-hpa 
spec:
   scaleTargetRef:
   apiVersion: apps/v1
   kind: Deployment
   name: pathnex-web-app
minReplicas: 1
maxReplicas: 5
targetCPUUtilizationPercentage: 80
*Jenkinsfile - Implement Parallel Stages
pipeline {
    agent any
    satges {
        satge('Build & Test'){
            Parallel {
                stage('Build') {
                    steps {
                        echo 'Building project...'
                    }
                }
                stage('Test') {
                    steps {
                        echo 'Running tests...'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying project...'
            }
        }
    }
}
*Gitlab CI/CD - Parallel Jobs for Build & test
stages:
  -build
  -test
  -deploy 

build:
  stage: build
  script:
    - docker build -t pathnex-web-app .

test:
  stage: test
  script:
    - docker run pathnex-web-app npm test

deploy:
  stage: deploy
  script:
    - kubectl apply -f Kubernetes/deployment.yaml


    * docker
    # Docker Volumes
    From ubuntu 
    VOLUME ["/opt/pathnex/data"]
    CMD ["sleep","1000"]

    #Real path
    /opt/pathnex/data

    # Bash command
    docker volume create pathnex-volume

