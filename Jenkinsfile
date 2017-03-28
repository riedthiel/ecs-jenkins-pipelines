node {
  stage 'Checkout'
    checkout scm

  stage 'Docker  configuration'
    sh 'aws ecr get-login --region us-east-1 | xargs xargs'

  stage 'Build New Docker Image'
    docker.withRegistry('558201170204.dkr.ecr.us-east-1.amazonaws.com') {
      docker.build('mynginx').push(env.BUILD_NUMBER)
    }
  stage 'Push new container to Repo'
    sh '

  }
