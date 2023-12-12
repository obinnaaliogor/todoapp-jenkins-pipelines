Running Vikunja in a pipeline with jenkins:

![Pipeline Result ]()


```markdown
# Setting up Jenkins on Ubuntu Distro

I used the Ubuntu distribution for the Jenkins server, specifically the t2.xlarge instance type.

## Install Jenkins

Follow this guide to install Jenkins on your Ubuntu server: [Installation Guide for Ubuntu](https://github.com/DeekshithSN/cheatsheet/blob/master/installation_guide_ubuntu.md)

## Install Helm

Follow the Helm installation guide: [Helm Installation Guide](https://helm.sh/docs/intro/install/)

Check Helm version:
```shell
helm version || echo "Helm is not installed"
version.BuildInfo{Version:"v3.13.2", GitCommit:"2a2fb3b98829f1e0be6fb18af2f6599e0f4e8243", GitTreeState:"clean", GoVersion:"go1.20.10"}
```

## Machine Too Slow? Change to t2.xlarge

If your machine is too slow, consider changing to the t2.xlarge instance type.

## Install Terraform

Follow the Terraform CLI installation guide: [Terraform Installation Guide](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)

## Jenkins Pipeline Configuration

Here is a sample Jenkins pipeline configuration:

```groovy
pipeline {
    agent any

    environment {
        HELM_REPOSITORIES = '[{"name": "truecharts", "url": "https://charts.truecharts.org/"}, {"name": "jetstack", "url": "https://charts.jetstack.io"}]'
        NGINX_CHART_VERSION = '4.8.4' // Replace with the actual version
        SLACK_CHANNEL = '#buildstatus-jenkins-pipeline'
        EKS_CLUSTER_NAME = 'demo'
        RELEASE_NAME = 'my-vikunja'
        CERT_MANAGER_HELM_CHART_VERSION = '1.13.2'
        AWS_DEFAULT_REGION = 'us-east-1'
        TERRAFORM_ACTION = 'destroy'
        REGION = 'us-east-1'
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }

    stages {
        // ... (add your stages here)
    }

    post {
        success {
            script {
                slackSend(
                    channel: env.SLACK_CHANNEL,
                    color: 'good',
                    message: "Pipeline succeeded for ${env.JOB_NAME} ${env.BUILD_NUMBER}: ${env.BUILD_URL}"
                )
            }
        }
        failure {
            script {
                slackSend(
                    channel: env.SLACK_CHANNEL,
                    color: 'danger',
                    message: "Pipeline failed for ${env.JOB_NAME} ${env.BUILD_NUMBER}: ${env.BUILD_URL}"
                )
            }
        }
    }
}
```
