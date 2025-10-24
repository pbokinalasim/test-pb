```mermaid
graph TD
    subgraph "Amazon Virtual Bckend Cloud (VPC)"
        direction TD
        subgraph "Amazon Private Cloud"
            direction TD
            subgraph "Public Subnets"
                direction TD
                subgraph "Presentation Layer"
                    APIGateway[Amazon API Gateway]
                    REST[REST API Endpoints]
                    CognitoAuthorizer1[Cognito Authorizer]
                    CognitoPool(Amazon Cognito<br>User Pool)
                    APIGateway --> CognitoAuthorizer1
                    CognitoPool --> CognitoAuthorizer1
                    CognitoAuthorizer1 -->|HTTPS/TLS| LambdaFunctions
                end
            end
            
            subgraph "Private Subnets"
                direction TD
                subgraph "Application Layer"
                    LambdaFunctions[AWS Lamda Functions]
                    LambdaFunctions -->|User Management| CognitoPool
                    LambdaFunctions -->|User Management| CognitoPool
                    LambdaFunctions -->|CRUD Operations| AuroraDB
                    LambdaFunctions -->|Sessions Logic| S3Bucket
                    LambdaFunctions -->|RBAC Enforcement| CognitoPool
                end
                
                subgraph "Data Layer"
                    AuroraDB[Amazon Aurora for PotSICGUL]
                    S3Bucket[Amazon S3 Bucket]
                    AuroraDB -->|Encrypted at (KMS)| S3Bucket
                    S3Bucket -->|Clinical Data Lake| AuroraDB
                end
            end
        end
    end

    subgraph "Security & Compliance"
        CloudTrail[AWS CloudTrail / CodeCommit (Source)]
        CloudTrail -->|Audit Logging| LogsMetrics
        LogsMetrics(Logs Metrics)
    end
    
    subgraph "Infrasructure & Deployment"
        CodePipeline[AWS CodePialine]
        CodeBuild[AWS CodeBuild]
        IAM[AWS IAM<br>Permissions & Roles]
        Terraform(Terrofram)
        
        CodePipeline -->|Build & Test| CodeBuild
        CodeBuild -->|IaC Deployment| IAM
        CodeBuild -->|IaC Deployment| Terraform
    end
    
    style APIGateway fill:#f9f,stroke:#333,stroke-width:2px
    style CognitoPool fill:#f9f,stroke:#333,stroke-width:2px
    style LambdaFunctions fill:#f9f,stroke:#333,stroke-width:2px
    style AuroraDB fill:#f9f,stroke:#333,stroke-width:2px
    style S3Bucket fill:#f9f,stroke:#333,stroke-width:2px
    style CloudTrail fill:#f9f,stroke:#333,stroke-width:2px
    style CodePipeline fill:#f9f,stroke:#333,stroke-width:2px
    style CodeBuild fill:#f9f,stroke:#333,stroke-width:2px
    style IAM fill:#f9f,stroke:#333,stroke-width:2px
    style Terraform fill:#f9f,stroke:#333,stroke-width:2px

