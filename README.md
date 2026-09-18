# Kessler's Little Helper

A Unity project about orbital debris, an unsettling "mission," and a menu screen that works very hard to convince you both of those things are serious.

There are PDFs, audios and screenshots placed in the repo for further exploring if desired.

## Premise

***File below is classified under: DIRECTIVE 1104 · Roghbrol Gravitic Directorate · Orbital Remediation Command · Sol III***
> ACCESSING... PROJECT: UNTERRAPED MANKIND · Cert. GR-4407 · Tether Class II
> 
> Ah, humanity. Another civilization that has discovered rockets and immediately started throwing garbage into orbit. Failure upon failure in launching their spacecraft, and their decaying satellites are slowly sabotaging their own effort with the amount of junk now orbiting their planet.
>
> In the far side of Andromeda, the Roghbrol's greatest minds watched on. Humanity would mark the 5,329,164,291th race that fails to reach spacefaring technology; and oh how they've yearned for fellow intelligent species for millennias. Thus Roghbrol embarked on a new mission, dubbed:
>
> **"Project Un-Terra-pped Mankind."**
>
> You are a freshgraduate from Roghbrol's academy, holding the prestigious title of Space Fabric Engineer and Gravity Manipulator. You are now tasked to watch over Mankind's solar system and see if you could conspicuously direct large enough objects to influence the junk around the earth.
>
> P.S: Remember to not accidentally fling that into the planet itself. And make it seem like an accident. They're not ready for our presence.

The player is cast as an ancient, vastly superior alien intermediary — not a hero, not even really *for* humanity — quietly tidying Earth's orbital debris from behind the scenes. The comedy is purely written and intentionally never acknowledged: an incomprehensibly advanced species treats humanity's entire spacefaring effort as a rounding error, and the UI never once breaks character to let the player in on the joke.

## Design intent

This project is **not** primarily a gameplay prototype. At this stage the explicit goal has been **visual intentionality first** — proving out tone, scale, and identity before a single system of "the game" exists. The design brief organizes that intent into three deliverables, each with a "via":

1. **Keyword legibility** — the player should immediately think *"Space," "Vast," "Asteroids/Orbits," "Mission"*. Via: junk floating around Earth, and a technical, terminal-like interface as the main menu.
2. **The emotional experience** — the player should feel the scale, via audio and how small Earth looks against the Sun sharing the frame.
3. **The identity** — *"Alien. Sumn' ain't right."* Via: an alien glyph/flickering title, and a second, more subtle layer carried by audio.

Underneath all three sits one non-negotiable: **the player should experience the grandeur and seriousness of an epic science-fiction mission, while constantly being reminded that the mission itself is absurd.** Every visual and audio choice is filtered through "does this read as vast/grandiose/superior/high-stakes" *and* "does it still let the absurdity leak through"* Big, dim-one-source-light space sells the first half; the UI's deadpan bureaucratic tone (directive numbers, certification stamps, "Field Prototype" build labels) carries the second.

## Current state

**Implemented:** a fully art-directed main menu over a live, physically-simulated solar system background — the tone-and-scale groundwork above. **Not implemented:** the actual gameplay loop. `RESUME MISSION`, `NEW GAME`, `SETTINGS` are wired for navigation and selection, not for content.

### Main menu (`MainMenuCanvas`)
- Keyboard + mouse row selection, hover, confirm/stand-down flows (`MainMenuController.cs`).
- A dimmed layer of letterhead-style flavor text (faction lines, directive tag, build stamp) sitting well below full opacity so it reads as ambient world detail, not competing with the actual title/menu/controls.
- The title (`KESSLER'S LITTLE HELPER`) periodically glitch-flickers into an alien cipher font in short randomized bursts, staggered independently per line (`AlienScriptGlitch.cs`) — the closest thing this project has to a "the mission isn't what it says it is" tell, before any writing exists to say so directly.
- A faction insignia (`Roghbrol_Insignia`) as a quiet heraldic anchor in the top frame.

