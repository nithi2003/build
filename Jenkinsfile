pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                // Checkout the Git repository
                checkout scm
            }
        }
        
        stage('Build and Test') {
            matrix {
                axes {
                    axis {
                        name 'LANGUAGE'
                        values 'cpp', 'java', 'python'
                    }
                }
                stages {
                    stage('Build ${LANGUAGE}') {
                        when {
                            changeset "${LANGUAGE}/**"
                        }
                        steps {
                            script {
                                def languageDir = "${LANGUAGE}/"
                                def changedFiles = getChangedFiles(languageDir)
                                
                                if (changedFiles) {
                                    echo "${LANGUAGE} code changes detected in the following files:"
                                    echo changedFiles.join('\n')
                                    
                                    // Add build and test steps for the specific language here
                                    // Example:
                                    if (LANGUAGE == 'cpp') {
                                        for (file in changedFiles) {
                                            bat "g++ ${file} -o ${file}.out"
                                            bat "./${file}.out"
                                        }
                                    }
                                    if (LANGUAGE == 'java') {
                                        for (file in changedFiles) {
                                            def f = file.substring(5);
                                            bat "javac ${f}"
                                            def className = f.replaceAll('.java', '')
                                            bat "java ${className}"
                                        }
                                    }
                                    if (LANGUAGE == 'python') {
                                        for (file in changedFiles) {
                                            bat "python ${file}"
                                        }
                                    }
                                } else {
                                    echo "${LANGUAGE} code changes detected, but no specific files found."
                                }
                            }
                        }
                    }
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
