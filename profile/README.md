# Welcome To DomotiK8s
_A modern platform for home automation_

The name "DomotiK8s" is derived from "domotics" ([Wikipedia](https://en.m.wiktionary.org/wiki/domotics)), which essentially means "home automation," and "K8s," an abbreviation for [Kubernetes](https://kubernetes.io). Thus, DomotiK8s is a home automation platform built atop Kubernetes, offering unique advantages over solutions developed from scratch.

## Why Kubernetes?
Kubernetes has developed into a potent orchestration engine capable of automating a wide array of tasks. This platform already includes many essential features beneficial for any application. We take advantage of these mechanisms by reusing what already exists, avoiding the need to reinvent the wheel. Examples include authentication, authorization, inter-process communication, an extensible CRUD API, load balancing, distributed database management, role-based access control, and more. Additionally, K8s is an excellent platform for running supplementary services like web-based code editors, VPN services, file sharing (such as SMB), configuration management systems to support GitOps and others. The Kubernetes ecosystem offers a vast array of components that already exist and only wait to be leveraged for home automation.

## Principles & Goals
The aim of DomotiK8s is not merely to integrate home automation with Kubernetes. More importantly, we aim to introduce substantial improvements over other home automation applications, such as Home Assistant or OpenHAB. Our main principles and goals include:

1. **Multi-User Home**: Recognizing that homes typically house multiple people, we believe not everyone should have the same level of access. Admins, children, temporary guests, and other parties should have appropriately varied access levels. We employ strong, role-based access control for detailed access management.
2. **High Availability**: Given the heavy reliance of many users on their home automation platforms, server outages can significantly disrupt daily life. DomotiK8s offers flexible installation options, from a lean single instance to multiple redundant instances distributed throughout the house, maximizing resiliency.
3. **Dynamic Nature of Homes**: Homes are dynamic environments where devices frequently change, and rooms may be repurposed. This variability poses a challenge for automation, which DomotiK8s addresses effectively.
4. **OOTB Dashboards**: Unlike other platforms that require considerable effort to create functional dashboards, DomotiK8s focuses on the automation aspect, minimizing time spent on dashboard configuration. Our goal is to provide a beautiful, general-purpose dashboard that meets most user needs right out of the box allowing them to spend their time on automating things rather than fiddling with UIs.
5. **GitOps**: Moving away from the trend of UI-based setups and configurations seen in established solutions, DomotiK8s reintroduces file-based configuration. In case of system loss or corruption, instead of starting from scratch, users can securely store their configuration work in a Git repository or as plain-text files for easy backup and recovery.
6. **Reuse Of Existing Solutions**: DomotiK8s will not try and reinvent the wheel. Almost nothing of what home automation does is so unique that it justifies duplication of work. As examples, we will never build our own way to store timeseries data when there are solutions such as Prometheus. We will never build out own automation engine when there are solutions such as NodeRED, and so on, and so on.

This revision corrects spelling and grammar errors and improves the clarity of the original text.

# Usage Example

## Creating & Switching A Light
1. Define and create a `Light` device
**example-light.yaml**
```yaml
apiVersion: domotik8s.io/v1beta1
kind: Light
metadata:
  name: example-light
spec:
  connection:
    type: "knx"
    config:
      power:
        read: "1/2/3"
        write: "1/2/4"
      brightness:
        read: "1/2/5"
        write: "1/2/6"
```
```bash
$ kubectl apply -f example-light.yaml
```

2. Check discovered device capabilities and state
```bash
$ kubectl get light example-light -o yaml
```
```yaml
apiVersion: domotik8s.io/v1beta1
kind: Light
metadata:
  name: kitchen-light
spec:
  connection: ... # Same as above
  capabilities: # Discovered device capabilities
    power: true
    brightness: true
    color: false
  state: # Desired state initialized with the current state
    power: false
    brightness: 0
status:
  state: # Current state of the device
    power: false
    brightness: 0
  lastUpdated: "2023-12-08T18:00:00Z"
```

3. Turn the `Light` on
```bash
kubectl patch light example-light --type=merge -p '{"spec": {"state": {"power": true}}}'
```
Admittedly, patching Kubernetes resources like this is not very convenient. Yet, you will rarely - if ever - manipulate a device state on the command line like this. We could think about developing a kubectl extension to make this smoother if we find users want it.
