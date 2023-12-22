# Example Spin app in K8S

This assumes you have
* Spin 2.0.1 or later
* The Spin k8s plugin
* and a recent k3d

## Setup

Here's how I created a k3ds cluster:

```console
$ k3d cluster create wasm-cluster --image ghcr.io/deislabs/containerd-wasm-shims/examples/k3d:v0.10.0 -p "8081:80@loadbalancer" --agents 2 --registry-create mycluster-registry:12345
```

(Check the version on the Wasm shims. v0.10 has Spin 2.0.1, which is what I am using at this time)

Check out the docs here: https://developer.fermyon.com/spin/v2/kubernetes 

Here's what I did:
* Created teh app `hellok8s` with `spin new hellok8s -t http-rust`
* `spin k8s scaffold default`

So you should be able to cd into that directory and do:

```
$ spin build
$ spin k8s build
$ k3d image import -c wasm-cluster default/hellok8s:0.1.0
$ spin k8s deploy
```

Then check with `kubectl get pods` that the app is running:

```console
$ k get po
NAME                       READY   STATUS    RESTARTS   AGE
hellok8s-b976687f5-z89zk   1/1     Running   0          18m
hellok8s-b976687f5-jdm9z   1/1     Running   0          18m
hellok8s-b976687f5-6jcj8   1/1     Running   0          18m
```

After that, it should be possible to `curl` it:

```
$ curl localhost:8081/hellok8s
```

## Troubleshooting

* I am using Docker Desktop. I always go to the `settings -> Features in development` and uncheck `Use containerd...` restart, and then check and restart. Some folks at Docker tell me that this is necessary to get the latest versions of the shims. 
* This doc at k3d is really helpful: https://k3d.io/v5.4.6/usage/exposing_services/
