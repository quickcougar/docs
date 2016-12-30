---
title: Okami
template: default
---

# Okami

# ![logo/graphic](insert_logo/graphic_if_applicable)

Okami is Exosite's advanced connectivity solution. It allows anything capable
of sending IP packets (devices, mobiles, desktops, etc.) to securely connect
and authenticate to Exosite's IoT platform. It tracks and maintains information
about device connections and facilitates bidirectional communication between
the devices and Exosite's systems.

# Resources

* [Getting Started](getting_started)
* [Overview](#overview)
* [Terminology](#terminology)
* [FAQs](#faqs)

# Overview

Okami's connectivity solution enables devices to connect to Exosite using the
MQTT, Exosite CBOR, or HTTP protocol. All connections are secured by TLS.
Devices are assigned a unique identity and are required to authenticate using
device-specific credentials as supported by the chosen protocol. Devices report
data to resources in Exosite and may receive control requests from Exosite,
including requests to update firmware.

Okami's event mechanism provides notifications when devices connect,
disconnect, and send data. Additionally, Okami persists and makes available
device state representing the last reported resource values from the device and
any outstanding control requests to the device.

Okami's identity management supports provisioning as a means to assign an
identity -- and associate authentication credentials -- to a device.
Provisioned devices have an identity in Exosite and can authenticate using
assigned credentials. Explicitly provisioned devices are assigned an identity
(and given a means to authenticate) prior to first connecting with Exosite;
whereas implicitly provisioned devices are given authentication credentials and
(possibly) an identity when they first connect to Exosite.

Devices report data to Resources. A resource is identified by an alias, has a
data format (string, number, or boolean), and, possibly, has a value.
Additionally, it is possible to restrict the possible values for a resource
(either by specifying a range or by providing a discrete value list) and to
identify the value's unit. A connected thermometer might, for example, report
to a resource with alias "temperature", value "number", unit °C, and range -50
- 100.

# Protocols

## MQTT

Okami supports MQTT as a wire protocol.  All messages are delivered with a
"zero" quality of service (QoS 0), a device may only publish to topics
representing its own resources and will automatically be subscribed to control
messages, published messages are automatically retained as the last assigned
value to the respective resources, and control messages are automatically held
until disconnected devices reconnect.

MQTT clients authenticate using either TLS client certificates or tokens.
Client certificates require explicitly provisioned devices, whereas token
authentication supports implicit provisioning. When utilizing certificates, the
certificate's "common name" must be assigned to the device's ID and the
certificate itself serves as the password -- the device provides neither a
username nor a password. When utilizing tokens, the device ID represents the
username and the token the password.

The MQTT wire protocol is fairly simple and any MQTT client library should
work. The connection must be secured by TLS and requires the SNI extension.
Some MQTT libraries include support for TLS/SNI, others will need their
connections wrapped via something like mbed TLS.

## Exosite CBOR

The Exosite CBOR protocol utilizes a raw TCP socket with CBOR-encoded payloads
and limited semantics to support authentication. CBOR is an ideal data format
for devices as it requires little code, produces wire-efficient messages, and,
as a binary format, intrinsically handles binary content.

In addition to the two authentication methods supported by MQTT (TLS client
certificates and tokens), the Exosite CBOR protocol also supports
private/public key signature validation and secret key hashing. As with TLS
client certificates, both utilize private keys

## HTTP

Okami is backwards-compatible with the HTTP-based OnePlatform Data API
(http://docs.exosite.com/http). This protocol supports CIK authentication only,
as described by the API. HTTPS connections are preferred; devices connecting
via HTTP will be flagged as "developement devices" and subject to restrictions
such as rate limiting.

--------------------------------------------------------------------------------

# Terminology

(quick overview of protocols, authentication mechanisms, and other terms)

| Term    | Definition             |
| ------- | ---------------------- |
| Term 1  | Definition of Term 1.  |
| Term 2  | Definition of Term 2.  |

# FAQs

Q: How is Okami different than One Platform?

A:

How do I get my device data?

What are my rate, bandwidth, and other restrictions?

How often can devices report data?

What are some best practices for connecting devices based on use cases? <provide examples>

What if I want to add multiple values together and then store them into the timeseries service?
[How to modify the script…]

What protocols are supported?

When should I use the CBOR line protocol?

<see additional FAQs in Google doc draft: https://docs.google.com/document/d/1UrGz3FzbwlWdF3zFuWZqzwj46fRNmS3G5ni3K_7m0W0/edit>

How do I do firmware updates with new resources?

Data transformations

Alerts

Analytics Insights / Metrics (coming soon)

Throttling connections (coming soon)

Pricing

Best practices

What I can and can’t do with resources

How to use geolocation

Tracking devices / assets that move

Selling devices to customers (sensor as a service)

Other tutorials / user guide items

Can a OneP device and Okami device be part of the same product? How to migrate over
