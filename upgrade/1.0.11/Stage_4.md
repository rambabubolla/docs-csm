# Stage 4 - Rollout DNS Unbound Deployment Restart

>**`NOTE:`**
>
> This stage is only necessary if CSM v1.0.10 has not yet been installed (i.e. upgrading from v1.0.1 directly to v1.0.11).
>

Instruct Kubernetes to gracefully restart the Unbound pods:

```text
ncn-m001:~ # kubectl -n services rollout restart deployment cray-dns-unbound
deployment.apps/cray-dns-unbound restarted

ncn-m001:~ # kubectl -n services rollout status deployment cray-dns-unbound
Waiting for deployment "cray-dns-unbound" rollout to finish: 0 out of 3 new replicas have been updated...
Waiting for deployment "cray-dns-unbound" rollout to finish: 3 old replicas are pending termination...
Waiting for deployment "cray-dns-unbound" rollout to finish: 3 old replicas are pending termination...
Waiting for deployment "cray-dns-unbound" rollout to finish: 3 old replicas are pending termination...
Waiting for deployment "cray-dns-unbound" rollout to finish: 2 old replicas are pending termination...
Waiting for deployment "cray-dns-unbound" rollout to finish: 2 old replicas are pending termination...
Waiting for deployment "cray-dns-unbound" rollout to finish: 2 old replicas are pending termination...
Waiting for deployment "cray-dns-unbound" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "cray-dns-unbound" rollout to finish: 1 old replicas are pending termination...
deployment "cray-dns-unbound" successfully rolled out
```

Once `Stage 4` is completed, proceed to [Stage 5](Stage_5.md)