application = "hello-nodejs"
project = "summer69"

openshiftApiURL = "https://api.n1ocp-pclus-05.india.airtel.itm"
openshiftAuthToken = null

contextDir = "/"
gitBranch = "master"
gitURL = "https://github.com/vidhyachari/openshift-jenkins-pipeline"
openshiftTemplatePath = "openshift-templates/nodejs.json"


node('n1ocp-pclus-05-slave'){

   getJenkinsSACredentials(){
    
    sh """
        oc login --token=${openshiftAuthToken} ${openshiftApiURL} --insecure-skip-tls-verify=True
        oc get pod
        oc get svc
    """
   }
    //git changelog: false, poll: false, url: 'https://github.com/vidhyachari/openshift-hello-nodejs'
    // Checkout the source code
    checkout scm
    
    sh """
      oc process -f ${openshiftTemplatePath} \
        NAME=${application} \
        SOURCE_REPOSITORY_REF=${gitBranch} \
        SOURCE_REPOSITORY_URL=${gitURL} \
        CONTEXT_DIR="/" \
        -n ${project} | oc apply -f - -n ${project}
      """

    //Start openshift build
    sh """
      oc get bc ${application} -n ${project}

      # Cancel any currently running builds
      oc cancel-build bc/${application} -n ${project}

      # Start build and follow logs
      oc start-build ${application} --follow -n ${project}
    """
   
   //Start Deployment
   sh """
      oc rollout cancel dc/${application} -n ${project}
      oc rollout latest dc/${application} -n ${project}
   """
   
}//node

def getJenkinsSACredentials(){
  withCredentials([string(credentialsId: 'n1ocp-pclus-05', variable: 'SECRET')]) {
    openshiftAuthToken = SECRET
  }

}
