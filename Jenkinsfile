#!/usr/bin/env groovy

/* Author: Alexandru Raul, raul.alexandru@atos.net */
/* This pipeline is running an Ansible playbook and role. */



pipeline {
    /* Environment */
   
    environment {
        def GIT_URL= "https://github.com/RO-TIM-CEB/ans_play_avamar_reports.git"
        def GIT_URL_ROLE= "https://github.com/lingeek/ans_role_avamar_reports_backup.git"
        def GIT_CREDENTIAL ="cf90f421-333c-4efd-8de7-4d67ec621e1a"
        def GIT_TOKEN_ID = "e1cb9b05-6531-439a-9287-d4669716e843"
        def GIT_BRANCH = "main"
        def AVAMAR_CREDENTIAL_ID = "bd957348-c142-4388-ba91-4002fcd67ae4"
        def PLAYBOOK_NAME = "main.yml"
        def TARGET = "wag_dc_spk_av"
        
        }
   /* END Environment */


    agent any
    /* Triggers  */
    /* This pipeline we'll automatically run at 10:00 on every day-of-week from Sunday through Sunday.*/
    triggers{ cron(' H H(11-12) * * *') }
   /* END Triggers */
   
   /* All stages */
    stages {

        /* Pull Terraform code from GitHUB */
        stage('Checkout Role') {
            steps {
                script {
                    properties([pipelineTriggers([pollSCM('* * * * *')])])
                }
                checkout([$class: 'GitSCM',
                          branches: [[name: "${GIT_BRANCH}"]],
                          doGenerateSubmoduleConfigurations: false,
                          extensions: [],
                          submoduleCfg: [],
                          userRemoteConfigs: [[credentialsId: "${GIT_CREDENTIAL}", url: "${GIT_URL_ROLE}"]]
                ])
            }
            post {
                success {
                    echo 'GIT checkout success!'
                }
                unstable {
                    echo 'I am unstable :/'
                }
                failure {
                    echo 'GIT checkout failed :('
                }
                changed {
                    echo 'Things were different before...'
                }
            }
        }
        /* End Pull Terraform code from GitHUB */

   /* Pull Terraform code from GitHUB */
        stage('Checkout Playbook') {
            steps {
                script {
                    properties([pipelineTriggers([pollSCM('* * * * *')])])
                }
                checkout([$class: 'GitSCM',
                          branches: [[name: "${GIT_BRANCH}"]],
                          doGenerateSubmoduleConfigurations: false,
                          extensions: [],
                          submoduleCfg: [],
                          userRemoteConfigs: [[credentialsId: "${GIT_CREDENTIAL}", url: "${GIT_URL}"]]
                ])
            }
            post {
                success {
                    echo 'GIT checkout success!'
                }
                unstable {
                    echo 'I am unstable :/'
                }
                failure {
                    echo 'GIT checkout failed :('
                }
                changed {
                    echo 'Things were different before...'
                }
            }
        }
        /* End Pull Terraform code from GitHUB */



        // New separate stage for setting up Git credentials and installing Ansible roles
        stage('Configure Git and Install Ansible Roles') {
            steps {
                // Configure Git to use Jenkins token and install required roles
                withCredentials([string(credentialsId: "${GIT_TOKEN_ID}", variable: 'GIT_TOKEN')]) {
                    sh '''
                    # Configure Git to use the token for authentication
                    git config --global credential.helper '!echo password=$GIT_TOKEN'

                    # Install required Ansible roles
                    ansible-galaxy install -r requirements.yml -p ./roles
                    '''
                }
            }
        }

/* End Stage setup Ansible requirements */

        /* Stage checkout */
 
         
         /* Stage Run Ansible playbook */
   stage('Generate Avamar reports') {
  steps {
    ansiblePlaybook(
      colorized: true,
      credentialsId: '${AVAMAR_CREDENTIAL_ID}',
      disableHostKeyChecking: true,
      extraVars: [target: "${TARGET}"],
      forks: 2,
      installation: 'Ansible-2.11.12',
      playbook: '${PLAYBOOK_NAME}'
      
    )
  }
}

        /* END Stage Run Ansible playbook */
    }
    /* END All stages */
}
        

