# Algorithmic Transparency and User Control

> See [moves README](README.md) for orientation. This is the seventh design move.

If there is any ranking beyond chronological, it is open-source, inspectable, and selectable by the user. Users can see what the system has inferred about them and can correct or delete it.

Concrete features:

- A *show me how this feed was assembled* affordance.
- Multiple ranking algorithms the user chooses between (chronological, "most replied to", "marked as resolved", curator picks).
- No implicit personalisation.

- *Why:* Opaque algorithmic governance is a form of arbitrary lord-rule.
- *Cost:* Default-chronological feeds underperform engagement-ranked feeds on conventional retention metrics. Most users will not configure their algorithm and will get a less convenient experience than personalisation would give them.
