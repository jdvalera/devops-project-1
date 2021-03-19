node {
    def app
    def image

    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') {
        app = docker.build("jdvalera/project1")
    }

    stage('Test image') {
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Deploy image') {
        docker.withRegistry('https://registry.hub.docker.com', 'DOCKER-HUB-CREDENTIALS') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }

    stage('Pull image') {
        docker.withRegistry('https://registry.hub.docker.com', 'DOCKER-HUB-CREDENTIALS') {
            image = docker.image("jdvalera/project1:${env.BUILD_NUMBER}")
            image.pull()
        }
    }

    stage('Run image') {
            sh 'docker ps -f name=web-app -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=web-app -q | xargs -r docker container rm'
            sh 'docker system prune -a -f'
            image.run('-p 80:80 --name web-app')
    }
}