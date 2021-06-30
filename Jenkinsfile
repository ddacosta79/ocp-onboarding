pipeline {
    environment {
        // Common variables for the pipeline and projects
        DOMAIN='apps.ocpddc1.os.fyre.ibm.com'
        devworkloadselectorvalue="env=dev"
        proworkloadselectorvalue="env=pro"
    }
    agent {
      node {
        label 'nodejs'
      }
    }
    parameters {
        // Input parameter for the pipeline is project name
        string(name: 'PROJECT', defaultValue: 'my-project', description: 'WARNING !!! Project name in lowercase')
    }
    stages {
        stage('set env') {
            steps {
                script {
                    // Step to create all necessary variables for the pipeline
                    echo("working on Project ${params.PROJECT}")
                    def projectname = "${params.PROJECT}"
                    // transform project name to lowercase. Mandatory for openshift projects
                    def project = projectname.toLowerCase()
                    // production project name
                    PRJPRO="${project}-pro"
                    // development project name
                    PRJDEV="${project}-dev"
                }
            }
        }
        stage('create env DEV') {
            steps {
                script {
                    // Uncomment to get lots of debugging output
                    // openshift.logLevel(1)
                    openshift.withCluster() {
                        def prjexist = openshift.selector('project', "${PRJDEV}").exists()
                        echo("starting to create project ${PRJDEV}")
                        def project= "${PRJDEV}"
                        if (!prjexist) {
                            echo("Creating project ${PRJDEV}")
                            // Step to create Project on Openshift
                            openshift.newProject("${PRJDEV}", '"' + "--display-name=${params.PROJECT} Development Project" + '"', '"' + "--description=${params.PROJECT} Development Project"+ '"')
                            // Step to add labels to the Project 
                            openshift.raw('label', 'namespace', '"'+"${PRJDEV}"+'"', "${env.devworkloadselectorvalue} app=${PRJDEV}")
                            // Step to add annotations to the Project 
                            openshift.raw('annotate', 'namespace', '"'+"${PRJDEV}"+'"', "openshift.io/node-selector=${env.devworkloadselectorvalue}")
                            // Step to grant project access to groups 
                            openshift.withProject("${PRJDEV}"){
                                echo('Granting access to the project to groups admins and developers')
                                openshift.raw('policy', 'add-role-to-group', 'view', 'admins')
                                openshift.raw('policy', 'add-role-to-group', 'admin', 'developers')
                            } 
                        } 
                        else {
                            echo("Project ${PRJDEV} already exist")
                            openshift.withProject("${PRJDEV}"){
                                echo('Granting access to the project')
                                openshift.raw('policy', 'add-role-to-group', 'edit', 'admins')
                                openshift.raw('policy', 'add-role-to-group', 'view', 'developers')
                            } 
                        }
                    }
                }
            }
        }
        stage('create env PRO') {
            steps {
                script {
                    // Uncomment to get lots of debugging output
                    //openshift.logLevel(1)
                    openshift.withCluster() {
                        def prjexist = openshift.selector('project', "${PRJPRO}").exists()
                        echo("working to create project ${PRJPRO}")
                        if (!prjexist) {
                            echo("Creating project ${PRJPRO}")
                            openshift.newProject("${PRJPRO}", '"' + "--display-name=${params.PROJECT} Production Project" + '"', '"' + "--description=${params.PROJECT} Production Project"+ '"')
                            openshift.raw('label', 'namespace', '"'+"${PRJPRO}"+'"', "${env.proworkloadselectorvalue} app=${PRJPRO}")
                            openshift.raw('annotate', 'namespace', '"'+"${PRJPRO}"+'"', "openshift.io/node-selector=${env.proworkloadselectorvalue}")
                            //openshift.newProject("${PRJPRO}", "--display-name=${env.PRJ}_Production_Project", "--description=${env.PRJ}_Production_Project")
                            //openshift.raw('label', 'project', '\\"${PRJPRO}\\"', "${env.proworkloadselectorvalue}")
                            openshift.withProject("${PRJPRO}"){
                                echo('Granting access to the project to groups admins and developers')
                                openshift.raw('policy', 'add-role-to-group', 'edit', 'admins')
                                openshift.raw('policy', 'add-role-to-group', 'view', 'developers')
                            } 
                        } 
                        else {
                            echo("Project ${PRJPRO} already exist")
                            openshift.withProject("${PRJPRO}"){
                                echo('Granting access to the project')
                                openshift.raw('policy', 'add-role-to-group', 'edit', 'admins')
                                openshift.raw('policy', 'add-role-to-group', 'view', 'developers')
                            } 
                        }
                    }
                }
            }
        }
        stage('Set Project Resource Quotas') {
            steps {
                script {
                    // Uncomment to get lots of debugging output
                    //openshift.logLevel(1)
                    openshift.withCluster() {
                        // Step to create a Resource Quota for Production Environment
                        openshift.withProject("${PRJPRO}"){
                            echo('Creating resourceQuota for Production environement')
                            openshift.raw('create', 'resourcequota', '"'+"${PRJPRO}"+'-quota"', '"' + "--hard=limits.cpu=1,limits.memory=1Gi"+ '"')
                        }
                        // Step to create a Resource Quota for Development Environment
                        openshift.withProject("${PRJDEV}"){
                            echo('Creating resourceQuota for Production environement')
                            openshift.raw('create', 'resourcequota', '"'+"${PRJDEV}"+'-quota"', '"' + "--hard=limits.cpu=1,limits.memory=1Gi"+ '"')
                        } 
                    }
                }
            }
        }
    }
}