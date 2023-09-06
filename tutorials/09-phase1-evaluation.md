# Evaluation 

By the end of this tutorial you should have a kubernetes cluster up and
running, the microservice application deployed, a functional monitoring stack,
kubernetes HPA enabled, and have performed experiments on the cluster.

You need to record (i.e., captured video recording/s) of your deployment to show us your system is working
properly for the following scenarios. You may use Zoom to record your screen and also explain the process, i.e, talk over the video for number 1, 2 and 3. 

## 1. Kubernetes (25% &ndash; max 3 minute length) 

Your kubernetes cluster should be up and running, your nodes should all be in the
state `Ready`, all pods should be in the state `Running`, and your installation should
generally be healthy.

## 2. Application Deployment (25% &ndash; max 3 minute length)

All pods that make up different microservices for the application should be successfully
deployed, in the `Running` state, and you should be able to navigate the application
running on your cluster using the IP of the `master` node.

## 3. Monitoring Stack (25% &ndash; max 4 minute length)

Your monitoring stack should be up and running, all pods should be in a `Healthy`
status and all pods should be `Running`. You should be able to open your prometheus
installation by opening port `9090` on your `master` node and be able to query
the monitored metrics like CPU or memory utilizations.

## 4. Experiment Report (25% &ndash; min 3 pages and max 5 pages.)

You need to write a report discussing the results you got from running the provided
Jupyter notebook on your cluster. Extract the generated plots from the Jupyter notebook
and explain the process discussing how and why the autoscaling events have occurred.

# Submission
You need to submit one zip file in eClass including your captured video/s (max 10 min length) and 
the experiment report in PDF.
