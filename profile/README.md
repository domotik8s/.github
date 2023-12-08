# Welcome To DomotiK8s
_A modern plartform for home automation_

The name "DomotiK8s" comes from the word "domotics" ([Wikipedia](https://en.m.wiktionary.org/wiki/domotics)) which basically means "home automation" and "K8s" which is short for [Kubernetes](https://kubernetes.io). Hence, DomotiK8s is a home automation platform built on top of Kubernetes which enables us to make use of some unique advantages when compared to solutions that where built from scratch.

## Why Kubernetes?
Kubernetes has evolved to a powerful orchestration engine that can be used to automate pretty much anything. As such, the platform already as much of the plumbing that is useful and any application. We leverage these machanisms reusing what already exists. Therefore, we spend less time reinventing the wheel. Examples include authentication and authorization, inter-process communication, load-balancing, distributed database, role-based access control and many more. In addition, K8s is a great platform to run additional supporting services on such as a web-based code editor, VPN services, file sharing (smb, etc) or anything else. The Kubernetes ecosystem provides thousands and thousands of components, for example in the form of Helm charts.

## DomotiK8s Principles
DomotiK8s' goal is not only to bring home automation onto Kubernetes. Much more importantly, we strive to add significant improvements when compared to other home automation applications such as HomeAssitant or OpenHAB. The main principles and goals include the following:

1. **Multi-User Home**: We understand that most homes aren't inhabited by a single person. While many users will use a home automation platform, the same level of access should not be granted to everybody. The admin should have a different level of access than kids, temporary guests or other parties. We implement strong role-based access control that allows granular access control.
2. **High Availability**: Many users reliy heavily on their home automation platform. All the more does an outage of the server that runs is impact the daily life. DomotiK8s can be installed very slimly as a single instance or scaled to multiple redundant instances potentially spread across the house to maximize resiliency.
3. **Dynamicness of Homes**: A home is a dynamic place that changes over time. Devices come and go, rooms are repurposed permanently or temporarily. This is especially a challenge for automations. DomotiK8s 
4. **Generated UI**: Most platforms out there require a significant investment of time to create usable dashboards. While some people enjoy knocking themselves out over buttons, cards, charts, responsive layouts, color themes and others, we believe home automation should focus on the "automation" part rather than wasting time on dashboards. DomotiK8s strives to generate a beatiful general purpose dashboard that meets the majority of user requirements out-of-the-box.
