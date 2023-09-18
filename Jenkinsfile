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
                                def result = sh(script: "${command} ${folderPath}/${fileName}", returnStatus: true)
                                compilationStatus[fileName] = result
                            }
                        }
                    }

                    // Determine if new files have been added
                    def changeLog = currentBuild.rawBuild.getChangeSets().getItems()
                    def newFilesAdded = false

                    changeLog.each { entry ->
                        if (entry.getAffectedPaths().find { it.startsWith(folderPath + "/") }) {
                            newFilesAdded = true
                        }
                    }

                    // Only run the pipeline if new files have been added or existing files modified
                    if (newFilesAdded) {
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
                            echo "${fileName}: ${status == 0 ? 'Success' : 'Failure'}"
                        }
                    } else {
                        echo "No new files or changes detected in ${folderPath}. Skipping pipeline execution."
                    }
                }
            }
        }
    }
@NonCPS
List<String> getChangedFiles(String directory) {
    def changedFiles = []
    for (changeLogSet in currentBuild.changeSets) {
        for (entry in changeLogSet.getItems()) {
            for (path in entry.affectedPaths) {
                if (path.startsWith(directory)) {
                    changedFiles.add(path)
                }
            }
        }
    }
    return changedFiles
}
