String stackname = "Nginx-ECS"
node {
  stage 'Checkout'
    checkout scm

  stage 'Docker  configuration'
    sh 'aws ecr get-login --region us-east-1 | xargs xargs'

  stage 'Build New Docker Image'
    docker.withRegistry('https://558201170204.dkr.ecr.us-east-1.amazonaws.com') {
      docker.build('mynginx').push(env.BUILD_NUMBER)
    }

  stage 'Insert Build Number into Parameters'
    sh 'sed -i "s/001/$BUILD_NUMBER/g" ./infra/parameters/dev-parameters.json'

  stage 'Create/Update Infrastructure'
    String  s3bucket = "jw-ia-dev"
    sh 'aws s3 sync ./infra/ s3://jw-ia-dev/ --exclude ".*" '
    try {
      result = sh(returnStdout: true, script: "aws cloudformation describe-stacks --stack-name Nginx-ECS1 --region us-east-1 --query 'Stacks[*].StackStatus' --output text")
      sh 'aws cloudformation update-stack --stack-name Nginx-ECS1 --template-url https://s3.amazonaws.com/jw-ia-dev/master.yaml --parameters file://./infra/parameters/dev-parameters.json --capabilities CAPABILITY_NAMED_IAM  --region us-east-1 '
    } catch (all) {
      sh 'aws cloudformation create-stack --stack-name Nginx-ECS1 --template-url https://s3.amazonaws.com/jw-ia-dev/master.yaml --parameters file://./infra/parameters/dev-parameters.json --capabilities CAPABILITY_NAMED_IAM  --region us-east-1 --disable-rollback'
    }
    //if (result.equals("") || result.contains("does not exist")) {
    //  sh 'aws cloudformation create-stack --stack-name Nginx-ECS1 --template-url https://s3.amazonaws.com/jw-ia-dev/master.yaml --parameters file://./infra/parameters/dev-parameters.json --capabilities CAPABILITY_NAMED_IAM  --region us-east-1 --disable-rollback'
    //} else {
    //  sh 'aws cloudformation update-stack --stack-name Nginx-ECS1 --template-url https://s3.amazonaws.com/jw-ia-dev/master.yaml --parameters file://./infra/parameters/dev-parameters.json --capabilities CAPABILITY_NAMED_IAM  --region us-east-1 '
    //}
  stage 'Wait for Completion'
    result = sh(returnStdout: true, script: "aws cloudformation describe-stacks --stack-name Nginx-ECS1 --region us-east-1 --query 'Stacks[*].StackStatus' --output text")
    for (int i = 0; i < 1000; i++) {
      result = sh(returnStdout: true, script: 'aws cloudformation describe-stacks --stack-name Nginx-ECS1 --region us-east-1 --query "Stacks[*].StackStatus" --output text')
      if (result.contains("ERROR") || result.contains("ROLLBACK")) {
         error: "Error in Stack Build"
      } else if (result.contains('COMPLETE')) {
        break;
      }
      echo "Status ${result}"
      sleep: 5000
    }
}
