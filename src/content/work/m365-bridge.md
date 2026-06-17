---
title: "m365-bridge"
role: "Solo build"
dates: "2026–present"
order: 5
summary: "Local MCP server that lets an AI assistant work safely inside my own Microsoft 365."
status: "In daily use"
---

m365-bridge is a local MCP server I built so an AI coding assistant can work inside Microsoft 365, reading a thread, drafting a reply, checking the calendar, instead of only talking about them from the outside. It runs entirely on my own machine, against a session I am already signed into.

The interesting problem was never the connection. It was the permission model: giving the agent least-privilege access scoped to exactly what a task needs and nothing more, so "help me with my inbox" never turns into "here are the keys to everything."

It reflects a conviction I keep coming back to: AI earns its place when it can reach into the tools people already work in, carefully, with the guardrails designed in from the start rather than bolted on after.
