# Microservice Deployment

In this section, we will deploy a sample microservice application called 
[Online Boutique](https://github.com/GoogleCloudPlatform/microservices-demo/)
developed by Google to demonstrate the capabilities of microservice deployment
and their kubernetes engine. To know more about the architecture of Online Boutique,
check out [their documentation](https://github.com/GoogleCloudPlatform/microservices-demo#architecture). You can see a running version of this application [here](https://onlineboutique.dev/).

## Screenshots

| Home Page                                                                                                         | Checkout Screen                                                                                                    |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| [![Screenshot of store homepage](./img/online-boutique-frontend-1.png)](./img/online-boutique-frontend-1.png) | [![Screenshot of checkout screen](./img/online-boutique-frontend-2.png)](./img/online-boutique-frontend-2.png) |

## Deployment

For this project, you will be using a pre-rendered version of this application with
publicly available docker containers. Run the following command on your master node. 

```sh
# deploy the default manifest
kubectl apply -f https://raw.githubusercontent.com/hamzehkhazaei/EECS6446_project/master/files/online-boutique.yaml
```

It may take up to 10 minutes for the deployment to be up and running. You can
wait for all pods to go to the `Running` state using the following command:

```console
$ watch kubectl get pods
NAME                                     READY   STATUS    RESTARTS        AGE
adservice-694bf8ff94-b9mnr               1/1     Running   0               4m6s
cartservice-7d5d88fc79-tv2k2             1/1     Running   0               4m7s
checkoutservice-b459bb6d8-sjxv2          1/1     Running   0               4m8s
currencyservice-55cb47d4d4-j64cb         1/1     Running   0               4m6s
emailservice-d6c7867f8-z5mj8             1/1     Running   0               4m8s
frontend-6fc96cdc7b-tc4qq                1/1     Running   0               4m8s
loadgenerator-864dd5f677-wdnlk           1/1     Running   3 (3m25s ago)   4m7s
paymentservice-68ddc89586-n8m79          1/1     Running   0               4m7s
productcatalogservice-65bc67d964-l6kwx   1/1     Running   0               4m7s
recommendationservice-6c4bf9877d-j9w89   1/1     Running   0               4m8s
redis-cart-5d69499b44-2kvvk              1/1     Running   0               4m6s
shippingservice-c9bbf96d6-2sbg8          1/1     Running   0               4m6s
```

After deploying the application, we need to check if the website is actually running. Since the VMs are on VirtualBox’s NAT network, we’ll tunnel the application’s port from the VM to our own machine using SSH.

### 1. Find the SSH port for master node
Run:
```sh
eecsvm ssh ubuntu1
```

You’ll see an instruction like this:
```console
ssh into the VM ubuntu1 with: ssh <user>@localhost -p 46150
```
Here, the port for the master VM is 46150. (Your number may be different.)

### 2. Create an SSH Tunnel
Use the port from the previous step to forward the application’s port (8089 in this example) to your machine:

```sh
ssh -N -f -L 8089:localhost:8089 common@localhost -p 46150
ssh -N -f -L 8080:localhost:80 common@localhost -p 46150
```

Replace `46150` with the actual port number you got from `eecsvm ssh ubuntu1`.

### 3. Access the Website
After the all the services are up and running, you can open the deployed website by openning the Firefox browser and going to the address `http://localhost:8080`. You can also open the load generator UI by going to the address `http://localhost:8089`.

If everything has worked well, you will be able to open the online store deployed and see the load generator plots and statistics shown. We will later on use the load generator API to change the number of simulated users and query the quality of service statistics from the load generator.

[Next Step](04-loadgenerator.md) -->

## References

- [Online Boutique Development Guide](https://github.com/GoogleCloudPlatform/microservices-demo/blob/master/docs/development-guide.md)
- [Online Boutique Original Deployment](https://raw.githubusercontent.com/GoogleCloudPlatform/microservices-demo/master/release/kubernetes-manifests.yaml)
