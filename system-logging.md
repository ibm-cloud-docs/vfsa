---

copyright:
  years: 2024
lastupdated: "2024-01-22"

keywords: working, routing, static, default, creating, ospf, bgp

subcollection: vfsa

---

{{site.data.keyword.attribute-definition-list}}

# Setting up system logging
{: #system-logging}

The Junos OS generates system log messages (also called syslog messages) to record events, including the following:

*	Routine operations, such as a user login to the configuration database
*	Failure and error conditions, such as failure to access a configuration file
*	Emergency or critical conditions, such as a power-down of the device due to excessive temperature

The default configuration does not send systems logs to an external server. It is recommended that you send logs to external servers as explained in this topic.

Each system log message identifies the Junos OS process that generated the message and briefly describes the operation or error that occurred. For detailed information about specific system log messages, see the [System Log Explorer](https://apps.juniper.net/syslog-explorer/){: external}.

## Junos OS system logging message severity levels
{: #logging-severity}

Table 1 lists the severity levels that you can specify in your configuration at the `edit system syslog` hierarchy level. Levels from `emergency` through `info` are in order from the highest severity (greatest effect on functioning) to the lowest.

Unlike other severity levels, the `none` level disables logging of a facility instead of indicating the severity of a triggering event on routing functions. For more information, see [Disabling the system logging of a facility](https://www.juniper.net/documentation/us/en/software/junos/network-mgmt/topics/topic-map/system-logging-on-a-single-chassis-system.html#id-disabling-the-system-logging-of-a-facility){: external}.

| Value	| Severity level | Description |
| ------------- | ------------- | ------------- |
| N/A	| None | Disables logging of the associated facility to a destination. |
| 0	| Emergency	| System panic, or other condition, that causes the router to stop functioning. |
| 1	| Alert	| Conditions that require immediate correction, such as a corrupted system database. |
| 2	| Critical	| Critical conditions, such as hard errors. |
| 3	| Error	| Error conditions that generally have less serious consequences than errors at the `emergency`, `alert`, and `critical` levels. |
| 4	| Warning	| Conditions that warrant monitoring. |
| 5	| Notice	| Conditions that are not errors, but might warrant special handling. |
| 6	| Info	| Events or non-error conditions of interest. |
| 7	| Any	| All severity levels. |
{: caption="Table 1. System log message severity levels" caption-side="bottom"}

The following is a sample vFSA syslog configuration for sending logs to a remote syslog server. Your actual settings depend on the configuration of the syslog server receiving the logs.

```sh
set system syslog user * any emergency
set system syslog host 1.1.1.1 any any
set system syslog host 1.1.1.1 match RT_FLOW_SESSION
set system syslog file messages any any
set system syslog file messages authorization info
set system syslog file interactive-commands interactive-commands any
```

## Configuring system logging for a security device
{: #configure-syslog}

Junos OS supports the configuration and monitoring of system log (or syslog) messages. You can configure files to log system messages while also assigning attributes, such as severity levels, to messages. Reboot requests are recorded to the system log files, which you can view with the `show log` command.

### Control plane logs
{: #control-plane-logs}

The control plane logs, also called system logs, include events that occur on the routing platform. The system sends control plane events to the event process on the routing engine, which then handles the events by using Junos OS policies and generating system log messages, or both. You can choose to send control plane logs to a file, user terminal, routing platform console, or remote machine. To generate control plane logs, use the syslog statement at the `system` hierarchy level.

### Data plane logs
{: #data-plane-logs}

The data plane logs (also called security logs) primarily include security events that are handled inside the data plane. Security logs can be in text or binary format, and can be saved locally (event mode), or sent to an external server (stream mode). Binary format is required for stream mode and recommended to conserve log space in event mode.

Junos OS supports the forwarding of logs using stream mode and event mode. All categories can be configured for sending specific category logs to different log servers for stream-mode log forwarding.

Always use stream mode to save CPU resources. This is critical for other operations, such as internode communication within Highly Available (HA) or RPD operations.
{: important}

Stream-mode log forwarding includes the following steps:

*	The data plane generates an RTLOG system log message and sends it out from the packet forwarding engine.
*	A PFE process generates an RTLOG system log message and sends it out from the packet forwarding engine.
*	The routing engine unified threat management (UTMD) process generates an RTLOG system log message, and the routing engine sends it out using the RTLOGD process.

For stream-mode log forwarding, the transport protocol used between the packet forwarding engine and the log server can be UDP, TCP, or TLS. These transport protocols are configurable. The transport protocol used between the routing engine and the log server can only be UDP.

The following sample configuration provides an example of configuring a data plane log using stream mode.

```sh
set security log mode stream
set security log source-address 172.168.0.3
set security log stream S1 format syslog
set security log stream S1 category all
set security log stream S1 host 1.1.1.1
```

## Redundant system log server
{: #redundant-log-server}

Security system logging traffic intended for remote servers is sent through the network interface ports, which support two simultaneous system log destinations. You must configure each system logging destination separately. When you configure two system log destination addresses, identical logs are sent to both destinations. While two destinations can be configured on any device that supports the feature, adding a second destination is primarily useful as a redundant backup for standalone and active/backup configured chassis cluster deployments.

## Related links
{: #logging-related-links}

* [Overview of system logging](https://www.juniper.net/documentation/us/en/software/junos/network-mgmt/topics/topic-map/system-logging.html){: external}
* [Configuring system logging for a security device](https://www.juniper.net/documentation/us/en/software/junos/network-mgmt/topics/topic-map/system-logging-for-a-security-device.html){: external}
* [System Log Explorer](https://apps.juniper.net/syslog-explorer/){: external}
* [Setting the system to stream security logs](https://www.juniper.net/documentation/us/en/software/junos/network-mgmt/topics/topic-map/system-logging-for-a-security-device.html#id-understanding-stream-logging-for-security-devices){: external}
* [Configuring traffic logs](https://supportportal.juniper.net/s/article/SRX-Getting-Started-Configure-Traffic-Logging-Security-Policy-Logs-for-SRX-Branch-Devices?language=en_US){: external}
