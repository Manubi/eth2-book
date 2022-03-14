---
title: "Simple Consensus"
---

# {{ $frontmatter.title }}

In this section, we'll work to develop a simple consensus mechanism that can properly function under perfect conditions. After we've created this foundation, we'll introduce new problems and attack vectors and update our mechanism accordingly. For now, though, it's best to start with a simple core that can begin to explain the basics of consensus.

## Restating the goal

Let's briefly restate the goal of our mini consensus mechanism. We have a series of nodes (computers) that are interacting with one another over a network. We want to develop a system in which these nodes can talk to one another and come to an agreement (consensus) about the ordering of some set of "events". For now, let's not worry about what these events represent and simply assume that we want to come to agreement over their ordering.

## Key properties

### Safety

(description of safety)

### Liveness

(description of liveness)

## Assumptions

1. All nodes function perfectly (no crashes, no faults)
2. All communication is instant

## Protocol

1. Nodes randomly generate events and broadcast to network
2. Leader receives events
3. Leader proposes events to network in order received
4. All other nodes will accept these events immediately (no latency, can trust as soon as comes in)