```mermaid
graph TD
    subgraph Amazon Virtual Backend Cloud (VPC)
        subgraph Amazon Private Cloud
            subgraph Public Subnets
                A[Amazon API Gateway] -->|REST API Endpoints| B[Amazon Cognito User Pool]
                B -->|Cognito Authorizer| A
                B -->|HTTPS/TLS| C[Amazon Cognito]
            end

            subgraph Private Subnets
                D[AWS Lambda Functions] --> E[User Management]
                D --> F[CRUD Operations (Sites, Clinicians, Subjects, Sessions)]
                D --> G[Sessions Logic]
                D --> H[RBAC Enforcement]

                I[Amazon Aurora for PostgreSQL] -.-> J[(HIPAA Compliant)]
                I -.-> K[(Encrypted at Rest (KMS))]
                I -.-> L[(Raw/Processed Files Encrypted at Rest (KMS))]
                I -.-> M[(ACID Properties)]

                N[Amazon S3 Bucket] -.-> O[(Clinical Data Lake)]
                N -.-> P[(HIPAA Compliant)]
                N -.-> Q[(Encrypted at Rest (SSE-KMS))]
                N -.-> R[(Pre-Signed URLs)]

            end

            C --> S{Groups/Admin}
            C --> T{Clinicians}
            C --> U{Researchers}
            C -.- V{MFA/OATH/SSM}

        end

        subgraph Security & Compliance
            W[AWS CloudTrail / CodeCommit (Source)] 
            W -.- X((Logs))
            W -.- Y((Metrics))
        end

        subgraph Infrastructure & Deployment
            Z[AWS CodePipeline | Build & Test]
            Z -.- AA[AWS CodeBuild | IaC Deployment]
        end
        
    end
    
    AA ---> AB[AWS IAM Permissions & Roles]
    AA ---> AC(Terraform)
