---

title: "CATS Metrics Definition"
abbrev: "CATS Metrics"
category: info

docname: draft-ysl-cats-metric-definition-latest
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
 -
    ins: C. Li
    fullname: Cheng Li
    organization: Huawei Technologies
    email: c.l@huawei.com
    country: China
 -
    ins: L.M. Contreras
    fullname: L. M. Contreras
    organization: Telefonica
    email: luismiguel.contrerasmurillo@telefonica.com
 -
    ins: J. Ros-Giralt
    fullname: Jordi Ros-Giralt
    organization: Qualcomm Europe, Inc.
    email: jros@qti.qualcomm.com


normative:

informative:
  performance-metrics:
    title: performance-metrics
    author:
    org:
    date:
    target: https://www.iana.org/assignments/performance-metrics/performance-metrics.xhtml

  DMTF:
    title: DMTF
    author:
    org:
    date:
    target: https://www.dmtf.org/

--- abstract

This document defines a set of computing metrics used for Computing-Aware Traffic Steering(CATS).

--- middle

# Introduction

Many computing services are deployed in a distributed way. In such deployment mode, multiple service instances are deployed in multiple service sites to provide equivalent service to end users. In order to provide better service to end users, a framework called Computing-Aware Traffic Steering(CATS) {{!I-D.ietf-cats-framework}} is defined.

CATS is a traffic engineering approach that takes into account the dynamic nature of computing resources and network state to optimize service-specific traffic forwarding towards a given service contact instance{{!I-D.ietf-cats-framework}}. Various metrics may be used to enforce such computing-aware traffic steering policies.

To steer traffic to a service contact instance, CATS components(C-PS, C-Forwarders, etc.) need information of the service instance's computing status.  In addition to network-related metrics, a common definition of relevant computing metrics is essential for effective coordination between network devices and compute instances. Standardized metrics enable precise traffic steering decisions that optimize resource utilization and improve overall system performance.

Various considerations for metric definition are proposed in {{?I-D.du-cats-computing-modeling-description}}, which are useful in defining computing metrics.

Based on the considerations defined in {{?I-D.du-cats-computing-modeling-description}}, this document defines relevant computing metrics for CATS by categorizing the metrics into three levels based on their complexity and granularity details.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document uses the following terms defined in {{!I-D.ietf-cats-framework}}:

- Computing-Aware Traffic Steering (CATS).

- Service.

- Service contact instance.

# Definition of Metrics

Definition and usage of specific metrics are related to the intended use case. However, when considering disseminating compute metrics to network devices, appropriate categorization and abstraction of CATS metrics is required in order to avoid introducing extra complexity into the network.

This document defines three abstract metric levels to meet different requirements use cases listed in {{!I-D.ietf-cats-usecases-requirements}}:

- Level 0 (L0): Raw metrics. The metrics are not abstracted, so different metrics use their own unit and format as used within a compute orchestration domain.

- Level 1 (L1): Normalized metrics in categories. The metrics are categorized into multiple dimensions, such as network, computing, and storage. Each category metric is normalized into a value or a set of values with a range of scores.

- Level 2 (L2): Fully normalized metric. Metrics are normalized into a single value. The category information or raw metrics information cannot be interpreted from the value directly.

## Level 0: Raw Metrics

Level 0 metrics encompass detailed, raw metrics, including but not limit to:

- CPU: Base Frequency, Number of Cores,  Boosted Frequency, Memory Bandwidth, Memory Size, Utilization Ratio, Core Utilization Ratio, Power Consumption.
- GPU: Frequency, Number of Render Unit, Memory Bandwidth, Memory Size, Memory Utilization Ratio, Core Utilization Ratio, Power Consumption.
- NPU: Computing Power, Utlization Ratio, Power Consumption.
- Network: Bandwidth, Capacity, Throughput, TXBytes, RXBytes, HostBusUtilization.
- Storage: Available Space, Read Speed, Write Speed.
- Delay: Time taken to process a request.

Detailed information of a metric in L0 can be encoded into Application Programming Interface(API)(e.g., Restful API), and different services have their own metrics with different information elements. L0 metrics are used widely in IT systems.

Regarding network related raw metrics, IPPM WG has defined many types of metrics in {{performance-metrics}}. {{?RFC9439}} also defines many metrics of packet performance and Throughput/Bandwidth. Regarding computing metrics, {{?I-D.rcr-opsawg-operational-compute-metrics}} lists a set of cloud resource metrics.

## Level 1: Normalized Metrics in Categories

The metrics in L1 are categorized into different categories, and abstraction will be applied to each category. L0 raw metrics can be classified into multiple categories, such as computing, networking, storage, and delay. In each category, the metrics are normalized into a value that present the state of a resource. Potential categories are shown below:

