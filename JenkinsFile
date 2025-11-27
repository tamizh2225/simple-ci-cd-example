pipeline {
  agent any

  environment {
    DEPLOY_DIR = 'C:\\xampp\\htdocs\\myapp'   // change folder name as needed
  }

  stages {
    stage('Checkout') {
      steps {
        // checkout the Jenkinsfile and repo
        checkout scm
        echo "Checked out ${env.GIT_URL ?: 'repo'}"
      }
    }

    stage('Build (noop)') {
      steps {
        // For a simple static site there may be no build step.
        // You can add steps like npm install/build here if needed later.
        echo "No build step for static html. Workspace: ${env.WORKSPACE}"
      }
    }

    stage('Deploy to XAMPP') {
      steps {
        // Create deploy dir if missing, then copy workspace -> deploy dir
        // Exclude .git and Jenkinsfile
        bat """
          if not exist "${DEPLOY_DIR}" mkdir "${DEPLOY_DIR}"
          REM robocopy copies files and directories. /MIR mirrors; /XF excludes files; /XD excludes directories
          robocopy "%WORKSPACE%" "${DEPLOY_DIR}" /MIR /XF Jenkinsfile /XF .gitignore /XD .git
          REM robocopy returns special codes; for CI we ignore codes < 8 as successful
          IF %ERRORLEVEL% GEQ 8 EXIT /B %ERRORLEVEL%
        """
      }
    }
  }

  post {
    success {
      echo "Deployment succeeded. Visit http://localhost/myapp/"
    }
    failure {
      echo "Pipeline failed. Check console output."
    }
  }
}
