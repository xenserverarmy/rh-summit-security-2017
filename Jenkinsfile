node( 'maven' ) {

    stage('checkout'){
        checkout scm
    }

    stage('trigger build'){
        login()
        sh """
            mkdir binary
            mv Dockerfile binary/.
            oc start-build base-layer-java --from-dir=binary --follow=true
        """
    }

   stage('wait for OpenSCAP scan results'){
       login()
       sh """
           IMAGE_NAME=\$(oc get istag java:latest --template='{{ .image.metadata.name }}')
           COUNTER=0
           DELAY=10
           MAX_COUNTER=30
           echo "waiting for openscap scan results for java:latest"
           set +e
           while [ \$COUNTER -lt \$MAX_COUNTER ]
           do
             echo "checking image"
             oc get image \$IMAGE_NAME --template="{{.metadata.annotations}}" | grep security.manageiq.org/last-scan >/dev/null 2>&1
             if [ \$? -eq 0 ]; then
                 echo "Scan Completed"
                 break
             fi
             if [ \$COUNTER -lt \$MAX_COUNTER ]; then
                 echo -n "."
                 COUNTER=\$(( \$COUNTER + 1 ))
             fi
             if [ \$COUNTER -eq \$MAX_COUNTER ]; then
                  echo "Max time checking for scan completed.  Please confirm cloudforms is up and try again"
                  exit 1
             fi
             sleep \$DELAY
            done
           set -e
       """

   }










}


def login() {
    sh """
       set +x
       oc login --certificate-authority=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt --token=\$(cat /var/run/secrets/kubernetes.io/serviceaccount/token) https://kubernetes.default.svc.cluster.local >/dev/null 2>&1 || echo 'OpenShift login failed'
       """
}