- Computing: A normalized value generating from the computing related L0 metrics, such as CPU/GPU/NPU L0 metrics
- Networking: A normalized value generating from the network related L0 metrics.
- Storage: A normalized value generating from the storage L0 metrics.
- Delay: A normalized value generating from computing/networking/storage metrics, reflecting the processing delay of a request.

Editor note: detailed categories can be updated according to the CATS WG discussion.

The L0 metrics, such as the ones defined in {{performance-metrics}} ,{{?RFC9439}} and {{?I-D.rcr-opsawg-operational-compute-metrics}} can be categorized into above categories. Each category will use its own method(weighted summary, etc.) to generate the normalized value. In this way, the protocol only care about the metric categories and its normalized value, and avoid to process the detailed metrics.


## Level 2: Fully Normalized Metric.

L2 metric is a one-dimensional value derived from a weighted sum of L1 metrics or from L0 metrics directly. Services may have their own normalization method which might use different metrics with different weight. Some implementations may support configuration of Ingress CATS-Forwarders with the metric normalizing method so that it can decode the affection from the L1 or L0 metrics.

The definition of L2 metric simplifies the complexity of transmission and management of multiple metrics by consolidating them into a single, unified measure.

Figure 1 shows the logic of metrics in Level 0, Level 1, and Level 2.

~~~
                        +--------------+
Level 2       +---------| Normalized M |------------+
              |         +--------------+            |
              |                |                    |
              |                |  Normalizing       |
         +-------------+    +------------+     +------------+
Level 1  | Category M1 |    | Category M2|     | Category M3|  ...
         +-------------+    +------------+     +------------+
               | |               |                    | |
               | |               |Normalizing         | |
        +------+ +------+     +------+         +------+ +------+
Level 0 |Raw M1| |Raw M2|.....|Raw M3|.........|Raw M4| |Raw M5| ...
        +------+ +------+     +------+         +------+ +------+

