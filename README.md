# AWS - EKS Setup

Amazon Elastic Kubernetes Service (Amazon EKS) is a managed Kubernetes service provided by Amazon Web Services (AWS). Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications.

With Amazon EKS, AWS takes care of the underlying Kubernetes infrastructure, allowing users to focus on building, deploying, and scaling their containerized applications. Some key features of Amazon EKS include:

1. **Managed Kubernetes Control Plane:** AWS manages the Kubernetes control plane, ensuring high availability and reliability.

2. **Easy Cluster Setup:** Amazon EKS simplifies the process of setting up and running Kubernetes clusters on AWS. Users can create and manage clusters with just a few clicks or through the AWS Management Console, CLI, or SDKs.

3. **Security and Compliance:** EKS integrates with AWS Identity and Access Management (IAM) for authentication and authorization, and it supports Amazon Virtual Private Cloud (VPC) network policies for network isolation. This helps users implement security best practices.

4. **Scalability:** EKS allows for easy scaling of clusters to meet changing workload demands. Users can add or remove worker nodes to adjust capacity.

5. **Integration with AWS Services:** EKS seamlessly integrates with other AWS services, such as Elastic Load Balancing, AWS Identity and Access Management (IAM), Amazon VPC, and more.

6. **Multi-Region Availability:** Amazon EKS is available in multiple AWS regions, providing flexibility for deploying applications closer to end-users or meeting regulatory requirements.

# Setup AWS-EKS
### 1. Cluster creation
##### 1.1. Create Cluster
![1-eks_cluster_creation](https://github.com/prabhakar2020/aws-eks/assets/7165155/0122e79a-4941-4a3c-836d-6e99b2598778)
![2-eks_cluster_creation2](https://github.com/prabhakar2020/aws-eks/assets/7165155/2230c8a1-e779-4c59-b733-e403a6d8aa8d)
![3-eks_cluster_creation3](https://github.com/prabhakar2020/aws-eks/assets/7165155/13d02641-4a23-4888-98f4-1f25b0909914)
##### 1.2. Create Cluster IAM Role creation
![4-eks_cluster_service_role](https://github.com/prabhakar2020/aws-eks/assets/7165155/341d2257-198d-43df-8fea-5f43112cfa4e)
##### 1.3. Other settings
![5-eks_cluster_networking](https://github.com/prabhakar2020/aws-eks/assets/7165155/e9fe6fc3-4b0a-4405-a582-c002fd61a035)
![6-eks_security_groups](https://github.com/prabhakar2020/aws-eks/assets/7165155/c1975027-521c-47a8-b050-7daa76bc2e71)
![7-eks_configure-observability](https://github.com/prabhakar2020/aws-eks/assets/7165155/a677db72-00cf-454c-b20c-7bcbae95d4fd)
![8-eks_add-ons](https://github.com/prabhakar2020/aws-eks/assets/7165155/bab95257-d256-4936-a59b-8ce16d25f351)
![9-eks_cluster-view](https://github.com/prabhakar2020/aws-eks/assets/7165155/06a32bd2-7437-42df-bfcd-8266b814e704)

### 2. Node Group creation
##### 2.1. Create Node Group
![10-eks_cluster-add-node-groups](https://github.com/prabhakar2020/aws-eks/assets/7165155/dcf19415-4e0e-4670-90e0-46292fc6a377)
##### 2.2. Create IAM role for NodeGroup
![11-eks_cluster--node-groups-Iam-role](https://github.com/prabhakar2020/aws-eks/assets/7165155/d663abfb-aa2e-4756-bc2d-725ca838ddc9)
##### 2.3. Create NodeGroup configurations
![12-eks_cluster--node-groups-configuration](https://github.com/prabhakar2020/aws-eks/assets/7165155/cd914290-ae6c-43b5-bfc4-b859be1da5a3)
##### 2.4. NodeGroup Scaling configurations
![13-eks_cluster-node-groups-scaling-config](https://github.com/prabhakar2020/aws-eks/assets/7165155/fe706dbb-434a-449d-b479-38342170f94e)
##### 2.4. NodeGroup preview after creation
![14-eks_cluster-node-groups-output](https://github.com/prabhakar2020/aws-eks/assets/7165155/af472fa9-38cd-4f68-a8b2-1162948517dc)

### 3. Perform Operations via CloudShell
##### 3.1. Open CloudShell
![15-cloudShell](https://github.com/prabhakar2020/aws-eks/assets/7165155/1d5a7847-a25a-4af0-9bcc-9a64ee352994)
##### 3.2. Update EKS Kubernetes configurations
![16-update-eks-kube-config](https://github.com/prabhakar2020/aws-eks/assets/7165155/3d28ad79-ca1e-4649-bbe0-a5361e086953)
##### 3.3. Upload Kubernetes manifest files via CloudShell
![17-upload-manifest-files](https://github.com/prabhakar2020/aws-eks/assets/7165155/f078cd4d-23a0-4ea3-9b0c-65a556e61169)

### 4. Kubernetes Manifest files
##### 4.1 Deployment manifest file
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app.kubernetes.io/name: my-application
    app.kubernetes.io/instance: my-application
    app.kubernetes.io/version: '1.0.0'
    app.kubernetes.io/managed-by: kubectl
    app: my-application
  name: my-application
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-application
  template:
    metadata:
      labels:
        app: my-application
    spec:
      containers:
        - image: <<DockerImage>> # Change this image source path
          imagePullPolicy: Always
          name: my-application
          ports:
          - containerPort: 80
          envFrom:
          - configMapRef: 
              name: my-application-config-map
          - secretRef:
              name: my-application-secrets
```
##### 4.2 Service manifest file
```
apiVersion: v1
kind: Service

metadata:
  name: my-application-service #cluster name

spec:
  selector:
    app: my-application
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: LoadBalancer
```
##### 4.3 ConfigMap manifest file
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-application-config-map
data:
  AWS_DEFAULT_REGION: "ap-south-1"
  ENVIRONMENT: "dev"
```
##### 4.4 Secrets manifest file
```
# use this command to encrypt the secrets in base64 `echo -n "value" | base64`
apiVersion: v1
kind: Secret
metadata:
  name: my-application-secrets
type: Opaque
data:
  DB_USERNAME: DB_USERNAME=
  DB_PASSWORD: DB_PASSWORD=
  DB_HOSTNAME: DB_HOSTNAME==
  DB_PORT: DB_PORT==
```
### 5. Kubernetes deployment
##### 5.1 Deployment via manifest files
```
kubectl apply -f .
```
##### 5.2 Delete all deployments via manifest files
```
kubectl delete -f .
```
##### 5.3 Get deployment status
```
kubectl get deployment
```
##### 5.4 Get pod status
```
kubectl get pod -w
```
##### 5.5 Get ConfigMap
```
kubectl get configmap
```
##### 5.6 Get Secrets
```
kubectl get secrets
```
##### 5.7 Get service status
```
kubectl get service
```
We can use this service url to connect to the application running in POD




