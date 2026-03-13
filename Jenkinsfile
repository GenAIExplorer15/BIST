pipeline {
    agent any

    environment {
        ORCH_URL = "https://rpa.local"
        ORCH_TENANT = "Dev1"
        ORCH_FOLDER = "Shared"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Package') {
            steps {

                echo "Packaging UiPath Project..."

                UiPathPack(
                    version: AutoVersion(),
                    projectJsonPath: "project.json",
                    outputPath: "output",
                    traceLevel: "Information"
                )
            }
        }

        stage('Deploy') {
            steps {

                echo "Deploying to Orchestrator..."

                UiPathDeploy(
                    orchestratorAddress: "${ORCH_URL}",
                    orchestratorTenant: "${ORCH_TENANT}",
                    folderName: "${ORCH_FOLDER}",
                    packagePath: "output",
                    entryPointPaths: "Main.xaml",
                    environments: "",
                    traceLevel: "Information",
                    createProcess: true,
                    credentials: [$class: 'UserPassAuthenticationEntry',
                        credentialsId: 'f8b5f6cc-20b5-4d9c-baf0-cfce9903ddc5'
                    ]
                )
            }
        }

    }

    post {
        always {
            cleanWs()
        }
    }
}