# Features ported from the Kurenai branch

These came from [`sunhawken/openmw`, branch `claude/kurenai-no-pcsx2`](https://github.com/sunhawken/openmw/tree/claude/kurenai-no-pcsx2).

## Physics: Jolt instead of Bullet

The engine's physics, collision and navmesh-geometry code is Kurenai's Jolt Physics port (Jolt 5.3.0, fetched by CMake).

* `apps/openmw/mwphysics`, `components/detournavigator`, `components/physicshelpers`, `components/nifjolt` and the
  `PhysicsShape` resources replace their Bullet counterparts. `openmw-bulletobjecttool` is now `openmw-physicsobjecttool`.
* Ragdolls for dead actors, dynamic objects (items that bounce and roll), object buoyancy and grabbing. All are
  settings in the new Physics options tab (`[Physics]` in `settings.cfg`); ragdolls are off by default.
* The multiplayer hooks are kept: a server that disables actor collision still does, and the multiplayer `World` calls
  (weather, globals, inertial force, on-ground) work as before.
* Bullet is no longer a dependency. `CI/`, the Linux workflow and the Docker image are changed to match.

## Hair, cloth and body

* **Jiggle bones**: spring-driven breast and glute bones, with an auto-rig for bodies that have none, per-mesh Z offsets,
  per-actor policies (the player, listed NPCs), an NPC list, and a retarget panel with a walking preview.
* **Verlet hair and cloth** ("physics-verlet"): cloth chains on capes, skirts and hair, floating in water.
* **Options tabs**: Jiggle, Verlet and Appearance (head and hair, in game), with live previews. The Options menu can
  keep the world running while open (`options menu real time`).
* Lua: `types.NPC.setJiggleEnabled`, `isJiggleEnabled`, `setRaceScaleIgnored`, `isRaceScaleIgnored`.
* Humanoid transformation forms (Hazaeki, Drow, Mang'Chu) draw the form's body, head and hair over the player's gear.
* Documentation: `docs/openmw-verlet-cloth.md`.

## Performance

* Software **occlusion culling** (Intel's Masked Occlusion Culling; `occlusion culling` in `[Camera]`) and soft shadows.
  Off by default: it assumes a perspective camera, so try it before turning it on with the top-down view.
* Large-battle speed-up (animation level of detail for distant actors, a cap on physics catch-up steps).
* `TextureDownscaler` (caps texture resolution), crash fix for out-of-range skin indices, and per-phase timing of cell
  changes in the log.

## Other

* Console: `bat <file>` runs a file of console commands. Containers get a "Transfer All" button.
* Mouse-flick directional melee attacks (off unless `mouse directional attack` is set).
* Lua: a failing engine handler or update no longer needs a reload, and persistently failing handlers are throttled.
* Settings: missing defaults are written into the user's `settings.cfg`; keys this build doesn't know are kept.
* Runtime plugin cleaning of the "Evil GMSTs" (toggle), a global-map guard against corrupt cell coordinates, and a
  "Goodbye" that always ends a conversation.

## Not taken

* The Windows `ResumeLauncher`, Kurenai's own CI, and its unit tests for Jolt (they still use Bullet types, so the unit tests
  are not built).
* Kurenai's own art and meshes. The `curvy body meshes` and `curvy naked body` options are there (off by default) and
  use optional meshes from your own data if present.

## Fixed along the way

* A crash on exit: closing the last message box while the window manager was being destroyed reached overlays whose
  windows were already gone.
* `collision-shapes.yaml` is installed with the resources.
