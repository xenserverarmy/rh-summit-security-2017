node( 'maven' ) {

    stage('checkout'){
        checkout scm
    }

    stage('trigger build'){
        login()
        sh """
            mkdir binary
            mv Dockerfile binary/.
            oc start-build base-layer-java --from-dir=binary
        """
    }










}


def login() {
    sh """
       set +x
       oc login --certificate-authority=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt --token=\$(cat /var/run/secrets/kubernetes.io/serviceaccount/token) https://kubernetes.default.svc.cluster.local >/dev/null 2>&1 || echo 'OpenShift login failed'
       """
}