~~~
{: #fig-cats-metric title="Logic of CATS Metrics in levels"}



# Representation of Metrics

A hierarchical view of metrics has been shown in Figure 1. This section includes the detailed representation of metrics.

[RFC9439] gives a good way to show the representation of some network metrics which is used for network capabilities exposure to
   applications. This document further describes the representation of CATS metrics.

Basically, in each metric level and for each metric, there will be some common fields for representation, including metric type, unit, and precision.  Metric type is a label for network devices to recognize what the metric is. "unit" and "precision" are usually associated with the metric.  How many bits a metric occupies in protocols is also required.

Beyond these basic representations, the source of the metrics must also be declared, since there are multiple levels of metrics and their sources are different.  As defined in [RFC9439], there are three cost-sources, nominal, sla, and estimation.  This document further divide the estimation type into three sub-types, direct measurement, aggregation, and normalization, since different levels of metrics require different sources to acquire CATS metrics.  Directly measured metrics have physical meanings and units without any processing. Aggregated metrics can be either physically meaningful or not, and they maintain their meanings compared to the directly measured metrics.  Normalized metrics can have physical meanings or not, but they do not have units, and they are just numbers that used for routing decision making.

To be more fine-grained, this document refers to the definition of [RFC9439]  on the metrics statistics.

## Level 0 Metric Representation

Raw metrics have exact physical meanings and units.  They are directly measured from the underlying computing resources providers.
   Lots of definition on this level of metrics have been defined in IT industry and other standardizations[DMTF], and this document only show some examples for different categories of metrics for reference.

### Compute Raw Metrics

The metric type of compute resources are named as “compute_type:
   CPU” or “compute_type: GPU”. Their frequency unit is GHZ, the compute
   capabilities unit is FLOPS.  Format should support integer and FP8.
   It will occupy 4 octets. Example:

~~~
Basic fields:
      Metric type: “compute type_CPU”
      Format: integer, FP8
      Bits occupation: 4 octets
Special fields:
      Frequency unit: GHZ
      Compute capabilities unit: FLOPs
Source:
      Direct measurement
Statistics:
      Mean
~~~
{: #fig-compute-raw-metric title="An Example for Compute Raw Metrics"}



### Storage Raw Metrics

   The metric type of storage resources like SSD are named as
   “storage_type: SSD”. The storage space unit is megaBytes(MBs).
   Format is integer.  It will occupy 2 octets.  The unit of read or
   write speed is denoted as MB per second. Example:

~~~
Basic fields:
      Metric type: “storage type_SSD”
      Format: integer
      Unit: GB
      Bits occupation: 2 octets
Source:
      nominal
Statistics:
      cur
~~~
{: #fig-storage-raw-metric title="An Example for Storage Raw Metrics"}

###  Network Raw Metrics

   The metric type of network resources like bandwidth are named as
   "network_type: Bandwidth”. The unit is gigabits per second(Gb/s).
   Format is integer.  It will occupy 2 octets.  The unit of TXBytes and
   RXBytes is denoted as MB per second. Example:

~~~
Basic fields:
      Metric type: “network type_Bandwidth”
      Format: integer
      Unit: Gb/s
      Bits occupation: 2 octets
Source:
      nominal
Statistics:
      cur
~~~
{: #fig-network-raw-metric title="An Example for Network Raw Metrics"}


###  Delay Raw Metrics

   Delay is a kind of synthesized metric which is influenced by
   computing, storage access, and network transmission.  It is named as
   “delay_raw”. Format should support integer and FP8.  Its unit is
   microsecond.  It will occupy 4 octets. Example:

~~~
Basic fields:
      Metric type: “delay_raw”
      Format: integer, FP8
      Unit: Microsecond(us)
      Bits occupation: 4 octets
Source:
      aggregation
Statistics:
      max
~~~
{: #fig-delay-raw-metric title="An Example for Delay Raw Metrics"}

### Considerations on the Sources of Metrics and the Statistics

The sources of L0 metrics can be nominal, directly measured, or aggregated.  Nominal L0 metrics are provided initially by resource
   providers.  Dynamic L0 metrics are measured and updated during service stage.  L0 metrics also support aggregation, in case that
   there are multiple service instances.

The statistics of L0 metrics will follow the definition of Section 3.2 of [RFC9439].

## Level 1 Metric Representation

Normalized metrics in categories have physical meanings but they do not have unit.  They are numbers after some ways of abstraction, but they can represent their type, in case that in some use cases, some specific types of metrics require more attention.

### Normalized Compute Metrics

The metric type of normalized compute metrics is “compute_norm”,
   and its format is integer.  It has no unit.  It will occupy an octet. Example:

~~~
Basic fields:
      Metric type: “compute_norm”
      Format: integer
      Bits occupation: an octet
      Score: 1
Source:
      normalization
~~~
{: #fig-normalized-compute-metric title="An Example for Normalized Compute Metrics"}

### Normalized Storage Metrics

The metric type of normalized compute metrics is “storage_norm”, and its format is integer.  It has no unit.  It will occupy a octet. Example:

~~~
Basic fields:
      Metric type: “storage_norm”
      Format: integer
      Bits occupation: an octet
      Score: 1
Source:
      normalization
~~~
{: #fig-normalized-storage-metric title="An Example for Normalized Storage Metrics"}

### Normalized Network Metrics

The metric type of normalized compute metrics is “network_norm”, and its format is integer.  It has no unit.  It will occupy a octet. Example:

~~~
Basic fields:
      Metric type: “network_norm”
      Format: integer
      Bits occupation: an octet
      Score: 1
Source:
      normalization
~~~
{: #fig-normalized-network-metric title="An Example for Normalized Network Metrics"}

### Normalized Delay

The metric type of normalized compute metrics is “delay_norm”, and its format is integer.  It has no unit.  It will occupy a octet. Example:

~~~
Basic fields:
      Metric type: “delay_norm”
      Format: integer
      Bits occupation: an octet
      Score: 1
Source:
      normalization
~~~
{: #fig-normalized-metric title="An Example for Normalized Delay Metrics"}

### Considerations on the Sources of Metrics and the Statistics

The sources of L1 metrics is normalized. Based on L0 metrics, service providers design their own algorithms to normalize metrics.  For example, assigning different cost values to each raw metric and do summation.  L1 metric do not need further statistical values.

## Level 2 Metric Representation

A fully normalized metric is a single value which does not have any physical meaning or unit.  Each provider may have its own methods to derive the value, but all providers must follow the definition in this section to represent the fully normalized value.

Metric type is “norm_fi”. The format of the value is non-negative integer.  It has no unit.  It will occupy a octet. Example:

~~~
Basic fields:
      Metric type: “norm_fi”
      Format: non-negative integer
      Bits occupation: an octet
      Score: 1
Source:
      normalization
~~~
{: #fig-level-2-metric title="An Example for Fully Normalized Metric"}

The fully normalized value also supports aggregation when there are multiple service instances providing these fully normalized values. When providing fully normalized values, service instances do not need to do further statistics.

# Comparison of three layers of metric

From L0 to L1 to L2, the computing metric is consolidated. Different level of abstraction can meet the requirements from different services. Table 1 shows the comparison among metric levels.


|  Level   | Encoding Complexity| Extensibility| Stability |Accuracy|
|:--------:+:-------------------+--------------+-----------|--------|
| Level 0  | Complicated        | Bad          | Bad       |Good    |
| Level 1  | Medium             | Medium       | Medium    |Medium  |
| Level 2  | Simple             | Good         | Good      |Medium  |
{: #comparison title="Comparison among Metrics Levels"}


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
