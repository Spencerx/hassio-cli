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
        echo 'Building....'
      }
      stage('Deploy') {
        echo 'Deploying....'
      }
    }
}
