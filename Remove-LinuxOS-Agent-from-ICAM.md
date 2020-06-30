## Remove the Linux OS object from ICAM

- Stop the lz agent in the managed host:

```
./os-agent.sh stop
```

- Find and delete the specific lz resource (replace the var value accordingly)

```
export NAMESPACE=kube-system
export TENANTID=403e-eb0a
export LZHOST=cocsm1

RESOURCEID=`kubectl -n ${NAMESPACE} exec -it $(kubectl get po -n kube-system|grep ibmcloudappmgmt-applicationmgmt-rest|awk '{print $1}') curl -- -kL -H "X-TenantID: ${TENANTID}" "127.0.0.1:8080/applicationmgmt/0.9/resources?_limit=5000"|jq -r '._items[] | select(.name == "'${LZHOST}'" and .type == "linuxOS")|._id'` && echo ${RESOURCEID}

## sample resource id output: 2DOYKmbeQT6j_et0I-oEvA

kubectl -n ${NAMESPACE} exec -it $(kubectl get po -n kube-system|grep ibmcloudappmgmt-applicationmgmt-rest|awk '{print $1}') curl -- -kL -H "X-TenantID: ${TENANTID}"  -X DELETE "127.0.0.1:8080/applicationmgmt/0.9/resources/${RESOURCEID}"
```
> make sure the ui object disappeared.


## Re-populate the agent to dashboard.

- Restart the lz agent from managed host:

```
./os-agent.sh start
```

- Restart the metric provider

```
kubectl get po |grep ibmcloudappmgmt-metricprovider-|awk '{print $1}'|xargs kubectl delete po
```

- Check the UI dashboard
