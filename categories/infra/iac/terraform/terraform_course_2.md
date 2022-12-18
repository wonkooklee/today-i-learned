# Learning IaC with Terraform - Chapter II

> Thanks to  
[Course on Youtube](https://www.youtube.com/watch?v=7xngnjfIlK4)

<br />

## Basics

### Todo: Walk through web app TF config
- backend + provider config
- EC2 instances
- S3 Bucket
- VPC
  - Subnet
- Security groups + rules
- Application load balancer
  - ALB target group + attachment
- Route 53 zone + record
- RDS instance

<br />

```mermaid
flowchart TD
  A[Amazon Route 53] --> B[Elastic Load Balancing]
  B --> Compute
  subgraph Compute
    C[Amazon EC2]
    D[Amazon EC2]
  end
  subgraph Database
    F[(Amazon RDS)]
  end
  Compute --- S
  subgraph S[Amazon S3]
    G[(Bucket)]
  end
  Compute --- Database
```

<br />

---

<br />

```terraform
terraform {
  required_provider {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

provider "aws" {
  region = "ap-northeast-2"
}

resource "aws_s3_bucket" "terraform_state" {
  bucket        = "devops-directive-tf-state" # REPLACE WITH MY BUCKET NAME
  force_destroy = true
  versioning {
    enabled = true
  }

  server_side_encryption_configuration {
    rule {
      apply_server_side_encryption_by_default {
        sse_algorithm = "AES256"
      }
    }
  }
}
```