# Collecting Metrics with Graphite

Doug Bell
Chicago Perl Mongers

------

# Metrics

---

# System Usage

CPU, Memory, I/O

---

# Application Usage

Active users, Cache misses, Database writes

---

# Finance

Security prices, Rates

---

# Time Series

---

# Timestamp -> Value

---

# Useful for...

---

# Monitoring

---

# Capacity Planning

---

# Usage Billing

------

# Graphite

<http://graphite.readthedocs.org/>

---

# Metrics Platform

---

# Python

Yes, I know where I am 🐍

---

# 3 Parts

---

# Carbon

Networking and Stuff

---

# Whisper

Database

---

# Graphite-Web

Data visualizer

---

# Scales Easily

Just run more Carbon daemons

------

# Installation

`pip` `pip`

---

```
pip install carbon
pip install whisper
pip install graphite-web
```

<http://graphite.readthedocs.org/en/latest/install.html>

---

# YMMV

I 💔⛔️☠☹  Python's Toolchain

---

# Graphite-Web Optional

😍 Grafana is much, much nicer

------

# Configuration

---

# Metrics

`modem.ef67abc19.bandwidth.upload`
`modem.ef67abc19.bandwidth.download`

---

# Metrics Tree

`modem.ef67abc19.bandwidth.upload`
`modem/ef67abc19/bandwidth/upload.wsp`

---

# Retention

---

# Period : Age

---

# Every 5 minutes for 3 days

---

# 5m:3d

---

# Data Schema

`storage-schemas.conf`

---

```
[default]
pattern = .*
retentions = 5m:3d
```

---

# Multiple retentions

---

```
[bandwidth]
pattern = [.]bandwidth[.]
retentions = 5m:7d,1d:1y,7d:50y
```

Older data is less precise

------

# Writing Custom Data

Finally, some 🐪!

---

# Plain Text

```
# <stat> <value> <timestamp>
modem.ef67abc19.bandwidth.upload 6000 1454019615
```

---

# Basic Socket

```perl
use IO::Socket::IP;
my $sock = IO::Socket::IP->new(
    PeerHost => 'localhost',
    PeerPort => 2003,
);
```

---

# Write Stat

```perl
print { $sock } "modem.ef67abc19.bandwidth.upload 6000 1454019615\n"
    or warn "Couldn't write: $!";
```

------

# Aggregations

---

# Aggregate series

---

# On-the-fly

---

```
# <output> (<interval>) = <input>
modem.all.bandwidth.<ud> (60) = modem.<id>.bandwidth.<ud>
```

---

# Different functions

* Avg
* Sum
* Min
* Max

Configured in `storage-schemas.conf`

------

# Grafana

💖 Better Data Visualizer!

---

# Live Demo?

------

# Scaling

---

# Tweak `carbon.conf`

Scale vertically

---

# `MAX_CACHE_SIZE`

How much to hold in memory

---

# `MAX_UPDATES_PER_SECOND`

Writes to disk

---

`carbon-relay.py`

Scale horizontally

---

# More Processes == Faster!

---

# Relay to Forward

---

# Multiple Cache Processes

```
[cache:a]
LINE_RECEIVER_PORT = 2103
PICKLE_RECEIVER_PORT = 2104
CACHE_QUERY_PORT = 7102

[cache:b]
LINE_RECEIVER_PORT = 2203
PICKLE_RECEIVER_PORT = 2204
CACHE_QUERY_PORT = 7202
```

---

# One Relay Process

```
[relay]
LINE_RECEIVER_INTERFACE = 0.0.0.0
LINE_RECEIVER_PORT = 2003
PICKLE_RECEIVER_INTERFACE = 0.0.0.0
PICKLE_RECEIVER_PORT = 2014
RELAY_METHOD = consistent-hashing
DESTINATIONS = 127.0.0.1:2104:a, 127.0.0.1:2204:b
```

---

# One Machine

---

# Many Machines

---

# Relay Servers

Only `carbon-relay.py`

---

# Database Servers

`carbon-relay.py` and `carbon-cache.py`
Max `iostat`

------

# Questions?

Slides: <http://github.com/preaction/Collecting-Metrics-With-Graphite/>

