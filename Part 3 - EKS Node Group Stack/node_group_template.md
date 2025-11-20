Milestone 3 — Deploying the EKS Worker Node Group (CloudFormation)
Objective

Milestone 3 focuses on provisioning the EKS Worker Node Group, which provides the compute capacity required to run Kubernetes workloads.
This milestone builds on:

Milestone 1: VPC & network layer

Milestone 2: Secure EKS Control Plane

In this milestone, we deploy:

An IAM Role for worker nodes

The EKS NodeGroup resource

Private worker node placement across multiple subnets

Node labels for organization and scheduling

Scaling configuration (min / desired / max)

Cross-stack imports from earlier milestones (cluster name + private subnets)

Infrastructure Built in This Milestone
1. IAM Role for Worker Nodes

Worker nodes require an IAM role that allows them to:

Join the EKS cluster

Pull container images from ECR

Use the EKS CNI plugin for networking

Communicate with the control plane

Policies Attached

AWS-managed policies required:

AmazonEKSWorkerNodePolicy

AmazonEKS_CNI_Policy

AmazonEC2ContainerRegistryReadOnly

Sample Code – IAM Role
NodeIamRole:
  Type: AWS::IAM::Role
  Properties:
    RoleName: worker-nodes-role
    Description: IAM role for EKS worker nodes
    AssumeRolePolicyDocument:
      Version: "2012-10-17"
      Statement:
        - Effect: Allow
          Principal:
            Service: ec2.amazonaws.com
          Action: sts:AssumeRole
    ManagedPolicyArns:
      - arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy
      - arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy
      - arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly

2. EKS NodeGroup Resource

This is the primary worker node component.
It defines:

ClusterName (imported from Milestone 2)

IAM NodeRole

Node instance type

Scaling configuration

Node labels

Private subnet placement

Scaling Configuration

MinSize: 1

DesiredSize: 2

MaxSize: 4

Subnet Placement

Nodes are deployed in AppSubnetA and AppSubnetB, allowing:

High availability

Even node distribution across Availability Zones

Future scaling across both subnets

Sample Code – NodeGroup
NodeGroup:
  Type: 'AWS::EKS::Nodegroup'
  Properties:
    ClusterName: !ImportValue mtier:EksClusterName
    NodeRole: !GetAtt NodeIamRole.Arn
    InstanceTypes:
      - !Ref EksNodeInstanceType
    ScalingConfig:
      MinSize: 1
      DesiredSize: 2
      MaxSize: 4
    Labels:
      env: prod
      role: worker
      nodegroup: app-workers
    Subnets:
      - !ImportValue mtier:AppSubnetA
      - !ImportValue mtier:AppSubnetB

Cross-Stack Imports Used in This Milestone
From Milestone 1 (VPC Stack):

mtier:AppSubnetA

mtier:AppSubnetB

From Milestone 2 (Control Plane Stack):

mtier:EksClusterName

Importing these values avoids duplication and ensures that all stacks remain linked properly in a modular architecture.
