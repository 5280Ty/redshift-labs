# Pop!_OS 22.04 to 24.04 Upgrade Attempt
2025-05-15

With Pop!_OS 24.04 and COSMIC on the horizon, I tested whether a manual upgrade from 22.04 to 24.04 was viable.

### Summary

- Replaced `jammy` with `noble` in all APT sources.
- Ran a full upgrade with package updates.
- Hit multiple dependency issues (`pop-desktop`, `pop-session`, and `libpop-launcher`).
- COSMIC packages weren’t ready or installable via upgrade.
- Reverted back to 22.04 and stabilized the system.

### Outcome

- System survived rollback. Nothing critical lost.
- In-place upgrades to 24.04 are not viable at this time.
- Will perform an upgrade (or a clean install if absolutely needed) when COSMIC reaches a stable release and System76 provides official support.

### Why This Matters

Documenting dead-ends is just as important as successes. This test confirmed that 24.04 isn't ready for upgrades, at least not for Pop!_OS users running 22.04. The OS stack isn't aligned yet, and forcing it creates more problems than it solves.

---

Incremental steps, logged. This sits alongside other `/daily-driver` system changes.
