---
title: Fixing Entities
categories:
  - guide
tags:
  - mapping
  - guidelines
weight: 4
---

# Introduction
This guide provides a list of fixes to apply to entities when porting maps.  
If the entity you are looking for is not in here please ask for help in **#map-porting** channel on our [Discord](https://discord.gg/momentummod)  
Detailed information on entities can also be found on [Valve Developer Wiki](https://developer.valvesoftware.com/wiki/Main_Page)


{{< hint info >}} 

This guide will teach you how to make changes using 'Entity Tools' and [export them to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)  
When making changes using 'Entity Tools' it's perfectly fine to export them to Lumper only once, when you are done with everything, however there is no downside to gradually saving your progress

{{< /hint >}}

# Boosters
Boosting players can be achieved in various ways and therefore triggers need to be evaluated on a case-by-case basis.  
This guide will teach you how to fix every common scenario

## trigger_push
This trigger applies a constant boost to a player inside it's volume. Consider 2 following scenarios

### Scenario 1: Constant push is required
On some maps it is necessary for the player to be constantly pushed while inside of the trigger.  
In that case no modifications are necessary!

### Scenario 2: Only a single push matters
In this case modifications are required depending on where the trigger is placed.

1. Open entity tools by typing `devui_show entitytools` in console
    - You can bind this to a key for ease of access, `bind <key> "devui_show entitytools"` in console
2. Find the trigger you want to edit (TODO: ELABORATE WHEN ENTITY TOOLS ARE BETTER, RIO IS FIXING THEM)

#### Trigger is in the middle of the map/stage:

3. Check the 'cooldown' box and type **1** in the textbox
4. Click 'Apply Changes`
5. Redo steps 2-4 for all appropriate triggers
6. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)
![Surf Ramp Boost](/images/map_porting/surf_ramp_boost.png)

#### Trigger is at the start of the map/stage:
3. Fail the map/stage and don't move your mouse so you look directly at the trigger
    - You can also set your angle by using `setang X Y Z` command in console
    - If setting the angle manually use multiples of 90 such as `setang 180 0 0` or `setang 0 90 0` to orient yourself properly
4. Walk into the trigger by pressing **W only**
    - The game will automatically get all relevant information after using the trigger in this way
5. If the booster is on completely flat ground ( no slopes next to it ) check 'Standable Surface Collision Filter'
5. Click 'Convert to Set Speed'
6. Redo steps 2-5 for all appropriate triggers
7. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)
![Surf Ramp Boost](/images/map_porting/convert_to_setspeed.png)

## trigger_multiple
This trigger can be used for many various effects, the only ones that are important here are those that act as boosters.  
'Entity Tools' make it very easy to identify them.  

1. Open entity tools by typing `devui_show entitytools` in console
    - You can bind this to a key for ease of access, `bind <key> "devui_show entitytools"` in console
2. Find the trigger you want to edit (TODO: ELABORATE WHEN ENTITY TOOLS ARE BETTER, RIO IS FIXING THEM)

#### If you activate the trigger by jumping on a platform
3. Click 'Convert to OnJump'
4. Redo steps 2-3 for all appropriate triggers
5. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)

#### If you activate the trigger by entering it in the air
3. [Add a cooldown](/guide/map_submission/fixing_entities/#trigger-is-in-the-middle-of-the-mapstage)
4. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)

## trigger_catapult
All catapults that boost the player directly up will be broken in Momentum Mod.  
Fix them by multiplying the value of the **playerSpeed** key by 1.5
![Surf Ramp Boost](/images/map_porting/fix_vertical_catapults.png)

# Logic

# Other entities
This section lists various entities that have changed behavior in Momentum Mod and require modifications
## trigger_teleport
Teleports in Momentum Mod retain player speed by default. This needs to be changed depending on gamemode.

### Surf
In surf it's important that teleports completely disable player movement until they hit the ground.
1. Open entity tools by typing `devui_show entitytools` in console
    - You can bind this to a key for ease of access, `bind <key> "devui_show entitytools"` in console
2. Select a teleport destination that bring you to the start of a map/stage/bonus
    - Make sure you don't select mid-stage teleports. You don't need to modify them in any way
    - You can check where the entity is by clicking 'Teleport to Destination'
3. Select 'Keep Negative Z'
4. Redo steps 2-3 for all appropriate destinations
![Surf Ramp Boost](/images/map_porting/keep_negative_z.png)
### Rocket Jump / Sticky Jump
Sometimes it's possible to hit a teleport while going upwards. This can lead to the player being launched off the platform right after failing  
You should not blindly edit these using 'Entity Tools' as that may lead to breaking teleport jumps

1. Identify the teleport in Lumper by using **Sync Target** or **Sync Pos** option in 'Entity Editor' tab
    - Sync Target will display all entities you are looking at in the game
    - Sync Pos will display all entities in the specified radius around you
    - Sometimes it can be difficult to find proper triggers because of other entities overlapping them, ask on our [Discord](https://discord.gg/momentummod) in #map-porting channel if you need help
2. Click on **Add KeyValue**
3. In the 'newproperty' field type **velocitymode**
4. In the 'newvalue' field type **1**

## func_button
Shooting a button to activate it in **Rocket Jump** can have a different effect in Momentum Mod compared to TF2
1. Check all func_button in Lumper
2. Identify those that have an 'OnDamaged' output
3. Change 'OnDamaged' to **OnPressed**
3. Add **512** to **spawnflags** if it's not already enabled
    - To determine if this flag is enabled divide the value in **spawnflags** by 512 and round down the result. If it's **odd** the flag is enabled
4. If the button is moving very slowly add **1** to **spawnflags** to disable it's movement
![Surf Ramp Boost](/images/map_porting/fix_buttons.png)
    



# Export to Lumper
The changes made with 'Entity Tools' will be reverted once you exit the map.  
Lumper can be used to apply these changes permanently.
1. While still in 'Entity Tools' click 'Export To Lumper'
    - This will automatically create a config with all changes you made and switch your tab to 'Jobs'
2. Run the Job
![Surf Ramp Boost](/images/map_porting/apply_patches.png)


 
