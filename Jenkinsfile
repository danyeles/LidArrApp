pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'lscr.io/linuxserver/lidarr:latest'
        CONTAINER_NAME = 'lidarr'
        PUID = '1000'
        PGID = '1000'
        TZ = 'America/Monterrey'
        WEBUI_PORTS = '8686/tcp,8686/udp'
        CONFIG_PATH = '/home/docker/lidarr/config'
    }

    stages {
        stage('Deploy Docker Image') {
            steps {
                script {
                    sh """                      
                    docker run -d \
                        --restart always \
                        --name ${CONTAINER_NAME} \
                        -p 8686:8686 \
                        -e PUID=${PUID} \
                        -e PGID=${PGID} \
                        -e TZ=${TZ} \
                        -v ${CONFIG_PATH}:/config \
                        -v /media/Media:/Media \
                        -v /media/Media/Downloads:/downloads \
                        -v /media/Media/Music:/music \
                        ${DOCKER_IMAGE}
                    """
                }
            }
        }
    }
}
