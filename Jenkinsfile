pipeline {
    agent any

    environment {
        S3_BUCKET_NAME           = 'frontend-bucket-2026' 
        CLOUDFRONT_DISTRIBUTION_ID = 'E1UR1354POQJKR'                     
        AWS_REGION               = 'ap-south-1'                       
        AWS_CREDENTIALS          = 'AWS'          
    }

    stages {

        stage('clone') {
            steps {
                echo "Cloning project"
                git branch: 'main', url: 'https://github.com/prasath-2002/Ecom.git'
            }
        }

        stage('Build React App') {
            steps {
                echo "Installing dependencies and building the project"
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Deploy to S3') {
            steps {
                echo "Deploying build files to S3 bucket: ${env.S3_BUCKET_NAME}"
                withAWS(credentials: env.AWS_CREDENTIALS, region: env.AWS_REGION) {
                    sh "aws s3 sync dist/ s3://${env.S3_BUCKET_NAME} --delete"
                }
            }
        }

        stage('Invalidate CloudFront Cache') {
            steps {
                echo "Invalidating CloudFront cache for distribution: ${env.CLOUDFRONT_DISTRIBUTION_ID}"
                withAWS(credentials: env.AWS_CREDENTIALS, region: env.AWS_REGION) {
                    sh "aws cloudfront create-invalidation --distribution-id ${env.CLOUDFRONT_DISTRIBUTION_ID} --paths '/*'"
                }
            }
        }
    }
}