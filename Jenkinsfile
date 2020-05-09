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
					'''
                }
            }
        }

		stage('Create kubernetes cluster') {
			steps {
				withAWS(region:'us-west-2', credentials:'ecr_credentials') {
					sh '''
					echo SERVICE_ROLE=${SERVICE_ROLE}
					echo SECURITY_GROUP=${SECURITY_GROUP}
					echo SUBNET_IDS=${SUBNET_IDS}

					aws eks create-cluster --name eksworkshop \
					--role-arn "${SERVICE_ROLE}" \
					--resources-vpc-config subnetIds="${SUBNET_IDS}",securityGroupIds="${SECURITY_GROUP}"

					aws eks describe-cluster --name "eksworkshop" --query cluster.status --output text

					'''
				}
			}
		}

	}
}