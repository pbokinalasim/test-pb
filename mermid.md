graph TD
    subgraph "Amazon Virtual Backend Cloud (VPC)"
        direction TD
        subgraph "Amazon Private Cloud"
            direction TD
            subgraph "Public Subnets"
                subgraph "Presentation Layer"
                    APIGateway[Amazon API Gateway]
                    REST{REST API Endpoints}
                    CognitoAuthorizer1[Cognito Authorizer]
                    CognitoPool(Amazon Cognito<br>User Pool)
                    
                    APIGateway --> REST
                    APIGateway --> CognitoAuthorizer1
                    CognitoPool -- Authorizer --> CognitoAuthorizer1
                    CognitoAuthorizer1 -- HTTPS/TLS --> LambdaFunctions
                end
            end
            
            subgraph "Private Subnets"
                subgraph "Application Layer"
                    LambdaFunctions[AWS Lambda Functions]
                    LambdaFunctions -- "User Management" --> CognitoPool
                    LambdaFunctions -- "CRUD Operations (Sites, Clinicians, Subjects, Sessions)" --> AuroraDB
                    LambdaFunctions -- "Sessions Logic" --> S3Bucket
                    LambdaFunctions -- "RBAC Enforcement" --> CognitoGroups
                end
                
                subgraph "Data Layer"
                    AuroraDB[Amazon Aurora for PostgreSQL]
                    S3Bucket[Amazon S3 Bucket]
                    AuroraDB -- Encrypted at Rest --> S3Bucket
                end
                
                subgraph "Private Subnets"
                    CognitoGroups[Amazon Cognito<br>Groups (Admin, Clinician, Researcher)]
                    CognitoPool -- MFA/OAUTH/SAML --> CognitoGroups
                end
            end
        end
    end

    subgraph "Security & Compliance"
        CloudTrail["AWS CloudTrail<br>/ CodeCommit (Source)"]
        CloudTrail -- "Audit Logging" --> LogsMetrics
        LogsMetrics(Logs Metrics)
    end
    
    subgraph "Infrastructure & Deployment"
        CodePipeline[AWS CodePipeline]
        CodeBuild[AWS CodeBuild]
        IAM["AWS IAM<br>Permissions & Roles"]
        Terraform(Terraform)
        
        CodePipeline -- "Build & Test" --> CodeBuild
        CodeBuild -- "IaC Deployment" --> IAM
        CodeBuild -- "IaC Deployment" --> Terraform
    end
