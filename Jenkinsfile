pipeline {
	agent any
	stages {

        stage('Create VPC'){
            steps {
                withAWS(region:'us-west-2', credentials:'ecr_credentials'){
                    sh '''
                        aws cloudformation create-stack \
                        --stack-name "eksworkshop-vpc" \
                        --template-url "https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-08-30/amazon-eks-vpc-sample.yaml" \
					
					sleep 120;

					export SERVICE_ROLE=$(aws iam get-role --role-name "AWSServiceRoleForAmazonEKS" --query Role.Arn --output text)
					echo $SERVICE_ROLE > SERVICE_ROLE.txt

					export SECURITY_GROUP=$(aws cloudformation describe-stacks --stack-name "eksworkshop-vpc" --query "Stacks[0].Outputs[?OutputKey=='SecurityGroups'].OutputValue" --output text)
					echo $SECURITY_GROUP > SECURITY_GROUP.txt

					export SUBNET_IDS=$( aws cloudformation describe-stacks --stack-name "eksworkshop-vpc" --query "Stacks[0].Outputs[?OutputKey=='SubnetIds'].OutputValue" --output text)
					echo $SUBNET_IDS > SUBNET_IDS.txt

					'''
                }
            }
        }

		stage('Create kubernetes cluster') {
			steps {
				withAWS(region:'us-west-2', credentials:'ecr_credentials') {
					sh '''
					

					echo SERVICE_ROLE=cat SERVICE_ROLE.txt
					echo SECURITY_GROUP=cat SECURITY_GROUP.txt
					echo SUBNET_IDS=cat SUBNET_IDS.txt

					

					aws eks --region us-west-2 create-cluster \
					--name eksworkshop \
					--role-arn "${SERVICE_ROLE}" \
					--resources-vpc-config subnetIds="${SUBNET_IDS}",securityGroupIds="${SECURITY_GROUP}"


					aws eks describe-cluster --name "eksworkshop" --query cluster.status --output text

					sleep 300;

					'''
				}
			}
		}

	}
}