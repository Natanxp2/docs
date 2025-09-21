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

You are free to port any publically available map with some exceptions:
- Mappers can reserve/block porting of their maps using [this form](https://docs.google.com/forms/d/e/1FAIpQLSeheNDY5A960u6GtXCHtt3s_2vZJL3o5tMJ_ZNbYOpb6cx5nQ/viewform).
- Before porting make sure the map isn't on [this spreadsheet](https://docs.google.com/spreadsheets/d/1KHeWfhGUNpN267CXtPvVdf2h7eQbjPUhWVkE5NimYhg/edit?gid=2051215588#gid=2051215588).
- When in doubt, contact the mapper ( please don't spam them )

# Setup
If you have any questions feel free to ask for help in #map-porting channel on our [Discord](https://discord.gg/momentummod)!

1. Download [Lumper](https://github.com/momentum-mod/lumper), we will use it to modify the map
2. Download the map you want to port (maps in **.bz2** format can be extracted using [7zip](https://www.7-zip.org/)):
    - [fastdl.me](https://main.fastdl.me/69.html) - Contains a huge collection of Surf, Bhop, and KZ maps
    - [ksf.surf](https://ksf.surf/) (preferred for Surf) - Main hub for competitive surf
    - [jump.tf](https://jump.tf/forum/) (preferred for RJ/SJ) - Main RJ/SJ forum
3. Rename the map
    - Some map names include version info like _a13, _b2, _njv etc. Rename the **.bsp** file to remove them
    - RJ/SJ maps use **jump_** prefix. This should be changed to **rj_** or **sj_** depending on for which gamemode the map was originally made 
4. Put the map (**.bsp**) in **/momentum/maps** folder
    - You can access it by right clicking Momentum Mod in your steam library and selecting Manage → Browse local files
5. Open the map using [Lumper](https://github.com/momentum-mod/lumper)
6. Open the map in Momentum Mod by opening the console (**~** by default, key below ESC) and typing `map <map name>`
7. Open the console again and enable cheats (`sv_cheats 1`) as well as Lumper synchronization (`mom_lumper_sync 1`)
8. Click the "Connect to Game Sync" button in Lumper
![Lumper Example](/images/map_porting/connect_to_game_sync.png)

You are now fully set up to start porting the map!

# Porting
## Step 1: Take Screenshots
1. Fly around the map in noclip (**g** by default, `noclip` in console) to find good spots to screenshots
    - You can use maximum of 5 screenshots, 1 for the thumbnail, 4 for the gallery
2. Use `mom_official_screenshot` in console to take screenshots. This command will automatically apply all relevant settings.  
    - Screenshots will be saved to **/momentum/screenshots**
    - You can bind the screenshot command to a key for ease of use in keybind settings or with console by typing `bind <key> mom_official_screenshot`
    
{{<hint warning>}}
If you notice broken reflections while taking screenshots, [cubemaps](/guide/map_submission/map_porting/#cubemaps) section of this guide will help with fixing them
{{< /hint >}}
    
{{< hint info >}}

Some mappers provide their own screenshots on [gamebanana](https://gamebanana.com/) (Surf, Bhop, KZ) or [jump.tf](https://jump.tf/forum/) (RJ/SJ).  
While you may not use those screenshots directly, feel free to recreate them using the steps above.

{{< /hint >}}
    
## Step 2: Remove Valve Assets
Maps may include packed materials, models, and other asset ( similar to a **.zip** file)  
Momentum Mod [cannot legally redistribute](/guide/map_submission/map_submission/#source-assets) Valve's assets.  
Valve's assets may still be used on maps however it is necessary to remove them from the **.bsp**.   

1. Go to 'Jobs' tab in Lumper
2. Add "Remove Game Assets" job
3. Run the job
![Remove Game Assets](/images/map_porting/lumper_remove_assets.png)

{{< hint info >}}

Momentum Mod automatically mounts assets from CS:S, CS:GO, and Portal 2 so users with those games installed will still be able to see them even after removal  
In case the map you're porting contains a small amount of assets from other source games you may leave them packed by unticking relevant checkboxes  
Use the 'Texture Browser' tab in Lumper to see which game each asset comes from

{{< /hint >}}

## Step 3: Modify Remaining Assets and Fix Cubemaps
### Textures
1. Go to the 'Texture Browser' tab and right click → remove any explicit material or obvious copyrighted assets you may find  
2. Consider resizing/reencoding any assets that take a lot of space (marked by yellow/red)  
    - If the texture is at or above 2048x2048 it consider resizing it down a level  
    - If the texture uses an uncompressed format consider reencoding it  
    - This should be evaluated on a case-by-case basis, if you are unsure whether you should modify the textures, leave them as they are  

![4096_asset](/images/map_porting/4096_asset.png)
![4096_asset](/images/map_porting/4096_asset_details.png)
    
{{< hint info >}}
Compressed formats are **DXT1**, **DXT5**, and **BC7**  
When reencoding use **DXT1** for smallest filesize but **only** if the texture uses an on/off alpha channel ( no gradient ), otherwise use BC7  
Do **NOT** resize or reencode any animated textures or textures with multiple faces  
  
Please verify in game that modified textures are not broken and still looks good. 
{{< /hint >}}

### Sounds
Sounds in Momentum Mod need to be categorized properly for volume sliders to work
1. Go to 'Pakfile Explorer' in Lumper
2. Scroll to the bottom to see if **/sound** folder exists
3. If it does, listen to every sound and categorize them according to the following table
    - You can use 'Open in External Program' button or right click → export the entire folder to listen to the sounds
    - Right click on **/sound** → Create Directory , then drag and drop sounds into appropriate folders. Click **yes** on the pop-up
    
| Channel  | Folder         |
| -------- | -------------- |
| Ambient  | sound/ambient/ |
| Music    | sound/music/   |
| Movement | sound/player/  |
| Weapons  | sound/weapon/  |
| UI       | sound/ui/      |

### Cubemaps
If you renamed the map during the [setup](/guide/map_submission/map_porting/#setup), reflections might be broken.
1. Go to 'Pakfile Explorer' in Lumper
2. Check if the map has **/materials/maps/<old_map_name>** folder
3. If it does right click → Rename that folder to the new map name. Click **yes** on the pop-up
![rename_cubemaps](/images/map_porting/rename_cubemaps.png)

## Step 4: Fix Entities
1. Go to the 'Entity Report' tab in Lumper.
2. Remove all **invalid** entities
    - Clicking the edit button on the right will bring you to 'Entity Editor'. You may delete the entity there
3. Fix other entities by following the comments in Lumper and guides listed below
    - Some **warnings** can be fixed, some will need to be removed, and some can stay unchanged.
    - Thanks to game sync, you can teleport to any entity simply by clicking the button next to it in 'Entity Editor' tab

| Boosters                                                                    | Logic                                                             | Other                                                                       |
| --------------------------------------------------------------------------- | ----------------------------------------------------------------- | -------------------------------------------------------------------------   |
| [trigger_push](/guide/map_submission/fixing_entities/#trigger_push)         | [logic_auto](/guide/map_submission/fixing_entities/#logic_auto)   | [trigger_teleport](/guide/map_submission/fixing_entities/#trigger_teleport) |
| [trigger_multiple](/guide/map_submission/fixing_entities/#trigger_multiple) | [logic_timer](/guide/map_submission/fixing_entities/#logic_timer) | [func_button](/guide/map_submission/fixing_entities/#func_button)           |
| [trigger_catapult](/guide/map_submission/fixing_entities/#trigger_catapult) |                                                                   |                                                                             |




# UNFINISHED STUFF BELOW


### Moving Brushes

Some maps have moving brushes which have cycles that are too fast to be hit consistently which effectively introduces RNG to competitive runs (e.g. surf_fruits stage 5 / strawberry). If there's community consensus on this (existing servers usually already have that), they should be frozen or deleted.

### Collectibles

Complicated collectible system triggers can be converted to [Momentum's collectible system entities](/guide/collectibles/). This is far less complicated and, facilitates future collectibles HUD work.

If a map's collectibles amount to simply hitting several triggers in arbitrary order, it may work best to just zone with unordered checkpoints. This provides practically the gameplay, and is clearer in game UI!

### Stripper Configs

Community servers often use Stripper configs for tweaking maps at runtime. Lumper can permanently apply the config using the "Stripper Config (File)" job, but **_do not blindly apply configs you find online_**! These configs can be useful as a reference, but it's almost always better to apply changes manually by editing the entity lump; many of the changes are not applicable to Momentum.

- Tempus (RJ/SJ) https://github.com/waldotf/tempus_stripper_code

## Textures

The easiest way to replace a texture is to modify any VMT files that refer to the VTF file in question.

- Find any instances of VMTs files that refer to that VTF file in the Pakfile Explorer
  - Currently we don't have automation for this, but it's usually relatively obvious
- Replace the `$basetexture` value (maybe also `$bumpmap` and others) with some other texture on the map that won't look out-of-place
- Remove the original VTF file via Texture Browser / Pakfile Explorer

{{< hint info >}} Source engine textures are stored in **VTF** files, which are the image files, and **VMT** files, which are the material definitions for those textures.

VMTs are text files that define how the texture is used in the game, such as its shader type, properties, and other settings. VMTs are _usually_ stored in the same directory as the VTF files, and have the same name as the VTF file, but with a `.vmt` extension.

Note that multiple VMT files can refer to the same VTF file, so you may need to check multiple VMTs if the texture is used in multiple places. {{< /hint >}}


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