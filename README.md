When deploying both (the `k8s-admin-port.yaml` and `k8s-non-admin-port.yaml`) to kubernetes, you can see the node app for `admin-port` failing:


```bash
kubectl get po
NAME                          READY   STATUS             RESTARTS      AGE
non-admin-port                1/1     Running            0             4m22s
admin-port                    0/1     CrashLoopBackOff   5 (51s ago)   4m5s
```

Looking at the logs provides more context:

```bash
kubectl logs admin-port
node:events:497
      throw er; // Unhandled 'error' event
      ^

Error: listen EACCES: permission denied 0.0.0.0:80
    at Server.setupListenHandle [as _listen2] (node:net:1876:21)
    at listenInCluster (node:net:1941:12)
    at Server.listen (node:net:2029:7)
    at Function.listen (/usr/src/app/node_modules/express/lib/application.js:635:24)
    at Object.<anonymous> (/usr/src/app/index.js:14:5)
    at Module._compile (node:internal/modules/cjs/loader:1378:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1437:10)
    at Module.load (node:internal/modules/cjs/loader:1212:32)
    at Module._load (node:internal/modules/cjs/loader:1028:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:142:12)
Emitted 'error' event on Server instance at:
    at emitErrorNT (node:net:1920:8)
    at process.processTicksAndRejections (node:internal/process/task_queues:82:21) {
  code: 'EACCES',
  errno: -13,
  syscall: 'listen',
  address: '0.0.0.0',
  port: 80
}

Node.js v21.6.2
```

In contrast, when looking at the logs of the `non-admin-port` container you'll see this:

```bash
kubectl logs non-admin-port
Example app listening on port 1025
Healthz called
Healthz called
....
```