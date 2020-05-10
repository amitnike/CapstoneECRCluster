#!/usr/bin/env groovy


pipeline {

	environment {
        SERVICE_ROLE = ''
		SECURITY_GROUP = ''
		SUBNET_IDS = ''
    }

	agent any
	stages {

        stage('Create VPC'){
            steps {
                withAWS(region:'us-west-2', credentials:'ecr_credentials'){
                    sh '''
                        aws cloudformation create-stack \
                        --stack-name "eksworkshop-vpc" \
                        --template-url "https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-08-30/amazon-eks-vpc-sample.yaml" \

					echo "Current agent  info: ${env.SERVICE_ROLE}"		
					sleep 120;


					env.SERVICE_ROLE=$(aws iam get-role --role-name "AWSServiceRoleForAmazonEKS" --query Role.Arn --output text)
					
					env.SECURITY_GROUP=$(aws cloudformation describe-stacks --stack-name "eksworkshop-vpc" --query "Stacks[0].Outputs[?OutputKey=='SecurityGroups'].OutputValue" --output text)
					
					env.SUBNET_IDS=$( aws cloudformation describe-stacks --stack-name "eksworkshop-vpc" --query "Stacks[0].Outputs[?OutputKey=='SubnetIds'].OutputValue" --output text)
					

					'''
                }
            }
        }

		stage('Create kubernetes cluster') {
			steps {
				withAWS(region:'us-west-2', credentials:'ecr_credentials') {
					sh '''
					
					echo ${env.SERVICE_ROLE}
					echo ${env.SECURITY_GROUP}
					echo ${env.SUBNET_IDS}
					

					aws eks --region us-west-2 create-cluster \
					--name eksworkshop \
					--role-arn "${env.SERVICE_ROLE}" \
					--resources-vpc-config subnetIds="${env.SUBNET_IDS}",securityGroupIds="${env.SECURITY_GROUP}"


					aws eks describe-cluster --name "eksworkshop" --query cluster.status --output text

					sleep 300;

					'''
				}
			}
		}

	}
}