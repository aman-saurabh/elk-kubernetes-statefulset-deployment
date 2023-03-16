# elk-kubernetes-statefulset-deployment
A sample project to deploy elk cluster with kubernetes.

For reference you can check following link :- 
https://devopscounsel.com/elasticsearch-kibana-setup-on-kubernetes-cluster/

**********************************************************************************************
For this project to work properly we need to ensure few things:

1.) For load balancer to work with AWS EKS cluster :- Install and setup AWS Load Balancer Controller for EKS cluster :- For this use the following link :-

https://docs.aws.amazon.com/eks/latest/userguide/aws-load-balancer-controller.html

But for it to work you will also need to Create an IAM OIDC provider for your cluster.You can use the following link for this purpose.

https://docs.aws.amazon.com/eks/latest/userguide/enable-iam-roles-for-service-accounts.html

Apart from that we also need to set some tags in all subnets. You can check following links for help and details :

https://www.youtube.com/watch?v=3WbEt_sfTWU

https://aws.amazon.com/premiumsupport/knowledge-center/eks-load-balancer-controller-subnets/

https://aws.amazon.com/premiumsupport/knowledge-center/eks-vpc-subnet-discovery/

2.) For dynamic provisioning of persistent volumes to work :- We will need to setup Amazon EBS CSI driver. You can check following link for help.

https://aws.amazon.com/premiumsupport/knowledge-center/eks-persistent-storage/

**********************************************************************************************

Note :- Here we have also created elk-persistent-volume.yaml and elk-pvc.yaml to create persistent volume manually. But this will not work with statefulsets, so we have setup dynamic provisioning of persistent volume.

Create namespace first :-
kubectl apply -f elk-namespace.yaml


Now create storage class :-
kubectl apply -f elk-storage.yaml -n elk-test


Verify that storage class is ccreated successfully :-
kubectl get storageclass -n elk-test
Or, 
kubectl describe storageclass -n elk-test


Now create service(headless service for stateful set) :-
kubectl apply -f elk-service.yaml -n elk-test

Verify that service is ccreated successfully :-
kubectl get service -n elk-test
Or, 
kubectl describe service -n elk-test


Now create statefulset for elasticsearch :-
kubectl apply -f elk-statefulsets.yaml -n elk-test

It will create pod for elasticsearch as well as persistent volume claim.

Verify that pod is created successfully :-
kubectl get pods -n elk-test

Check the endpoints for elasticsearch :-
kubectl get endpoints -n elk-test -o wide

Verify that persistent volume claim is created successfully :-
kubectl get pvc -n elk


Create deployment and service(load-balancer type) for kibana :-
kubectl apply -f elk-kibana.yaml -n elk-test

Verify that pod is created successfully :-
kubectl get pods -n elk-test

Verify that service is ccreated successfully :-
kubectl get service -n elk-test


Now you can go to Load balancers(Inside EC2 section), select the newly created load balancer for your service and copy the DNS URL and enter that in the browser. DNS URL should be something like below:

a5re9133c0eed943gh46gfa9c542-6545459600.ap-south-1.elb.amazonaws.com

So Enter the following URL in the browser and you should be able to visit the workpress site we deployed. 
http://a5re9133c0eed943gh46gfa9c542-6545459600.ap-south-1.elb.amazonaws.com
