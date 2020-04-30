# EKS-event-demo
This is repo for [Demo Online talk](https://www.eventbrite.com/e/online-talk-kubernetes-at-aws-and-integrating-with-other-aws-services-tickets-102763337910)

# DEMO Presentation
Demo Presentation [Link](https://docs.google.com/presentation/d/1WOg4HB2I5Ksh2ZGdpaHe2vvbKihB5tFbH0mprMJYhTg/edit?usp=sharing)

# Step by step
```
1. Deploy the ALB Controller
2. Deploy deployment.yaml
3. Deploy service.yaml
4. Deploy ingress.yaml
```
# For deploy ALB Controller on demo
##Create an IAM OIDC provider and associate it with your cluster.
```
eksctl utils associate-iam-oidc-provider --region ap-southeast-1 --cluster cluster-demo --approve
```
##Create an IAM policy called ALBIngressControllerIAMPolicy for the ALB Ingress Controller pod that allows it to make calls to AWS APIs on your behalf.
```
aws iam create-policy --policy-name ALBIngressControllerIAMPolicy --policy-document https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/iam-policy.json
```
##Create a Kubernetes service account named alb-ingress-controller in the kube-system namespace, a cluster role, and a cluster role binding for the ALB Ingress Controller to use with the following command
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/rbac-role.yaml
```
##Create an IAM role for the ALB Ingress Controller and attach the role to the service account created in the previous step
```
eksctl create iamserviceaccount --region ap-southeast-1 --name alb-ingress-controller --namespace kube-system --cluster cluster-demo --attach-policy-arn arn:aws:iam::<your_AWS_ACCOUNT_ID>:policy/ALBIngressControllerIAMPolicy --override-existing-serviceaccounts --approve
```
##Deploy the ALB Ingress Controller with the following command
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/alb-ingress-controller.yaml
```
##Open the ALB Ingress Controller deployment manifest for editing with the following command
```
kubectl edit deployment.apps/alb-ingress-controller -n kube-system
```
##Add a line for the cluster name after the --ingress-class=alb line.
```
 spec:
      containers:
      - args:
        - --ingress-class=alb
        - --cluster-name=cluster-demo
```

#Create and another source for learning
```
1. [AWS link for ALB Controler](https://docs.aws.amazon.com/eks/latest/userguide/alb-ingress.html#w243aac23b7c17c10b3b1)
2. [Article based on Demo](https://dev.to/iilness2/practical-way-to-setup-redirect-http-to-https-with-aws-eks-3m5i)
```
