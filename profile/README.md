# Welcome To DomotiK8s
_A modern platform for home automation_

The name "DomotiK8s" is derived from "domotics" ([Wikipedia](https://en.m.wiktionary.org/wiki/domotics)), which essentially means "home automation," and "K8s," an abbreviation for [Kubernetes](https://kubernetes.io). Thus, DomotiK8s is a home automation platform built atop Kubernetes, offering unique advantages over solutions developed from scratch.

## Motivation - Why Kubernetes?
Kubernetes has developed into a potent orchestration engine capable of automating a wide array of tasks. This platform already includes many essential features beneficial for any application. We take advantage of these mechanisms by reusing what already exists, avoiding the need to reinvent the wheel. Examples include authentication, authorization, inter-process communication, an extensible CRUD API, load balancing, distributed database management, role-based access control, and more. Additionally, K8s is an excellent platform for running supplementary services like web-based code editors, VPN services, file sharing (such as SMB), configuration management systems to support GitOps and others. The Kubernetes ecosystem offers a vast array of components that already exist and only wait to be leveraged for home automation.

Here is another example: Home automation systems require strong and flexible automation engines do to complex things. While Kubernetes can get the "current state" to the "desired state", it doesn't have any inherent mechanisms to define that "desired state" based on complex rules (date, time, sun events, brightness, presence, temperature, average values of a sensor over time, etc, etc). Instead of implementing this in DomotiK8s, we rely on an established solution called [NodeRED](https://nodered.org/) which is already [integrated with Kubernetes](https://flows.nodered.org/node/node-red-contrib-kubernetes-client-ubos).


## Principles & Goals
The aim of DomotiK8s is not merely to integrate home automation with Kubernetes. More importantly, we aim to introduce substantial improvements over other home automation applications, such as Home Assistant or OpenHAB. Our main principles and goals include:

1. **Multi-User Home**: Recognizing that homes typically house multiple people, we believe not everyone should have the same level of access. Admins, children, temporary guests, and other parties should have appropriately varied access levels. We employ strong, role-based access control for detailed access management.
2. **High Availability**: Given the heavy reliance of many users on their home automation platforms, server outages can significantly disrupt daily life. DomotiK8s offers flexible installation options, from a lean single instance to multiple redundant instances distributed throughout the house, maximizing resiliency.
3. **Dynamic Nature of Homes**: Homes are dynamic environments where devices frequently change, and rooms may be repurposed. This variability poses a challenge for automation, which DomotiK8s addresses effectively.
4. **OOTB Dashboards**: Unlike other platforms that require considerable effort to create functional dashboards, DomotiK8s focuses on the automation aspect, minimizing time spent on dashboard configuration. Our goal is to provide a beautiful, general-purpose dashboard that meets most user needs right out of the box allowing them to spend their time on automating things rather than fiddling with UIs.
5. **GitOps**: Moving away from the trend of UI-based setups and configurations seen in established solutions, DomotiK8s reintroduces file-based configuration. In case of system loss or corruption, instead of starting from scratch, users can securely store their configuration work in a Git repository or as plain-text files for easy backup and recovery.
6. **Reuse Of Existing Solutions**: DomotiK8s will not try and reinvent the wheel. Almost nothing of what home automation does is so unique that it justifies duplication of work. As examples, we will never build our own way to store timeseries data when there are solutions such as Prometheus. We will never build out own automation engine when there are solutions such as NodeRED, and so on, and so on.

# What Does That Look Like?

Controllering your smart home from Kubernetes works by Customer Resource Definitions (CRDs) that DomotiK8s adds to your Kubernetes. As an example, let's take a look at the `Light` resource when used with KNX:

1. Define and create a `Light` device
   
**office-main-light.yaml**
```yaml
apiVersion: domotik8s.io/v1beta1
kind: Light
metadata:
  name: office-main-light
  labels:
    area: "House"
    floor: "FirstFloor"
    room: "Office"
    name: "CeilingLight"
spec:
  connection:
    system: "knx"
    config:
      power:
        read: "0/1/15"
        write: "0/1/14"
        dpt: "1.001"
  enforce: true
```
```bash
$ kubectl apply -f example-light.yaml
```

2. Verify the light was created
```bash
$ kubectl get lights
NAME                AGE
office-main-light   2s
```

3. Have a look at the details
```bash
$ kubectl describe light office-main-light
Name:         office-main-light
Namespace:
Labels:       area=House
              floor=FirstFloor
              name=CeilingLight
              room=Office
Annotations:  <none>
API Version:  domotik8s.io/v1beta1
Kind:         Light
Metadata: ...
Spec:
  Capabilities:
    Brightness:  false
    Color:       false
    Power:       true
  Connection:
    Config:
      Power:
        Dpt:    1.001
        Read:   0/1/15
        Write:  0/1/14
    System:     knx
  Enforce:      false
  State:
    Power:  false
Status:
  Last Updated:  2023-12-11T20:21:40.512763+01:00
  State:
    Power:  false
Events:     <none>
```

4. Turn the `Light` on
```bash
kubectl patch light office-main-light --type=merge -p '{"spec": {"state": {"power": true}}}'
```
Admittedly, patching Kubernetes resources like this is not very convenient. Yet, you will rarely - if ever - manipulate a device state on the command line like this. We could think about developing a kubectl extension to make this smoother if we find users want it.
