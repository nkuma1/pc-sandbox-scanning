node {
   stage('Preparation') {
       // for display purposes
       echo "Preparing"
   }

   stage('Build') {
       // Build an image for scanning
       sh 'echo "FROM nginx" > Dockerfile'
       sh 'echo "RUN mkdir -p /tmp/test/dir" >> Dockerfile'
       sh 'docker build --no-cache -t dev/nginx:test .'
   }

  stage('Scan') {
    prismaCloudScanImage ca: '/certs/client/ca.pem', 
    cert: '/certs/client/cert.pem', 
    compliancePolicy: 'critical', 
    containerized: false, 
    dockerAddress: 'https://docker:2376', 
    gracePeriodDays: 0, 
    ignoreImageBuildTime: false, 
    image: 'dev/nginx:test', 
    key: '/certs/client/key.pem', 
    logLevel: 'true', 
    policy: 'critical', 
    requirePackageUpdate: false, 
    timeout: 10
    }


    stage('Scan image with twistcli') {
        //withCredentials([usernamePassword(credentialsId: 'twistlock_creds', passwordVariable: 'TL_PASS', usernameVariable: 'TL_USER')]) {
            sh 'curl -k -u ${PRISMA_ACCESS_KEY}:${PRISMA_SECRET_KEY} --output twistcli ${PCC_CONSOLE_URL}/api/v1/util/twistcli'
            sh 'chmod a+x ./twistcli'
            sh "./twistcli images scan --u ${PRISMA_ACCESS_KEY} --p ${PRISMA_SECRET_KEY} --address ${PCC_CONSOLE_URL} --docker-address https://docker:2376 --docker-tlscacert /certs/client/ca.pem --docker-tlscert /certs/client/cert.pem --docker-tlskey /certs/client/key.pem --details dev/nginx:test"
    }

  
  stage('Publish') {
     prismaCloudPublish ca: '/certs/client/ca.pem',
        cert: '/certs/client/cert.pem',
        dockerAddress: 'tcp://docker:2376',
        ignoreImageBuildTime: true,
        image: 'dev/nginx:test',
        key: '/certs/client/key.pem',
        logLevel: 'true',
        timeout: 10

        }
}

