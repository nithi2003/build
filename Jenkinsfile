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
            matrix {
                axes {
                    axis {
                        name 'LANG'
                        values 'python', 'cpp', 'java'
                    }
                }
                stages {
                    stage('Build') {
                        when {
                            changeset "**/${env.LANG}/**"
                        }
                        steps {
                            script {
                                def folderPath = env.LANG
                                def compilationStatus = [:]

                                // Function to run a command for each file in a folder
                                def runCommandsForFilesInFolder = { command ->
                                    def files = findFiles(glob: "${folderPath}/*.*")

                                    for (def file : files) {
                                        def fileName = file.name
                                        if (fileExists("${folderPath}/${fileName}")) {
                                            def result = bat(script: "${command} ${folderPath}\\${fileName}", returnStatus: true, returnStdout: true)
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
                                    runCommandsForFilesInFolder("javac -d .")
                                }

                                // Python Programs
                                if (folderPath == 'python') {
                                    runCommandsForFilesInFolder("python")
                                }

                                // Print compilation status
                                echo "Compilation Status for ${folderPath}:"
                                for (def fileName : compilationStatus.keySet()) {
                                    def status = compilationStatus[fileName]
                                    if (status == 0) {
                                        echo "${fileName}: Yes"
                                    } else {
                                        echo "${fileName}: No"
                                        currentBuild.result = 'FAILURE' // Mark the build as failed
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}
