---
title: Map Porting
categories:
  - guide
tags:
  - mapping
  - tools
weight: 2
---

# Introduction

This guide gives an overview of all the steps required to officially port a map to Momentum Mod.  
The goal of porting is to make minimal changes necessary for the map to function.

You are free to port any publically avaiable map with some exceptions:
- Mappers can reserve/block porting of their maps using [this form](https://docs.google.com/forms/d/e/1FAIpQLSeheNDY5A960u6GtXCHtt3s_2vZJL3o5tMJ_ZNbYOpb6cx5nQ/viewform).
- Before porting make sure the map isn't on [this spreadsheet](https://docs.google.com/spreadsheets/d/1KHeWfhGUNpN267CXtPvVdf2h7eQbjPUhWVkE5NimYhg/edit?gid=2051215588#gid=2051215588).
- When in doubt, contact the mapper ( please don't spam them )

# Setup
If you have any questions feel free to ask for help in #map-porting channel on our [Discord](https://discord.gg/momentummod)!

1. Download [Lumper](https://github.com/momentum-mod/lumper), we will use it to modify the map
2. Download the map you want to port (maps in **.bz2** format can be extracted using [7zip](https://www.7-zip.org/)):
    - [fastdl.me](https://main.fastdl.me/69.html) - Contains a huge collection of Surf, Bhop, and KZ maps
    - [ksf.surf](https://ksf.surf/) (preferred for Surf) - Main hub for competitive surf (**.bz2** format)
    - [jump.tf](https://jump.tf/forum/) (preferred for RJ/SJ) - Main RJ/SJ forum
3. Put the map (**.bsp**) in **/momentum/maps** folder
    - You can access it by right clicking Momentum Mod in your steam library and selecting Manage → Browse local files
4. Open the map using [Lumper](https://github.com/momentum-mod/lumper)
5. Open the map in Momentum Mod by opening the console (**~** by default, key below ESC) and typing `map <map name>`
6. Open the console again and enable cheats (`sv_cheats 1`) as well as Lumper synchronization (`mom_lumper_sync 1`)
7. Click the "Connect to Game Sync" button in Lumper
![Lumper Example](/images/map_porting/connect_to_game_sync.png)

You are now fully set up to start porting the map!


# Required Modifications

## Entities

### Entity Review

Lumper's entity review tool will point out entities that are not supported by Momentum, or should be replaced with a different entity.

If an entity is marked as **Invalid** or **Warning**, it will generally have comments explaining the issue and what to replace it with. Invalid entities **must** be replaced or removed, while warnings are suggestions that are worth reviewing.

Pressing the "Edit" button on the right side of an entity in the Entity Review page takes you to the Entity Editor, and filters the list by that entity's classname. If you're not sure of the entity's purpose, teleporting in-game using [Game Sync](#game-sync) is helpful. You can also press the **VDC Reference** button on a page for that entity to see its docs on the Valve wiki. And if in doubt, please ask for help in the _#map-porting_ channel on Discord!

### Boost Ramps

`trigger_push` entities that push the player into a ramp are very inconsistent and give different speeds depending on how you jump into it. Replace these with `trigger_setspeed` for a more consistent and less exploitable boost.

Running directly into a trigger_push and getting boosted by it will automatically record your resulting horizontal and vertical speed in the Entity Tools window. Clicking "Convert to Set Speed" will then perform the conversion, applying the best settings to replicate this boost consistently. The resulting `trigger_setspeed` may have a horizontal speed that differs from the original trigger, but in combination with the automatically detected vertical speed, the original boost is replicated accurately.

![Ramp Boost](/images/map_porting/setspeed.png)

### Rapidly Reactivating Boost Triggers

The player can sometimes activate a boost multiple times in quick succession with problematic techniques, including "crouchboosting". Different styles of boost triggers require different adjustments to fix this issue:

1. **Jump-based Boosts** - Boosts that are designed such that the player should be boosted when they jump on them should be converted into a `trigger_multiple` that boosts the player with an `OnJump` output, such as `OnJump !activator,AddOutput,basevelocity # # #`.

![Floor Boost](/images/map_porting/floor_boost.png)

2. **Constant Push Floor Boosts** - If the boost is `trigger_push` based and it is important that the player is continuously pushed while inside it (not just when they jump or exit the trigger), you can leave it as a `trigger_push`, but use a `filter_momentum_surface_collision` filter with the "Touching standable surfaces" option so the boost only applies when the player is on the ground. You should only use this option if the floor is completely flat, otherwise the boost will re-apply every time the player touches another surface. If you have the original VMF and can recompile the map, removing the trigger and replacing the surface below it with a `func_conveyor` is also an option.

3. **Surf Ramp Boosts and Other Airborne Boosts** - When there is no ground for the player to jump on, a boost can be fixed by temporarily disabling itself on exit, so the player has to wait before re-activating the trigger. This is achieved using `OnEndTouch` outputs, e.g. `OnEndTouch !self,Disable,,0` and `OnEndTouch !self,Enable,,1`. The Cooldown option in Entity Tools lets you easily create and configure this for existing triggers.

![Surf Ramp Boost](/images/map_porting/surf_ramp_boost.png)

### Jump Boosts

Boosts that should boost the player when they jump should use the new `OnJump` output instead of `OnEndTouch`. In addition to avoiding crouchboost exploits as mentioned previously, this also prevents other exploits. This goes for both basevelocity-based and gravity-based boosters.

![OnJump Boost](/images/map_porting/onjump_boost.png)

Some upward boosts briefly reverse the player's gravity instead of using basevelocity. This method may have a gradual and potentially awkward acceleration period. In particularly bad cases, consider converting these to the snappier basevelocity style. Note that the map geometry is sometimes built around the gravity acceleration period in a way that basevelocity cannot replace, so be sure to test this change well before committing it.

### Drop Teleports

Some maps, most notably surf maps, teleport the player into a floating "cage" that is open on the bottom to reset the player's velocity after they fail or transition between stages.

![Drop Teleport](/images/map_porting/drop_tele.png)

Momentum Mod introduces the "Keep Negative Z Velocity Only" velocity mode option for `trigger_teleport` that removes any horizontal velocity or upward vertical velocity from entities when they are teleported. This is more robust than only relying on the cage, but more importantly, this activates some important behaviors in Momentum Mod:

- When teleporting directly into a stage zone, it keeps the zone from activating until the player lands on the ground. This results in more competitively correct splits.
- It prevents players from using air acceleration until they land on the ground, preventing an unfavorable technique to gain speed early.

{{< hint info >}} These behaviors only activate when there is standable ground below the teleport destination, but you should still use "Keep Negative Z Velocity Only" for caged teleport destinations that are not above standable ground. {{< /hint >}}

All teleports with caged teleport destinations should use this velocity mode. In the in-game Entity Tools, open the "Teleport Velocity Mode" dropdown, select the destination that you want to change to a drop teleport, and then select the "Keep Negative Z" radio button.

![Velocity Mode Tools](/images/map_porting/velocity_mode.png)

### Jail Timers

Some old surf maps use a `logic_timer` to teleport all players to a jail after a few minutes. These timer entities and the associated teleport triggers should be removed using Lumper:

![Lumper Timer](/images/map_porting/lumper_timer.png)

![Lumper Teleports](/images/map_porting/lumper_teleports.png)

### Fix Landmark Teleport Angles

{{< hint warning >}} TODO: Are we really keeping this? Doesn't new behaviour make pretty redundant? If so could show Entity Tools usage {{< /hint >}}

- Our trigger_teleport logic uses CS:GO's landmark teleport logic, which is different from older games. It is backwards compatible with older games, but achieving compatibility may require simple adjustments to entity props.
- Landmark teleports originally made for older games may need to be changed to make the angles of the teleport destination and landmark entity match, and disable `UseLandmarkAngles` as well.

### func_button

Many maps in TF2 use the `OnDamaged` output on buttons. In Momentum Mod, `OnDamaged` fires once per instance of damage. This means a button using this output will fire up to 9 times when shot by a shotgun.  
If this causes unintended effects (such as incrementing a `math_counter` too many times), then the following changes will make the button fire the output only once upon being shot:

- Enable the `Damage Activates` flag if not already enabled (by adding 512 to `spawnflags`)
- Change `OnDamaged` outputs to `OnPressed`

If buttons are taking forever to reset or moving when they shouldn't be, enable the `Don't Move` spawnflag as well (by adding 1 to `spawnflags`).

### Moving Brushes

Some maps have moving brushes which have cycles that are too fast to be hit consistently which effectively introduces RNG to competitive runs (e.g. surf_fruits stage 5 / strawberry). If there's community consensus on this (existing servers usually already have that), they should be frozen or deleted.

### Collectibles

Complicated collectible system triggers can be converted to [Momentum's collectible system entities](/guide/collectibles/). This is far less complicated and, facilitates future collectibles HUD work.

If a map's collectibles amount to simply hitting several triggers in arbitrary order, it may work best to just zone with unordered checkpoints. This provides practically the gameplay, and is clearer in game UI!

### Stripper Configs

Community servers often use Stripper configs for tweaking maps at runtime. Lumper can permanently apply the config using the "Stripper Config (File)" job, but **_do not blindly apply configs you find online_**! These configs can be useful as a reference, but it's almost always better to apply changes manually by editing the entity lump; many of the changes are not applicable to Momentum.

- Tempus (RJ/SJ) https://github.com/waldotf/tempus_stripper_code

## Textures

Maps will sometimes contain textures that we don't want to include:

- Pornography, racism, or other "edgy" content
  - If something seems questionable and you're not sure about it, ask in Discord.
- Obvious copyrighted assets from other media
  - See [map submission guidelines](/guide/map_submission/map_submission/#other-copyright-assets)
  - This can be hard to make a judgment call on. Just look out for anything _extremely_ glaring.
  - Again, ask in Discord if you're unsure.

Lumper's **Texture Browser** is a fast way to quickly review all textures in the map, and can be used to replace bad textures with placeholders.

![Lumper Texture Browser](/images/map_porting/lumper_texture_browser.png)

The easiest way to replace a texture is to modify any VMT files that refer to the VTF file in question.

- Find any instances of VMTs files that refer to that VTF file in the Pakfile Explorer
  - Currently we don't have automation for this, but it's usually relatively obvious
- Replace the `$basetexture` value (maybe also `$bumpmap` and others) with some other texture on the map that won't look out-of-place
- Remove the original VTF file via Texture Browser / Pakfile Explorer

{{< hint info >}} Source engine textures are stored in **VTF** files, which are the image files, and **VMT** files, which are the material definitions for those textures.

VMTs are text files that define how the texture is used in the game, such as its shader type, properties, and other settings. VMTs are _usually_ stored in the same directory as the VTF files, and have the same name as the VTF file, but with a `.vmt` extension.

Note that multiple VMT files can refer to the same VTF file, so you may need to check multiple VMTs if the texture is used in multiple places. {{< /hint >}}

## Packed Game Assets

The pakfile lump of many maps contain copyrighted Valve assets, which we [cannot include](/guide/map_submission/map_submission/#source-assets).

Lumper contains a hash-based manifest file of all these assets, so removing them is very straightforward. You just need to run the **Remove Game Assets** job.

![Remove Game Assets](/images/map_porting/lumper_remove_assets.png)

## Sounds

The game allows players to set a volume level for each of several sound channels. Sound files packed in each map should be organized into specific folders corresponding to these sound channels:

| Channel  | Folder         |
| -------- | -------------- |
| Ambient  | sound/ambient/ |
| Music    | sound/music/   |
| Movement | sound/player/  |
| Weapons  | sound/weapon/  |
| UI       | sound/ui/      |

Lumper makes this process easy by automatically detecting entities and soundscapes that use these sounds (in fact it works for all assets!) and updating their paths. Simply drag-and-drop the sounds you want to move into the appropriate folder, then press "Yes" on the dialog that appears.

![Lumper Sound Moving](/images/map_porting/lumper_sound_moving.png)

_Note: Path refactoring is very technically complex and has had issues in the past, so it's worth checking logs make sense and testing in game_

## Limited Ammo Triggers

Maps ported from TF2 for RJ and SJ sometimes have sections which limit the player's ammo. If infinite ammo throughout the map would significantly change the gameplay experience, consider adding ammo triggers to the map. Information concerning the ammo system in Momentum Mod can be found at [Ammo System](/guide/mapping/ammo_system).

Certain triggers can be added to a bsp file without having to recompile the map with the help of generated stripper configs that can be executed in Lumper. Tools available to help create these stripper configs include [vmf_to_stripper](https://github.com/benjl/vmf_to_stripper/) and [zoneToTrigger](https://github.com/Natanxp2/zoneToTrigger).

# Common Issues Porting Older Maps

When porting maps from older Source engine versions, there are a few issues that might come up due to incompatibilities with Strata Source.

## HDR Skyboxes

Skyboxes will sometimes fail to load in maps compiled with HDR. This is because the "sky" shader defaults to HDR, but will fail to load if the textures are not HDR. This issue can be fixed by setting the shader to "Sky_SDR".

![HDR Skybox](/images/map_porting/hdr_skybox.png)

![VTF Edit Sky SDR](/images/map_porting/vtfedit_sky_sdr.png)

## Corrupt HDR Cubemaps

Some maps from CS:S have corrupted HDR cubemaps in Momentum. If you notice this, you can try find working cubemaps from the CS:GO version. In cases we've seen they've been flat black textures, so you can replace HDR version with these VTFs (TODO: add these). [See this Github issue for discussion](https://github.com/momentum-mod/game/issues/2315#issuecomment-3097105058).

![Corrupt Cubemaps](/images/map_porting/corrupt_cubemaps.png)

## Dark Refraction Textures

Refraction textures in most maps do not render correctly. The cause of this issue is unknown and the only way to fix it currently is to recompile.

![Dark Refraction Textures](/images/map_porting/refraction_dark.png)

![Fixed Refraction Textures](/images/map_porting/refaction_fixed.png)

## Invalid VMT Files

Strata Source has stricter VMT parsing rules and will not load VMTs with syntax errors. These invalid VMT files must be manually fixed in Lumper or VTFEdit in order for the textures to load in game.

![Invalid VMT](/images/map_porting/invalid_vmt.png)

![Invalid VMT Lumper](/images/map_porting/invalid_vmt_lumper.png)

## Missing Shadows on CS:GO Maps

Some CS:GO maps use cascaded shadow maps (CSM) to create more detailed shadows. If these maps do not use an `env_cascade_light` entity, then these detailed shadows will not display in Momentum Mod.

![Missing CSM Entity](/images/map_porting/csm_broken.png)

![Working CSM](/images/map_porting/csm_working.png)

In order to fix this, add a `env_cascade_light` with Lumper:

![CSM Lumper](/images/map_porting/csm_lumper.png)