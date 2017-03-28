node {
  def stackname = 'Nginx-ECS'
  def s3-bucket = 'jw-ia-dev'
  stage 'Checkout'
    checkout scm

  stage 'Docker  configuration'
    sh 'aws ecr get-login --region us-east-1 | xargs xargs'

  stage 'Build New Docker Image'
    docker.withRegistry('https://558201170204.dkr.ecr.us-east-1.amazonaws.com') {
      docker.build('mynginx').push(env.BUILD_NUMBER)
    }

  stage 'Create/Update Infrastructure'
    sh 'aws s3 sync ./infra/ s3://${s3-bucket}/ --exclude ".*" '
    sh 'aws cloudformation create-stack --stack-name ${stackname} --template-url https://s3.amazonaws.com/${s3-bucket}/master.yaml --parameters ./infra/parameters/dev-parameters.json --capabilities CAPABILITY_NAMED_IAM  --region us-east-1  --disable-rollback'
  }
