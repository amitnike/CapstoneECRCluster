pipeline {
	agent any
	stages {

        stage('Create kubernetes cluster'){
            steps {
                withAWS(region:'us-west-2', credentials:'ecr_credentials'){
                    sh '''
                        aws cloudformation create-stack \
                        --stack-name "eksworkshop-vpc" \
                        --template-url "https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2018-08-30/amazon-eks-vpc-sample.yaml" \

                    '''

                    sh '''
                        until [[ `aws cloudformation describe-stacks --stack-name "eksworkshop-vpc" --query "Stacks[0].[StackStatus]" --output text` == "CREATE_COMPLETE" ]]; 
                        do  echo "The stack is NOT in a state of CREATE_COMPLETE at `date`";   
                        sleep 30; 
                        done && 
                        echo "The Stack is built at `date` - Please proceed"

                    '''
                }
            }
        }

	}
}