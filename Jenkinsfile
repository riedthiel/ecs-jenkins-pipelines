node {
  stage 'Checkout'
    checkout scm

  stage 'Docker  configuration'
    echo User: $USER
    sh 'aws ecr get-login --region us-east-1 | xargs xargs'
  }
