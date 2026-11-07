# Task 1: Multi-container Pods (Init Container & Sidecar)

3-node Minikube cluster running an Nginx page that shows a timestamp, updated every 5 seconds by a sidecar through `/proc/<pid>/root` (shareProcessNamespace, no volumes).

## Running Pods


3 replicas, 2/2 containers each, spread across the 3 nodes.

## Timestamp updates
![Before]
![After]\

The sidecar rewrites index.html every 5 seconds, so the time changes on refresh.

## Failure diagnosis
![CrashLoop]
![Zero downtime]
![Diagnosis]

The init container exits with code 1, so the new Pod stays in Init:CrashLoopBackOff. The old Pods keep serving traffic (zero downtime). `kubectl logs -c init-done` and `kubectl describe pod` show the cause.

## Verification after fix
![After fix])

After restoring `exit 0`, the Deployment returned to the healthy ReplicaSet; all 3 Pods are `2/2 Running` across 3 nodes, and the failed Pod was removed.
