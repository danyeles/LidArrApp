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

    parameters {
        choice(name: 'ACTION', choices: ['Deploy', 'Update', 'Stop and Run'], description: 'Select action')
    }

    stages {
        stage('Pull Latest Image') {
            when {
                expression { ACTION == 'Update' }
            }
            steps {
                script {
                    sh "docker pull ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Check Existing Container') {
            steps {
                script {
                    env.CONTAINER_EXISTS = sh(
                        script: "docker ps -aq -f name=${CONTAINER_NAME}",
                        returnStdout: true
                    ).trim()
                }
            }
        }

        stage('Perform Selected Action') {
            steps {
                script {
                    if (ACTION == 'Update' && CONTAINER_EXISTS) {
                        echo "Updating container ${CONTAINER_NAME}"
                        
                        sh """
                        docker stop ${CONTAINER_NAME}
                        docker rm ${CONTAINER_NAME}
                        docker run -d \
                            --restart always \
                            --name ${CONTAINER_NAME} \
                            -p 8686:8686 \
                            -e PUID=${PUID} \
                            -e PGID=${PGID} \
                            -e TZ=${TZ} \
                            -v ${CONFIG_PATH}:/config \
                            -v /mnt/Media:/Media \
                            -v /mnt/Media/Downloads:/downloads \
                            -v /mnt/Media/Music:/music \
                            ${DOCKER_IMAGE}
                        """
                    } else if (ACTION == 'Stop and Run' && CONTAINER_EXISTS) {
                        echo "Restarting container ${CONTAINER_NAME} without updating image"
                        
                        sh """
                        docker stop ${CONTAINER_NAME}
                        docker run -d \
                            --restart always \
                            --name ${CONTAINER_NAME} \
                            -p 8686:8686 \
                            -e PUID=${PUID} \
                            -e PGID=${PGID} \
                            -e TZ=${TZ} \
                            -v ${CONFIG_PATH}:/config \
                            -v /mnt/Media:/Media \
                            -v /mnt/Media/Downloads:/downloads \
                            -v /mnt/Media/Music:/music \
                            ${DOCKER_IMAGE}
                        """
                    } else {
                        echo "Deploying new container ${CONTAINER_NAME}"
                        
                        sh """
                        docker run -d \
                            --restart always \
                            --name ${CONTAINER_NAME} \
                            -p 8686:8686 \
                            -e PUID=${PUID} \
                            -e PGID=${PGID} \
                            -e TZ=${TZ} \
                            -v ${CONFIG_PATH}:/config \
                            -v /mnt/Media:/Media \
                            -v /mnt/Media/Downloads:/downloads \
                            -v /mnt/Media/Music:/music \
                            ${DOCKER_IMAGE}
                        """
                    }
                }
            }
        }
    }
}
