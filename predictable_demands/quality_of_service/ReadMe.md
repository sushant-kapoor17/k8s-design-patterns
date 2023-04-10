## Quality of Service

Based on requests/limits provided for a pod, kubernetes platform will offer a specific Quality of Service(QoS)

#### Best Effort
Pods which do not have any `requests` or `limits` set for it's containers. These are known as the lowest priority pods and will be the first to be terminated by `kubelet` , if the node is under pressure due to resource crunch.


#### Burstable
Pods which have both `requests` and `limits` defined, however the values are not equal. Usually, the limits will be larger than requests, which means that the pod can consume more resources(when available) upto it's limit. However, if the node is under severe resource pressure, these pods will be the first to be terminated by `kubelet`, if no `Best effort` pods remain

#### Guaranteed
Pods which have equal values for `requests` and `limits`. There are highest priority pods and are not to be termintated before `Best effort` and `Burstable` pods.

### Verify QoS
 Apply all the resource files in the directory and run the following command:

`kubectl get pods -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.qosClass}{"\n"}{end}'`



