## Q1. A Pod is stuck in Pending state. How do you troubleshoot it?
First, I will check why the Pod is in a 'Pending' state. The main reason for this status could typically be resource shortages, node issues, taints/tolerations, node selectors/affinity, PVC issues, or scheduling constraints.

**Step by Step.**
1. **I will check the Pod status.**
   ```bash
   kubectl get pod <pod-name> -o wide
   ```
2. **Then, I will check Pod events.**
   ```bash
   kubectl describe pod <pod-name>
   ```
   I'll check the Events section. Here, the scheduler generally indicates the exact reason, such as:
   - Insufficient cpu
   - Insufficient memory
   - node(s) had taint
   - didn't match node selector
   - pod has unbound immediate PersistentVolumeClaims
3. **I will check Node availability**
   
   I will check if the nodes are ready and if the required CPU/memory is available.
   ```bash
   kubectl get nodes
   kubectl describe node <node-name>
   ```
4. **I will check Taint and tolerations.**
   
   If a node has a taint and the pod does not have a matching toleration, the pod will not be scheduled.
   ```bash
   kubectl describe node <node-name> | grep Taints
   ```
5. **I will check Pod resources.**
   
   If CPU/memory requests exceed the available capacity, the scheduler will not place the pod.
   ```bash
   kubectl get pod <pod-name> -o yaml
   ```
6. **If PVC is using, then I will check for it.**
   ```bash
   kubectl get pvc
   kubectl describe pvc <pvc-name>
   ```
   
## Q2. A Pod is in CrashLoopBackOff. What checks do you perform?
## Q3. A Pod is showing ImagePullBackoff. How do you resolve it?
## Q4. A Pod is stuck in ContainerCreating. What could be the reasons?
## Q5. A Pod is continuously restarting. How do yo identify the root cause?
## Q6. A Container is terminated with OOMKilled, How do you troubleshoot it?
## Q7. A Pod is stuck in Terminating state. How do you remove it safely?
## Q8. A Pod is running. but it is not receiving traffic from the service. How do you troubleshoot?
## Q9. A Pod starts successfully but crashes after a few minutes. How do you investigate?
## Q10. A Pod works in DEV but fails in Production. How do you compare and troubleshoot the environment?
## Q11. A Pod is not getting scheduled even though nodes appear to have free resources. What could be the reason?
## Q12. A Pod shows FailedScheduling. What events and configurations do you check?
## Q13. A Pod is not starting because a ConfigMap or Secret is missing. How do you identify the issue?
## Q14. A Pod cannot connect to an external database. What checks do you perform?
