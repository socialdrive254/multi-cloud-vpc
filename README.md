# aws-multi-tier-vpc
This build showcases a private network on AWS that splits your application into three security zones — public (web), private (app), isolated (database) — spread across two data centres, so each component is only reachable by what legitimately needs to reach it, and the whole thing survives a data centre failure.
