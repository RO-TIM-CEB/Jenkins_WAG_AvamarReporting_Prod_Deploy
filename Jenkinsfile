#!/usr/bin/env groovy

/* Author: Alexandru Raul, raul.alexandru@atos.net */
/* This pipeline is running an Ansible playbook and role. */



pipeline {
    /* Environment */

    environment {
        def GIT_URL= "https://github.com/RO-TIM-CEB/ans_play_avamar_reports.git"
        def GIT_URL_ROLE= "https://github.com/lingeek/ans_role_avamar_reports_backup.git"
        def GIT_CREDENTIAL ="cf90f421-333c-4efd-8de7-4d67ec621e1a"
        def GIT_BRANCH = "main"
        def AVAMAR_CREDENTIAL_ID = "bd957348-c142-4388-ba91-4002fcd67ae4"
        def PLAYBOOK_NAME = "main.yml"
        def TARGET = "all_wag_av"
        }
   /* END Environment */


    agent any
    /* Triggers  */
    /* This pipeline we'll automatically run at 10:00 on every day-of-week from Sunday through Sunday.*/
    triggers{ cron(' H H(11-12) * * *') }
   /* END Triggers */
   /* All stages */
    stages {

        /* Pull Ansible playbook code from GitHUB */
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
        /* End Pull Ansible playbook code from GitHUB */

         /* Check YAML file */
       stage('Standardize YAML file') {
            steps {
                dir("${WORKSPACE}") {
                    sh "yamllint ."
                }
            }
        }
        /* End Check YAML file */


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


