# Testing the Horizontal Pod Autoscaler

Now that we have everything up and running, it is time to test the
Kubernetes' built-in autosclaer called [Horizontal Pod Autoscaler (HPA)](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/).


Clone this project's git repository:

```sh
$ git clone https://github.com/hamzehkhazaei/EECS6446_project
```

Before moving to our jupyter notebook, we need to install the dependencies of our
experimentation script. To do so, you can run the following command:

```sh
$ cd EECS6446_project/notebooks/
$ pip install -r requirements.txt
```

To open the jupyter notebook (EECS6446_project/notebooks/experiment.ipynb) go to its' directory and run jupyter notebook:

```sh
$ cd EECS6446_project/notebooks/
$ jupyter notebook
```

This will give you the link to open jupyter notebook interface. To be able to open the link on your remote machine, run the following command on a new terminal:

```console
$ ssh -N -f -L 8888:localhost:8888 common@localhost -p 46150
```

**Replace `46150` with the actual port number you got from `eecsvm ssh ubuntu1`.**

This notebook includes an integration of all the components we have deployed
to our cluster and uses their respective APIs to get monitoring/actionable data.
You can see a preview of the jupyter notebook on [GitHub](https://github.com/hamzehkhazaei/EECS4222_Project_1/blob/master/notebooks/experiment.ipynb).

While running the experiment, you can monitor the status of the deployments using provided
interfaces as well as the commandline:

```console
$ watch kubectl get deploy
Every 2.0s: kubectl get deploy                                             

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
redis-cart              1/1     1            1           2d2h
shippingservice         1/1     1            1           2d2h
paymentservice          1/1     1            1           2d2h
emailservice            1/1     1            1           2d2h
adservice               1/1     1            1           2d2h
loadgenerator           1/1     1            1           2d2h
productcatalogservice   2/2     2            2           2d2h
checkoutservice         2/2     2            2           2d2h
recommendationservice   3/3     3            3           2d2h
currencyservice         3/3     3            3           2d2h
frontend                4/4     4            4           2d2h
cartservice             1/1     1            1           2d2h
```

[Final Step](09-phase1-evaluation.md) -->

## References

- [Medium: Cluster Autoscaler and Horizontal Pod Autoscaler for on-premise Kubernetes Clusters](https://jonachin.medium.com/cluster-autoscaler-and-horizontal-pod-autoscaler-for-on-premise-kubernetes-clusters-b90cb54c262b)
- [HPA Documentations](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)
- [HPA Algorithm Design](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#algorithm-details)
