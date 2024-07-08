---

title: "CATS metric Definition"
abbrev: "CATS Metric"
category: info

docname: draft-ysl-cats-metric-definition-00
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus:
v: 3
area: "Routing"
workgroup: "Computing-Aware Traffic Steering"
keyword: CATS, metric

author:
 -
    ins: Y. Kehan
    fullname: Kehan Yao
    organization: China Mobile
    email: yaokehan@chinamobile.com
    country: China
 -
    ins: H. Shi
    fullname: Hang Shi
    organization: Huawei Technologies
    email: shihang9@huawei.com
    country: China
    role: editor
 -
    ins: C. Li
    fullname: Cheng Li
    organization: Huawei Technologies
    email: c.l@huawei.com
    country: China
    role: editor




normative:

informative:
  performance-metrics:
    title: performance-metrics
    author:
    org:
    date:
    target: https://www.iana.org/assignments/performance-metrics/performance-metrics.xhtml


--- abstract

This document defines the computing metrics used in Computing-Aware Traffic Steering.

--- middle

# Introduction

Many modern computing services are deployed in a distributed way. In this deployment mode, multiple service instances are deployed in multiple sites to provide equivalent function to end users. In order to provide better service to end users, a framework called CATS (Computing-Aware Traffic Steering) {{!I-D.ietf-cats-framework}} is proposed.

CATS (Computing-Aware Traffic Steering) {{!I-D.ietf-cats-framework}} is a traffic engineering approach that takes into account the dynamic nature of computing resources and network state to optimize service-specific traffic forwarding towards a given service contact instance. Various relevant metrics may be used to enforce such computing-aware traffic steering policies.

To effectively steer traffic to the appropriate service instance, network devices need a model of the service instance's computing status. A common definition of computing metrics is essential for effective coordination between network devices and computing systems. Without standardized computing metrics, devices on the network may interpret and respond to traffic conditions and computing load differently, leading to inefficiencies and potential conflicts. A standardized metric allows both network devices and computing systems to evaluate load consistently, enabling precise traffic steering decisions that optimize resource utilization and improve overall system performance.

Various considerations for metric definition are proposed in {{?I-D.du-cats-computing-modeling-description}}, which are useful in defining computing metrics.

Based on the considerations defined in {{?I-D.du-cats-computing-modeling-description}}, this document defines relevant computing metrics for CATS by categorizing the metrics into three levels based on their complexity and richness.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document uses terms defined in {{!I-D.ietf-cats-framework}}. We list them below for clarification.

- Computing-Aware Traffic Steering (CATS): An architecture that takes into account the dynamic nature of computing resources and network state to steer service traffic to a service instance. This dynamicity is expressed by means of relevant metrics.

- Service: An offering that is made available by a provider by orchestrating a set of resources (networking, compute, storage, etc.).

- Service instance: An instance of running resources according to a given service logic.

# Definition of Metrics

Many metrics are discussing and/or defined in routing and computing area. Definition and usage of specific metrics are highly related to the use case, especially in IT use cases. However, when considering distributing compute metrics to network devices, appropriate categorizing and abstraction is required in order to not introduce extra complexity into the network.

Based on the abstraction level of metrics, this document defines three levels of metric to meet different requirements of different use cases:

- Level 0(L0): Raw Metrics. In this level, the metrics are not abstracted, so different metrics use their own unit and format.

- Level 1(L1): Normalized Metrics in Categories. In this level, the metrics are categorized into multiple dimensions, such as network, computing and storage. Each category metric is normalized into a value.

- Level 2(L2): Fully Normalized Metric. In this level, metrics are normalized into a single value, the category information or raw metrics information cannot be interpreted from the value directly.

## Level 0: Raw Metrics

The metrics without any abstraction are Level 0 metrics. Therefore, Level 0 metrics encompass detailed, raw metrics, including but not limit to:

- CPU: Base Frequency, Number of Cores,  Boosted Frequency, Memory Bandwidth, Memory Size, Memory Utilization Ratio, Core Utilization Ratio, Power Consumption.
- GPU: Frequency, Number of Render Unit, Memory Bandwidth, Memory Size, Memory Utilization Ratio, Core Utilization Ratio, Power Consumption.
- NPU: Computing Power, Utlization Ratio, Power Consumption.
- Network: Bandwidth, TXBytes, RXBytes, HostBusUtilization.
- Storage: Available Space, Read Speed, Write Speed.
- Delay: Time takes to process a request.

