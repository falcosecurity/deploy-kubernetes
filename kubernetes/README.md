# Kubernetes Manifest Files

Under this directory, you can find the Kubernetes manifest files to deploy Falco and its components.

## Falco

The [./falco](./falco) directory gives you the required YAML files to stand up Falco on Kubernetes as a DaemonSet. This will result in a Falco Pod being deployed to each node, and thus the ability to monitor any running containers for abnormal behavior.

### Prerequisites and Template Limitations

The default configuration in Falco utilizes the kernel module driver (`kmod`). The template daemonset setup in this repository is configured to deploy to the `default` namespace. To modify [falco.yaml](https://github.com/falcosecurity/falco/blob/master/falco.yaml) settings or experiment with different kernel drivers, refer to the [Install and Operate Guides](https://falco.org/docs/install-operate/), specifically the [Kubernetes Deployment](https://falco.org/docs/install-operate/deployment/) page. These guides provide insights on adjusting Falco's settings to suit various drivers, along with prerequisites for each. Also, check out the [libs](https://github.com/falcosecurity/libs) repository that contains the sources for each driver.

For additional guidance on using Falco in Kubernetes or benchmarking within a testbed, consider exploring the repository at https://github.com/falcosecurity/cncf-green-review-testing. Please note that the repository primarily focuses on testbed benchmarking, and as a result, the setup might not be best for real-world use.

__NOTE__: If you're working with [minikube](https://minikube.sigs.k8s.io/docs/start/) locally, bear in mind that specific mount setups might be necessary. For instance, running `minikube start --mount --mount-string="/usr/src:/usr/src" --driver=docker` ensures that the `driver-loader` can access `/usr/src/kernels/` if the kernel driver (`kmod` or `ebpf`) is built on-the-fly. Running Falco with `--modern-bpf` does not require building a driver because it is already bundled within the userspace binary. This capability is enabled by the newer CORE (Compile Once - Run Everywhere) BPF feature and works only on more recent kernels (>= 5.8).

### What's Included in the `falco` Deployment?

**initContainers**
- `falco-driver-loader`: Downloads the kernel driver or attempts to build it on-the-fly.
- `falcoctl-artifact-install`: Downloads default rules and installs falcoctl along with other artifacts like plugins.


**containers**
- `falco`: Executes the Falco binary.
- `falcoctl-artifact-follow`: Utilizes falcoctl's functionality to watch for updated rules.

The template daemonset setup does not handle the method of extracting Falco logs from the container to their final destination (such as a data lake or SIEM). You can explore using tools like `falco-exporter`, `falcosidekick`, or create custom solutions.

### Deploy to Kubernetes

Launch the deployment with [kustomize](https://kustomize.io/):

```
kubectl apply -k falco
```

Tear Down would be:

```
kubectl delete -k ./falco
```

### Verify the Installation

In order to test that Falco is working correctly, you can, for instance, launch a shell in any Pod. Right after it, you should be able to see a message in the logs of the Falco Pod.

```
$ kubectl -n default get pods
NAME          READY     STATUS    RESTARTS   AGE
falco-74htl   1/1       Running   0          13h
falco-fqz2m   1/1       Running   0          13h
falco-sgjfx   1/1       Running   0          13h

$ kubectl -n default exec -it falco-74htl -c falco -- bash
root@falco-74htl:/# exit

$ kubectl -n default -c falco logs falco-74htl

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
