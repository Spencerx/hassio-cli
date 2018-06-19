node {
   withEnv(["GOPATH=${WORKSPACE}"]) {
      stage('Checkout') {
        dir('src/github.com/home-assistant/hassio-cli') {
          checkout scm
        }
      }   
      stage('Get Deps') {
        docker
          .image('golang:1.10')
          .inside { c -> sh 'go get github.com/urfave/cli' }
      }
      stage('Build') {
        docker
          .image('golang:1.10')
          .inside { c -> sh 'cd $GOPATH/src/github.com/home-assistant/hassio-cli; CGO_ENABLED=0 GOARCH=386 go build -ldflags="-s -w" -o "hassio_i386"' }
      }
      stage('Test') {
        echo 'Testing....'

        docker.image('homeassistant/i386-hassio-supervisor')
        docker.image('homeassistant/i386-homeassistant')

        withEnv(["HADATA=${WORKSPACE}/tmp/hassioa", "UUID=63afb61b-e617-4329-9679-84ecce5c407a"]) {
          sh 'mkdir -p ${HADATA}'
          docker
            .image('homeassistant/i386-hassio-supervisor')
            .withRun('-v /var/run/docker.sock:/var/run/docker.sock \
              -v /var/run/dbus:/var/run/dbus \
              -v ${HADATA}:/data -e SUPERVISOR_SHARE=${HADATA} \
              -e SUPERVISOR_NAME=hassio_supervisor \
              -e HOMEASSISTANT_REPOSITORY=homeassistant/i386-homeassistant \
              -e MACHINE_ID=${UUID}') 
              { c ->
                docker.image('homeassistant/i386-hassio-supervisor').inside("--link ${c.id}:hassio") {
                    //sh 'while ! (curl -s http://hassio/supervisor/pings | grep ok); do sleep 1; done'
                }
          }
        }
      }
      stage('Deploy') {
        echo 'Deploying....'
      }
    }
}