In L0, detailed information of a metric can be encoded into the protocol, and different service has its own metrics with different information elements. This kind of metrics are used widely in IT systems.

Regarding network related raw metrics, IPPM WG has defined many types of metrics in {{performance-metrics}}. {{?RFC9439}} also defines a lot of metrics of packet performance and Throughput/Bandwidth. Regarding computing metrics, {{?I-D.rcr-opsawg-operational-compute-metrics}} defines a set of cloud resource metrics.

## Level 1: Categorized Metrics.

In Level 1, the metrics will be categorized into different categories, and appropriate abstraction will be applied to each category. The Level 0 raw metrics can be categorized into multiple categories, such as computing, networking, storage and delay. In each category, the metrics are normalized into a value that present the state of the resource, making it as a Level 1 metric. Potential categories are shown below:

- Computing: A normalized value generating from the computing related L0 metrics, such as CPU/GPU/NPU L0 metrics
- Networking: A normalized value generating from the network related L0 metrics.
- Storage: A normalized value generating from the storage L0 metrics.
- Delay: A normalized value generating from computing/networking/storage metrics, reflecting the processing delay of a request.

Editor note: detailed categories can be updated according to the CATS WG discussion.

The L0 metrics, such as the ones defined in {{performance-metrics}} ,{{?RFC9439}} and {{?I-D.rcr-opsawg-operational-compute-metrics}} can be categorized into above categories. Each category will use its own method(weighted summary, etc.) to generate the normalized value. In this way, the protocol only care about the metric categories and its normalized value, and avoid to process the detailed metrics.


## Level 2: Fully Normalized Metric.

L2 metric is a one-dimensional value derived from a weighted sum of L1 metrics or from L0 metrics directly. Different service has its own normalization method which might use different metrics with different weight. For the ingress CATS router, it can compare the metric value to make the traffic steering decision (e.g., larger value has higher priority) . In some cases, some implementations may support to configure the ingress CATS router to know the metric normalizing method so that it can decode the affection from the L1 or L0 metrics.

This method simplifies the complexity of transmission and management of multiple metrics by consolidating them into a single, unified measure.


# Comparison of three layers of metric

From L0 to L1 to L2, the computing metric is consolidated. Different level of abstraction can meet the requirements from different services. Table 1 shows the comparison among metric levels.


|  Level   | Encoding Complexity| Extensibility| Stability |Accuracy|
|:--------:+:-------------------+--------------+-----------|--------|
| Level 0  | Complicated        | Bad          | Bad       |Good    |
| Level 1  | Medium             | Medium       | Medium    |Medium  |
| Level 2  | Simple             | Good         | Good      |Medium  |
{: #ref-to-tab title="Comparison among Metrics Levels"}


Since Level 0 metrics are raw metrics, therefore, different services may have their own metrics, resulting in hundreds or thousands of metrics in total, this brings huge complexity in protocol encoding and standardization. Therefore, this kind of metrics are always used in customized IT systems case by case. In Level 1 metrics, metrics are categorized into several categories and each category is normalized into a value, therefore they can be encoded into the protocol and standardized. Regarding the Level 2 metrics, all the metrics are normalized into one single metric, it is easier to be encoded in protocol and standardized. Therefore, from the encoding complexity aspect, Level 2 and Level 1 metrics are suggested.

Similarly, when considering extensibility, new services can define their own new L0 metrics, which requires protocol to be extended as needed. Too many metrics type can create a lot of overhead to the protocol resulting in a bad extensibility of the protocol. Level 1 introduce only several metrics categories, which is acceptable for protocol extension. Level 2 metric only need one single metric, so it brings least burden to the protocol. Therefore, from the extensibility aspect, Level 2 and Level 1 metrics are suggested.

Regarding Stability, new Level 0 raw metrics may require new extension in protocol, which brings unstable format for protocol, therefore, this document does not recommend to standardize Level 0 metrics in protocol. Level 1 metrics request only few categories, and Level 2 Metric only introduce one metric to the protocol, so they are preferred from the stability aspect.


In conclusion, for computing-aware traffic steering, it is recommended to use the L2 metric due to its simplicity. If advanced scheduling is needed, L1 metric can be used. L2 metrics are the most comprehensive and dynamic, therefore transferring them to network devices is discouraged due to their high overhead.

Editor notes: this draft can be updated according to the discussion of metric definition in CATS WG.


# Security Considerations

TBD

# IANA Considerations

TBD


--- back
