pipeline {
  agent any

  environment {
    DEPLOY_DIR = 'C:\\xampp\\htdocs\\myapp'
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
        echo "Workspace: ${env.WORKSPACE}"
      }
    }

    stage('Build (noop)') {
      steps {
        echo "No build step for static html."
      }
    }

    stage('Deploy to XAMPP') {
      steps {
        bat '''
        if not exist "C:\\xampp\\htdocs\\myapp" mkdir "C:\\xampp\\htdocs\\myapp"

        rem --- run robocopy ---
        robocopy "%WORKSPACE%" "C:\\xampp\\htdocs\\myapp" /MIR /XF Jenkinsfile /XF .gitignore /XD .git
        set RC=%ERRORLEVEL%
        echo Robocopy exit code: %RC%

        rem --- only fail if RC >= 8 ---
        if %RC% GEQ 8 (
          echo Robocopy FAILED with code %RC%
          exit /B %RC%
        )

        rem --- success for RC < 8 ---
        echo Robocopy succeeded or had non-fatal warnings (code %RC%)
        exit /B 0
        '''
      }
    }

  }

  post {
    success {
      echo "Deployment succeeded! Visit http://localhost/myapp/"
    }
    failure {
      echo "Deployment failed. Check console log."
    }
  }
}
