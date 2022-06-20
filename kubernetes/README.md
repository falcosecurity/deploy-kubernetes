# Kubernetes manifest files

Under this directory, you can find the Kubernetes manifest files to deploy Falco and its components.

## Falco

The [./falco](./falco) directory gives you the required YAML files to stand up Falco on Kubernetes as a DaemonSet. This will result in a Falco Pod being deployed to each node, and thus the ability to monitor any running containers for abnormal behavior.

### Deploying to Kubernetes

```
kubectl apply -k falco
```

### Verifying the installation

In order to test that Falco is working correctly, you can, for instance, launch a shell in any Pod. Right after it, you should be able to see a message in the logs of the Falco Pod.

```
$ kubectl get pods
NAME          READY     STATUS    RESTARTS   AGE
falco-74htl   1/1       Running   0          13h
falco-fqz2m   1/1       Running   0          13h
falco-sgjfx   1/1       Running   0          13h

$ kubectl exec -it falco-74htl -- bash
root@falco-74htl:/# exit

$ kubectl logs falco-74htl
{"output":"17:48:58.590038385: Notice A shell was spawned in a container with an attached terminal 
(user=root k8s.pod=falco-74htl container=a98c2aa8e670 shell=bash parent=<NA> cmdline=bash terminal=34816)",
"priority":"Notice","rule":"Terminal shell in container","time":"2017-12-20T17:48:58.590038385Z", 
"output_fields": {"container.id":"a98c2aa8e670","evt.time":1513792138590038385,"k8s.pod.name":"falco-74htl",
"proc.cmdline":"bash","proc.name":"bash","proc.pname":null,"proc.tty":34816,"user.name":"root"}}
```

Alternatively, you can deploy the [event-generator](https://github.com/falcosecurity/event-generator) application to automatically generate events that test your Falco deployment. Please note that this application will generate a large number of events.

To deploy the `event-generator` in Kubernetes you can follow [these steps](https://github.com/falcosecurity/event-generator#with-kubernetes).


## Falcosidekick

```
kubectl apply -k falcosidekick
```

## falco-exporter

```
kubectl apply -k falco-exporter
```
