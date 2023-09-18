pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checkout the Git repository
                checkout scm
            }
        }
        stage('Compile and Run') {
            steps {
                script {
                    def folderPath = env.BRANCH_NAME.toLowerCase()
                    def compilationStatus = [:]

                    // Function to run a command for each file in a folder
                    def runCommandsForFilesInFolder = { command ->
                        def files = findFiles(glob: "${folderPath}/*.*")

                        files.each { file ->
                            def fileName = file.name
                            if (fileExists("${folderPath}/${fileName}")) {
                                def result = bat(script: "${command} ${folderPath}\\${fileName}", returnStatus: true)
                                echo "compiled successfully"
                                compilationStatus[fileName] = result
                            }
                        }
                    }

                    // C++ Programs
                    if (folderPath == 'cpp') {
                        runCommandsForFilesInFolder("g++ -o")
                    }
                    
                    // Java Programs
                    if (folderPath == 'java') {
                        runCommandsForFilesInFolder("javac -d")
                    }

                    // Python Programs
                    if (folderPath == 'python') {
                        runCommandsForFilesInFolder("python")
                    }

                    // Print compilation status
                    echo "Compilation Status:"
                    compilationStatus.each { fileName, status ->
                        echo "${fileName}: ${status == 0 ? 'Yes' : 'No'}"
                    }
                }
            }
        }
    }
}
