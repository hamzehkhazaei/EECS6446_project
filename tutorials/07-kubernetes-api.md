# Kubernetes API for Scaling Resources

Using the API provided by Kubernetes, we can interact with out cluster,
get the current status of deployments, change the replica count for each
deployment and many more. You can check out a list of kubernetes client
libraries for different programming languages in
[in their documentations](https://kubernetes.io/docs/reference/using-api/client-libraries/).
To follow along with this tutorial, you will need to install and configure
the [Python client library for kubernetes](https://github.com/kubernetes-client/python/).

<!-- To ease the process of working with jupyter notebooks on the master node,
we suggest installing Visual Studio Code outlined in the requirements section
along with the suggested extensions. -->

First, let's install the necessary library on the `master` VM to interact with the
Kubernetes API:

```sh
pip install kubernetes==12.0.1
```

Then, we can move to our python code. Create `.py` file on your master VM and copy paste the following code in it.
To create a file, you can use `nano filename.py`.

<!-- You may use the [VS Code Jupyter Extension](https://code.visualstudio.com/docs/python/jupyter-support) for the next steps (from right panel choose Jupyter extension and from File tab choose new file -> python file), or use simple `.py` files. -->

The following code imports libraries and defines a couple of helper functions that we can use later on.

```py
from kubernetes import client, config
config.load_kube_config()

api_instance = client.AppsV1Api()

def get_replica_and_ready(deployment_name, deployment_ns="default"):
    api_response = api_instance.read_namespaced_deployment(deployment_name, deployment_ns)
    return api_response.status.replicas, api_response.status.ready_replicas

def set_replica_num(rnum, deployment_name, deployment_ns="default"):
    rnum = int(rnum)
    if rnum < 1:
        rnum = 1
    api_response = api_instance.read_namespaced_deployment(deployment_name, deployment_ns)
    api_response.spec.replicas = rnum
    api_instance.patch_namespaced_deployment_scale(deployment_name, deployment_ns, api_response)
    
    
# Example usage:
# get_replica_and_ready('frontend')
# set_replica_num(3,'frontend')
```

Now we can use these functions to get or change the replica count for each deployment.
<br />	
For example, to get the number of set and ready pods for the `frontend` deployment, we can use the following (you may need to run the following command with a short delay to see the changes after using the **set_replica_num()** function):

```py
get_replica_and_ready('frontend')
```

Similarly, to set the number of pods in `frontend` deployment to 3, we can use:

```py
set_replica_num(3,'frontend')
```

You probably won't need to use other Kubernetes APIs for this project, but in
case you were interested, you can check out [their documentation](https://github.com/kubernetes-client/python/blob/master/kubernetes/README.md).

[Next Step](08-hpa-test.md) -->

## References

- [Github: Kubernetes Python Client](https://github.com/kubernetes-client/python)
- [Kubernetes Python Client: Examples](https://github.com/kubernetes-client/python/tree/master/examples)
