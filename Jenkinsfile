pipeline {
  agent any

  environment {
    // Change this if your XAMPP is installed elsewhere or you want a different folder
    DEPLOY_DIR = 'C:\\xampp\\htdocs\\myapp'
  }

  stages {
    stage('Checkout') {
      steps {
        // If using "Pipeline script from SCM" this checkout is optional, but harmless
        checkout scm
        echo "Workspace: ${env.WORKSPACE}"
      }
    }

    stage('Build (noop)') {
      steps {
        echo "No build step for static html. Workspace: ${env.WORKSPACE}"
      }
    }

    stage('Deploy to XAMPP') {
      steps {
        // Multiline bat block. It captures robocopy's exit code and exits 0 for non-fatal codes (<8).
        bat """
          if not exist "${DEPLOY_DIR}" mkdir "${DEPLOY_DIR}"
          REM Run robocopy and mirror workspace to deploy dir, excluding Jenkinsfile and .git
          robocopy "%WORKSPACE%" "${DEPLOY_DIR}" /MIR /XF Jenkinsfile /XF .gitignore /XD .git
          set RC=%ERRORLEVEL%
          echo Robocopy exit code: %RC%
          if %RC% GEQ 8 (
            echo Robocopy failed with code %RC%
            exit /B %RC%
          ) else (
            echo Robocopy completed with code %RC% (non-fatal). Exiting 0 for Jenkins.
            exit /B 0
          )
        """
      }
    }
  }

  post {
    success {
      echo "Deployment succeeded. Visit http://localhost/myapp/ (or adjust URL for your folder)"
    }
    failure {
      echo "Pipeline failed. Check console output."
    }
  }
}
