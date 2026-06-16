# Cross-link snippet — paste into the cloud-honeypot-soc-lab README

You can't link two repos automatically, so add this short section to the
**cloud-honeypot-soc-lab** README (a good spot is just before "## Connect").
It tells visitors the lab has a downstream automation tool, which makes the two
projects read as one coherent body of work.

---

```markdown
## Downstream: Automated AI Triage

The telemetry this lab captures is the input to a companion open-source project,
**[AI-SOC-Triage](https://github.com/02William/ai-soc-triage)** — an AI-driven
SOC triage engine that automatically enriches, prioritizes, and explains the
exact failed-logon and brute-force activity collected here.

The two projects form a complete pipeline:

`honeypot (this repo) → KQL export → AI-SOC-Triage → ranked, explained incidents`

The detection patterns observed in this lab — IP reuse, common-username
targeting, brute-force waves — are encoded directly as behavioural signals in
the triage engine (password spraying, repeat-offender IPs, brute-force-then-breach).
```

---

After pasting, update the link if you name the triage repo something other than
`ai-soc-triage`.
