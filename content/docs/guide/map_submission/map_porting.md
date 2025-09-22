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
If you have any questions feel free to ask for help in **#map-porting** channel on our [Discord](https://discord.gg/momentummod)!

1. Download [Lumper](https://github.com/momentum-mod/lumper), we will use it to modify the map
2. Download the map you want to port (maps in **.bz2** format can be extracted using [7zip](https://www.7-zip.org/)):
    - [fastdl.me](https://main.fastdl.me/69.html) - Contains a huge collection of Surf, Bhop, and KZ maps
    - [ksf.surf](https://ksf.surf/) (preferred for Surf) - Main hub for competitive surf
    - [jump.tf](https://jump.tf/forum/) (preferred for RJ/SJ) - Main RJ/SJ forum
    - [femboy.kz](https://files.femboy.kz/fastdl/csgo/maps/) - CS:GO KZ maps
    {{<hint info>}}
    Porting for gldsrc gamemodes (Conc, HL1Bhop, 1.6KZ) as well as Defrag is way more complicated and requires a different approach.  
    Please follow the [gldsrc porting guide](/guide/mapping/porting_goldsrc_to_source/) and the [Quake 3 porting guide](/guide/mapping/porting_quake3_to_source/). 
    {{</hint>}}
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

![Moving Sounds](/images/map_porting/lumper_sound_moving.png)



### Cubemaps
If you renamed the map during the [setup](/guide/map_submission/map_porting/#setup), reflections might be broken  
You can skip this step if you didn't rename the map  
1. Go to 'Pakfile Explorer' in Lumper
2. Check if the map has **/materials/maps/<old_map_name>** folder
3. If it does right click → Rename that folder to the new map name. Click **yes** on the pop-up
![Rename Cubemaps](/images/map_porting/rename_cubemaps.png)

## Step 4: Fix Entities
1. Go to the 'Entity Report' tab in Lumper.
2. Remove all **invalid** entities
    - Clicking the edit button on the right will bring you to 'Entity Editor'. You may delete the entity there
3. Fix other entities by following the comments in Lumper and guides listed below
    - Some **warnings** can be fixed, some will need to be removed, and some can stay unchanged.
    - Thanks to game sync, you can teleport to any entity simply by clicking the button next to it in 'Entity Editor' tab

| Boosters                                                                                    | Logic                                                             | Other                                                                       |
| ------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- | --------------------------------------------------------------------------- |
| [trigger_push](/guide/map_submission/fixing_entities/#trigger_push-and-trigger_multiple)    | [logic_auto](/guide/map_submission/fixing_entities/#logic_auto)   | [trigger_teleport](/guide/map_submission/fixing_entities/#trigger_teleport) |
| [trigger_multiple](/guide/map_submission/fixing_entities/#trigger_push-and-trigger_multiple)| [logic_timer](/guide/map_submission/fixing_entities/#logic_timer) | [func_button](/guide/map_submission/fixing_entities/#func_button)           |
| [trigger_catapult](/guide/map_submission/fixing_entities/#trigger_catapult)                 |                                                                   | [trigger_gravity](/guide/map_submission/fixing_entities/#trigger_gravity)   |


## Step 4.5: Other Fixes
Vast majority of maps will **not** require any of the fixes covered in this section.  
These are still common enough though, that they need to be covered in this guide.  
Please look through them but more likely than not you will be ready to move on to [Step 5](/guide/map_submission/map_porting/)


### Old Bhop platforms
Some old bhop maps use **func_button** or **func_door** for bhop platform. These should be converted to **func_bhop**
1. Open entity tools by typing `devui_show entitytools` in console
2. Open the 'Bhop Block Fix' section
    - If the number of 'Bhop Blockfix Entities' is **0** you don't need to fix anything
3. Make sure the checkbox is ticked ( it should be by default )
4. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)

### Stripper Configs
Community servers sometimes apply server side fixes to maps ( mainly applicable to Rocket Jump / Sticky Jump )  
You can apply them permanently to the **.bsp** with Lumper

- [Tempus ( Rocket Jump / Sticky Jump )](https://github.com/waldotf/tempus_stripper_code)

{{<hint danger>}}

While these are sometimes useful, **a lot of them** are not applicable to Momentum Mod  
Read through them carefully before deciding to apply them  
If you're not sure if you should use them, please ask in **#map-porting** channel on our [Discord](https://discord.gg/momentummod)

{{</hint>}}

1. Download the **.cfg** file
2. Go to 'Jobs' tab in Lumper
3. Add the 'Stripper (File)' job
4. Provide the path to your downloaded **.cfg** and run the job
![Download Stripper Config](/images/map_porting/download_stripper_config.png)
![Apply Stripper Config](/images/map_porting/apply_stripper_config.png)

### Invalid VMT Files
Momentum Mod uses stricter parsing rules than other source games  
Fix **.vmt** of broken textures using the 'Pakfile Explorer' in Lumper
![Invalid VMT](/images/map_porting/invalid_vmt.png)
![Invalid VMT Lumper](/images/map_porting/invalid_vmt_lumper.png)

### Missing Skyboxes
Skyboxes will sometimes fail to load in maps compiled with HDR.
1. Go to 'Pakfile Explorer' tab
2. Find the **.vmts** of the skybox
    - It will be in **/materials/skybox**
    - There will be 6 pairs of **.vmt** and **.vtf**, one for each side
3. Open every **.vmt** and change the first line to **"Sky_SDR"** (including quotes)  
TODO: GET A PROPER LUMPER SCREENSHOT FOR THIS. WHAT IS THE MAP BELOW?
![VTF Edit Sky SDR](/images/map_porting/vtfedit_sky_sdr.png)
![HDR Skybox](/images/map_porting/hdr_skybox.png)

### Missing Shadows on CS:GO Maps
Some CS:GO maps use cascaded shadow maps (CSM) to create more detailed shadows  
In Momentum Mod **env_cascade_light** entity must exist for them to display properly

1. Go to 'Entity Editor'
2. Click the **+**
    - This will create an empty entity showing as **\<missing classname!\>** in the list
3. Fill out the new entity using the image below
    - Copy the origin value from any other entity
![CSM Lumper](/images/map_porting/csm_lumper.png)
![Missing CSM Entity](/images/map_porting/csm_broken.png)
![Working CSM](/images/map_porting/csm_working.png)

### Corrupt HDR Cubemaps
Some maps from CS:S have corrupted reflections in Momentum Mod.  
In cases we've seen they've been flat blue/black textures.  
[github info for later](https://github.com/momentum-mod/game/issues/2315#issuecomment-3097105058).  
TODO: ACTUALLY ADD STEPS TO FIX THIS: [Asked about it on discord](https://discord.com/channels/235111289435717633/569734083496902656/1419721854020489407)

![Corrupt Cubemaps](/images/map_porting/corrupt_cubemaps.png)

### Broken / Missing triggers
On very rare occassions maps can have missing or misplaced triggers.  
It's possible to add any rectangular triggers to the map without decompiling by using community made tools and Lumper
- [vmf_to_stripper](https://github.com/benjl/vmf_to_stripper/) - Allows for converting triggers made in hammer to a stripper config
- [zoneToTrigger](https://github.com/Natanxp2/zoneToTrigger) - Allows for converting [Momentum Mod zones](/guide/map_submission/map_zoning/) to a stripper config

{{<hint danger>}}

Be very careful when modifying maps in this way.  
It's best to contact the mapper to make sure they are fine with whatever you are trying to do  
You can always ask for help in **#map-porting** channel on our [Discord](https://discord.gg/momentummod)

{{</hint>}}

### Collectibles
Maps with collectibles can be ported in 2 ways
- Convert the collectible system triggers to [Momentum's collectible system entities](/guide/collectibles/)
- [Zone the map](/guide/map_submission/map_zoning/) using unordered, required checkpoints

{{<hint warning>}}

Converting collectibles to Momentum Mod's system can be complicated.  
There is no one-way-fits-all solution.  
Apply your best judgement when attempting it.

{{</hint>}}

### Moving Brushes

Some maps have moving brushes which have cycles that are too fast to be hit consistently which effectively introduces RNG to competitive runs.    
They should be frozen or deleted **only** if there is **community consensus** around it.  
If you're not sure about this, please ask in **#map-porting** channel on our [Discord](https://discord.gg/momentummod)!
![Moving Brushes](/images/map_porting/moving_brushes.gif)

## Step 5: [Zone the Map](/guide/map_submission/map_zoning/)
## Step 6: [Submit the Map](/guide/map_submission/map_submission/)

# UNFINISHED STUFF ///// NEED REVIEW ------------------------------------------------------------------------
## Textures - CAN'T LUMPER REPLACE THEM DIRECTLY? DO WE WANT PEOPLE REPLACING TEXTURES WITHOUT CONSULTING ANYONE? --- I WOULD NOT INCLUDE THIS AT ALL

The easiest way to replace a texture is to modify any VMT files that refer to the VTF file in question.

- Find any instances of VMTs files that refer to that VTF file in the Pakfile Explorer
  - Currently we don't have automation for this, but it's usually relatively obvious
- Replace the `$basetexture` value (maybe also `$bumpmap` and others) with some other texture on the map that won't look out-of-place
- Remove the original VTF file via Texture Browser / Pakfile Explorer

{{< hint info >}} Source engine textures are stored in **VTF** files, which are the image files, and **VMT** files, which are the material definitions for those textures.

VMTs are text files that define how the texture is used in the game, such as its shader type, properties, and other settings. VMTs are _usually_ stored in the same directory as the VTF files, and have the same name as the VTF file, but with a `.vmt` extension.

Note that multiple VMT files can refer to the same VTF file, so you may need to check multiple VMTs if the texture is used in multiple places. {{< /hint >}}

## Dark Refraction Textures - THIS SHOULD BE FIXED AND NOT COVERED IN THIS GUIDE EVEN NOW --- PEOPLE SHOULDN'T BE RECOMPILING MAPS

Refraction textures in most maps do not render correctly. The cause of this issue is unknown and the only way to fix it currently is to recompile.

![Dark Refraction Textures](/images/map_porting/refraction_dark.png)

![Fixed Refraction Textures](/images/map_porting/refaction_fixed.png)



