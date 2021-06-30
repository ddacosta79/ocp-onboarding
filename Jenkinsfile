pipeline {
    environment {
        DOMAIN='apps.ocpddc1.os.fyre.ibm.com'
        PRJ="onboarding"
        devworkloadselectorvalue="env=dev"
        proworkloadselectorvalue="env=pro"
    }
    agent {
      node {
        label 'nodejs'
      }
    }
    stages {
        stage('set env') {
            steps {
                script {
                    echo("working on Project ${PRJ}")
                    PRJPRO="${PRJ}-pro"
                    PRJDEV="${PRJ}-dev"
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
                        echo("working to create project ${PRJDEV}")
                        def project= "${PRJDEV}"
                        if (!prjexist) {
                            echo("Creating project ${PRJDEV}")
                            //openshift.newProject("${PRJDEV}", '"--display-name='${env.PRJ}'"Development Project"', '"--description='${env.PRJ}'"_Development_Project"')
                            openshift.newProject("${PRJDEV}", '"' + "--display-name=${env.PRJ} Development Project" + '"', '"' + "--description=${env.PRJ} Development Project"+ '"')
                            openshift.raw('label', 'namespace', '"'+"${PRJDEV}"+'"', "${env.devworkloadselectorvalue} app=${PRJDEV}")
                            openshift.raw('annotate', 'namespace', '"'+"${PRJDEV}"+'"', "openshift.io/node-selector=${env.devworkloadselectorvalue}")
                            openshift.withProject("${PRJDEV}"){
                                echo('Granting access to the project')
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
                            openshift.newProject("${PRJPRO}", ' " ' + "--display-name=${env.PRJ} Production Project" + ' " ', '"' +"--description=${env.PRJ} Production Project"+ '"')
                            openshift.raw('label', 'namespace', '"'+"${PRJPRO}"+'"', "${env.proworkloadselectorvalue} app=${PRJPRO}")
                            openshift.raw('annotate', 'namespace', '"'+"${PRJPRO}"+'"', "openshift.io/node-selector=${env.proworkloadselectorvalue}")
                            //openshift.newProject("${PRJPRO}", "--display-name=${env.PRJ}_Production_Project", "--description=${env.PRJ}_Production_Project")
                            //openshift.raw('label', 'project', '\\"${PRJPRO}\\"', "${env.proworkloadselectorvalue}")
                            openshift.withProject("${PRJPRO}"){
                                echo('Granting access to the project')
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
    }
}