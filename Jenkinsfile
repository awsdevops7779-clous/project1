pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install CodeQL') {
      steps {
        sh '''
          echo "Downloading CodeQL CLI..."
          curl -L -o codeql.zip https://github.com/github/codeql-cli-binaries/releases/latest/download/codeql-linux64.zip
          unzip -q codeql.zip -d codeql
          export PATH=$PWD/codeql:$PATH
          codeql version
        '''
      }
    }

    stage('Create CodeQL Database') {
      steps {
        sh '''
          export PATH=$PWD/codeql:$PATH
          # Change language as per your project (javascript, python, java, etc.)
          codeql database create codeql-db --language=javascript --source-root=.
        '''
      }
    }

    stage('Run CodeQL Analysis') {
      steps {
        sh '''
          export PATH=$PWD/codeql:$PATH
          # Run built-in security queries
          codeql database analyze codeql-db --format=sarif-latest --output=codeql-results.sarif javascript-security-and-quality.qls
        '''
      }
      post {
        always {
          archiveArtifacts artifacts: 'codeql-results.sarif', allowEmptyArchive: true
        }
      }
    }

    stage('Check Findings') {
      steps {
        script {
          def issues = sh(script: "jq '.runs[].results | length' codeql-results.sarif | awk '{s+=\$1} END {print s}' || echo 0", returnStdout: true).trim()
          echo "Total issues found: ${issues}"
          if (issues.toInteger() > 0) {
            error("CodeQL found ${issues} issue(s). Please review the report.")
          } else {
            echo "✅ No security issues found!"
          }
        }
      }
    }
  }
}
