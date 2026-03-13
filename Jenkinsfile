pipeline {
    agent any

    environment {
        UIPATH_ORCH_URL = "https://rpa.local"
        UIPATH_TENANT = "Dev1"
        UIPATH_FOLDER_DEV = "Shared"
        UIPATH_FOLDER_PROD = "RPA_Automation"
        UIPATH_CLIENT_ID = "5a544aa0-77fe-4d8e-ab96-0faa3d3ec786"
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

        stage('Deploy to UAT') {
            steps {
                echo "Deploying to UAT..."

                UiPathDeploy(
    orchestratorAddress: "${UIPATH_ORCH_URL}",
    orchestratorTenant: "${UIPATH_TENANT}",
    folderName: "${UIPATH_FOLDER_DEV}",
    packagePath: "output/*.nupkg",
    entryPointPaths: "Main.xaml",
    environments: "",
    traceLevel: "Information",
    createProcess: true,

    credentials: [$class: 'UserPassAuthenticationEntry',
    credentialsId: 'f8b5f6cc-20b5-4d9c-baf0-cfce9903ddc5'
]
)
)
            }
        }

        stage('Deploy to Production') {
            steps {
                echo "Deploying to Production..."

                UiPathDeploy(
                    orchestratorAddress: "${UIPATH_ORCH_URL}",
                    orchestratorTenant: "${UIPATH_TENANT}",
                    folderName: "${UIPATH_FOLDER_PROD}",
                    packagePath: "output/*.nupkg",

                    entryPointPaths: ["Main.xaml"],
                    environments: "",
                    traceLevel: "Information",
                    createProcess: true,

                    credentials: Token(
                        accountName: "",
                        clientId: "${UIPATH_CLIENT_ID}",
                        credentialsId: "APIUserKey"
                    )
                )
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully"
        }

        failure {
            echo "Pipeline failed"
        }

        always {
            cleanWs()
        }
    }
}