### Background (solar system)
- `CelestialBody.cs` — real inverse-square N-body gravity (`Gravity.Instance.simGravity * mass / distance²`) via `Rigidbody.AddForce`, not a scripted/faked orbit. Attractors (Sun, planets, moons) mutually pull on each other at O(k²) with k kept small; debris only falls toward attractors and never exerts gravity itself, so large debris fields stay cheap at O(n·k).
- Orbits are circularized at spawn (`CircularizeOrbit`) from an editor-only radius/angle setup so the scene can be authored by eye and still be physically self-consistent at runtime.
- `MainMenuCameraRig.cs` slowly orbits the menu camera around Earth at a fixed radius/elevation, tilted so Earth sits low in frame with the Sun sharing the shot — a framing decision made specifically to sell scale over a full-frame planet, and a direct answer to the brief's "remember Earth is the focal point" / "camera rotates around it" notes.
- Procedural ring geometry (`RingMeshGenerator.cs`) and custom shaders (`PlanetRing.shader`, `CloudShell.shader`) for rings and atmosphere, with real (period-appropriate, not to-scale) planet textures for Mercury through Neptune.

### Audio
- `KLH_Space_Rumble` — ambient bed (the brief's "quiet atmosphere, low rumbles and occasional whoosh").
- `KLH_MainMenu_Melody` — restrained menu theme.
- `KLH_UI_Navigate` — navigation feedback.

## Designed, not yet built: the core loop

The gameplay direction is defined but not implemented. Recorded here so the intent isn't lost between the visual pass and whenever systems work starts:

> The player manipulates celestial objects to capture and redirect Earth's orbital debris, constantly adjusting their trajectory to clean up humanity's mess without accidentally destroying the planet. As their abilities grow, they experiment with increasingly massive objects and develop more effective ways to manage the debris.
>
> — i.e., **gravity manipulation and orbital problem solving.**

Two nested loops:
- **Loop 1 (moment-to-moment):** nudge a celestial object → notice it's off-course → decide to correct its path → observe the consequence → decide if the result is useful → manipulate again.
- **Loop 2 (progression):** gain a level → get feedback → the game demonstrates the new effect (e.g. move a heavier body) → try the new power → refine strategy → back into Loop 1.

Planned interaction surface is deliberately narrow: celestial objects, and the map itself (drag & zoom) — "really, that's it." Open HUD/UX questions already flagged in design: whether to show orbital paths always-on or as a toggle (avoid clutter vs. avoid making the player guess what's interactable), an Earth "orbital cleanliness %" as the core win/lose gauge (100% coverage = doomed, 0% = clear), and whether leveling is communicated numerically or via earned titles.

## Visual & mood pillars

From the same design pass, the standing rules for anything added to the scene going forward:

- **Earth is the focal point** — everything else, including the Sun, frames it rather than competing with it.
- **One true light source** (the Sun) — no fill lighting that would flatten the "vast, dim" read.
- **Realism-style graphics** for a believable space, but a **limited, reusable visual vocabulary for asteroids/debris** rather than hand-authoring every unique shape — variation without one-off cost.
- **Roghbrol (and anything alien) should never read as silly** — powerful, ancient, advanced, unbothered by human concerns. Reference points from the brief: Enigma (Dota 2), Algalon (WoW), Archon (StarCraft 2), Nocturne (LoL), Unbidden (Stellaris) — cosmic-scale, not cute-alien. *NOT YET IMPLEMENTED*
- **Accurate per-planet size/order/coloring** so the solar system reads correctly without needing labels.
- **Quiet atmosphere** — restraint over spectacle in the sound design.

## Typography & licensing

Every font in the project ships under the **SIL Open Font License 1.1**, chosen deliberately over several tempting-but-murky "alien alphabet" fonts found on font aggregator sites (explicitly non-commercial, or with conflicting/unverifiable license claims across mirrors) — those were ruled out rather than risked.

| Font | Use | License |
|---|---|---|
| IBM Plex Mono | Technical/terminal UI text | OFL 1.1 (IBM Corp.) |
| Big Shoulders Display | Title display | OFL 1.1 (The Big Shoulders Project Authors) |
| Lucius Cipher | Alien glitch font | OFL 1.1 (GGBotNet) |

## Tech

- Unity 6, Universal Render Pipeline.
- TextMeshPro (merged into `com.unity.ugui`) for all UI text.
- New Input System for menu navigation and the debug fly camera.

## Credits

Built by... Me! Design direction and deliverables tracked in the team's shared design docs (storyboard, core-loop, and visual-pillar passes); this README summarizes the "why" behind what's in the scene so far.
