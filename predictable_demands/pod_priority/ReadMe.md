## Pod Priority
Scheduler handles pods as first come , first serve basis in kubernetes. There is no guarantee that an app with high importance will get scheduled, when there is a system wide resource crunch.Pod Priority helps overcome this problem by ensuring pods with higher priority gets in front of the queue for scheduling.

A Pod priority indicates the importance of a pod in relation to other pods. If a pod with higher priority cannot be scheduled, then the scheduler will try to evict lower 
priority pods to schedule the high priority pod.

Pod priority can be specified using a `PriorityClass`. Kubernetes already ships with two Priority classes, `system-cluster-critical` or `system-node-critical`(highest priority). Higher value specified in `PriorityClass` resource affects the order in which the scheduler places the pods.

`Note`:  When defining priority classes for your applications, make sure that you use a value that is lower than the built-in `system-cluster-critical` priority class. By doing so, you can be sure that your applications will not evict system-critical pods. For criticial services such as logging agents, metrics server or an identiy provider  `system-cluster-critical` or another level below should be used.
Marking a pod as `critical` does not prevent evictions, it only prevents the pod from becoming permanently unavailable.

#### Scheduler and Pod Priority
When a pod is created, the Priority Admission Controller uses the `PriorityClass` to populate the priority value for pods. Then, the scheduler sorts the pending pods queue based on priority order. If there's a resource crunch on nodes, then the scheduler can evict lower priority pods to schedule high priority ones. 

#### PreemptionPolicy
Priority class also defines a `preemptionPolicy` which defaults to `PreemptLowerPriority`. This basically results in providing scheduler the ability to terminate lower priority pods in favor of pods defined with higher `PriorityClass`. If `preemptionPolicy` is set to `Never`, pods in that PriorityClass will be non-preempting. This policy will not cause other existing pods to be evicted and pods would rather be scheduled as resources become free. 

#### Effects with PodDisruptionBudget
Pod priority can have an undesired effect on other Pods that are evicted. Kubernetes supports `PodDisruptionBudget` when preempting Pods, but respecting `PodDisruptionBudget` is only best effort(i.e. it is not guaranteed). The Scheduler first tries to find pods whose `PodDisruptionBudget` will not be violated , but if no such resources are found, then , scheduler will evict lower priority pods, even if their `PodDisruptionBudget` is violated.

### Verify Pod Priority
Apply all the files in the directory.
Ensure 3 nodes exist in the cluster for scheduling.
Run `kubectl get pdb`, which will display allowed disruptions on critical-apps-pdb. This means that in case of heavy resource utilization, scheduler based on priority classes may first evict a critical apps pod, as that is allowed without any violation, although the non-critical in this case may have a lower priority.


Further Reading:
[https://tanzu.vmware.com/developer/guides/workload-tenancy-priority-preemption/](https://tanzu.vmware.com/developer/guides/workload-tenancy-priority-preemption/)