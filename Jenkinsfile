def bucket = 'oraculi-terraform-states7'
def functionName = 'serverless-demo'
def region = 'ap-south-1'
def accesskey = 'AKIA4Z5W7B6H2RONA7NN'
def secretkey = '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'
def environments = ['develop': 'sandbox', 'preprod': 'staging', 'master': 'production']
def s3Uri = 's3://oraculi-terraform-states7/serverless-demo/sandbox/'

node('workers'){
    stage('Checkout'){
        checkout scm
    }

    stage('Test'){
        sh "echo 'running tests'"
    }

    stage('Build'){
        sh """
            docker build -f Dockerfile.build -t serverless-app .
            containerName=\$(docker run -d serverless-app)
            docker cp \$containerName:/go/src/github.com/maheshbics/lambda-serverless/main main
            docker rm -f \$containerName
            zip -r deployment.zip main
        """ 
    }

    stage('Push'){
        if(env.BRANCH_NAME == 'develop' || env.BRANCH_NAME == 'preprod' || env.BRANCH_NAME == 'master'){
            // sh "aws s3 cp deployment.zip s3://${bucket}/${functionName}/${environments[env.BRANCH_NAME]}/"
            sh "aws configure set region $region" 
            sh "aws configure set aws_access_key_id $accesskey"  
            sh "aws configure set aws_secret_access_key $secretkey"
            sh "aws s3 cp deployment.zip $s3Uri"
        }
    }
}


//     stage('Deploy'){
//         if(env.BRANCH_NAME == 'develop' || env.BRANCH_NAME == 'preprod' || env.BRANCH_NAME == 'master'){
//             sh """
//                 aws lambda update-function-code --function-name ${functionName} --s3-bucket ${bucket} --s3-key ${functionName}/${environments[env.BRANCH_NAME]}/deployment.zip --region ${region}
//                 version=\$(aws lambda get-alias --function-name ${functionName} --name ${environments[env.BRANCH_NAME]} --region ${region} | jq -r '.FunctionVersion')
//                 new_envvars=\$(aws lambda get-function-configuration --function-name ${functionName} --region ${region} --qualifier \$version --query "Environment.Variables") 
//                  aws lambda update-function-configuration --function-name ${functionName} --environment "{ \\"Variables\\": \$new_envvars }" --region ${region}
//             """
            
//             sleep(3)
            
//             sh """
//                 publishedVersion=\$(aws lambda publish-version --function-name ${functionName} --description ${environments[env.BRANCH_NAME]} --region ${region} | jq -r '.Version')
//                 aws lambda update-alias --function-name ${functionName} --name ${environments[env.BRANCH_NAME]} --function-version \$publishedVersion --region ${region}
//             """
//         }
//     }

// }