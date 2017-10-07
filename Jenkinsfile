#!groovy
puppet.credentials 'pe-access-token'
node('tse-control-repo') {
  sshagent (credentials: ['jenkins-seteam-ssh']) {
    withEnv(['PATH+EXTRA=/usr/local/bin']) {
      checkout scm

      stage('Setup'){
        ansiColor('xterm') {
          sh(script: '''
            export PATH=$PATH:$HOME/.rbenv/bin
            rbenv global 2.3.1
            eval "$(rbenv init -)"
            bundle install
          ''')
        }
      }

      stage('Lint Control Repo'){
        ansiColor('xterm') {
          sh(script: '''
            export PATH=$PATH:$HOME/.rbenv/bin
            rbenv global 2.3.1
            eval "$(rbenv init -)"
            bundle exec rake lint
          ''')
        }
      }

      stage('Syntax Check Control Repo'){
        ansiColor('xterm') {
          sh(script: '''
            export PATH=$PATH:$HOME/.rbenv/bin
            rbenv global 2.3.1
            eval "$(rbenv init -)"
            bundle exec rake syntax --verbose
          ''')
        }
      }

      stage('Validate Puppetfile in Control Repo'){
        ansiColor('xterm') {
          sh(script: '''
            export PATH=$PATH:$HOME/.rbenv/bin
            rbenv global 2.3.1
            eval "$(rbenv init -)"
            bundle exec rake r10k:syntax
          ''')
        }
      }

      stage('Validate Tests Exist'){
        ansiColor('xterm') {
          sh(script: '''
            export PATH=$PATH:$HOME/.rbenv/bin
            rbenv global 2.3.1
            eval "$(rbenv init -)"
            bundle exec rake check_for_spec_tests
          ''')
        }
      }
    }
  }
}

stage('Run Spec Tests') {
  sleep 10
//  node('tse-slave-linux') {
//    sshagent (credentials: ['jenkins-seteam-ssh']) {
//      checkout scm
//      withEnv(['PATH+EXTRA=/usr/local/bin']) {
//        ansiColor('xterm') {
//          sh(script: '''
//            export PATH=$PATH:$HOME/.rbenv/bin:$HOME/.rbenv/shims
//            echo $PATH
//            rbenv global 2.3.1
//            gem install bundle
//            bundle install
//            bundle exec rake spec
//          ''')
//        }
//      }
//    }
//  }
}

stage("Promote To Environment"){
  if (env.BRANCH_NAME == 'production'){
    puppet.codeDeploy env.BRANCH_NAME
  }
}
