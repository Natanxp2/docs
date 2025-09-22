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
If the entity you are looking for is not in here please ask for help in **#map-porting** channel on our [Discord](https://discord.gg/momentummod).  
Detailed information on entities can also be found on [Valve Developer Wiki](https://developer.valvesoftware.com/wiki/Main_Page).

# Boosters
Boosting players can be achieved in various ways and therefore triggers need to be evaluated on a case-by-case basis.  
This guide will teach you how to fix every common scenario.

## trigger_push and trigger_multiple
**trigger_push** applies a constant boost to a player inside it's volume.  
**trigger_multiple** can be used for many purposes, however here we only care about it when it gives the player a singular burst of speed.  
Both need to be modified depending on how you activate them.

1. Open entity tools by typing `devui_show entitytools` in console
    - You can bind this to a key for ease of access, `bind <key> "devui_show entitytools"` in console
2. Open 'Boost Triggers' section and find the trigger you want to edit

{{<hint info>}}

**Entity tools** will list all boost triggers on the map. You can teleport to them by clicking 'Teleport to Trigger' and fix each one based on scenarios listed below.  
When making these changes it's perfectly fine to export them only once, when you are done with everything, however there is no downside to gradually saving your progress.

{{</hint>}}
{{<hint info>}}

You can change the font size of **Entity Tools** by using `devui_font_scale` command in console.

{{</hint>}}

### Scenario 1: Constant push is required
On some maps it is necessary for the player to be constantly pushed while inside of the trigger.  

#### If the boost is applied in the air / while surfing
3. Check the 'cooldown' box and type **1** in the textbox
4. Click 'Apply Changes`
5. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)

![Surf Ramp Boost](/images/map_porting/surf_ramp_boost.png)

#### If the boost is applied while walking on the ground
3. If the ground is completely smooth ( no ramps or bumps ), check **Standable Surface Collision Filter**
4. If the ground is not smooth, add a cooldown to it with [the steps above](http://localhost:1313/guide/map_submission/fixing_entities/#if-the-boost-is-applied-in-the-air--while-surfing)
5. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)

![Floor boost](/images/map_porting/floor_boost.png)

### Scenario 2: Only a single push matters
In this case modifications are required depending on how you activate the trigger.

#### If you activate the trigger by jumping on it:
3. Click 'Convert to OnJump'
4. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)

![OnJump Boost](/images/map_porting/onjump_boost.png)

#### If you activate the trigger by flying/surfing into it:

3. Add a cooldown with [the steps above](http://localhost:1313/guide/map_submission/fixing_entities/#if-the-boost-is-applied-in-the-air--while-surfing)
4. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)

#### If you activate the trigger by walking into it:
3. Fail the map/stage and don't move your mouse so you look directly at the trigger
    - You can also set your angle by using `setang X Y Z` command in console
    - If setting the angle manually use multiples of 90 such as `setang 180 0 0` or `setang 0 90 0` to orient yourself properly
4. Walk into the trigger by pressing **W only**
    - The game will automatically get all relevant information after using the trigger in this way
4. Click 'Convert to Set Speed'
5. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)

![Convert To SetSpeed](/images/map_porting/convert_to_setspeed.png)

## trigger_catapult
All catapults that boost the player directly up will be broken in Momentum Mod.  
Fix them by multiplying the value of the **playerSpeed** key by 1.5

![Fix Vertical Catapults](/images/map_porting/fix_vertical_catapults.png)

# Logic
These entites manage logic on the map. They need to be evaluated case-by-case and removed if necessary.

## logic_auto
While logic_auto is generally necessary, some maps use it to set server variables. This is not allowed in Momentum Mod.

1. Look through outputs of every logic_auto
2. Remove only outputs that have parameters starting with **sv_** or **mp_**
    - Take note of **sv_maxvelocity** if present, you will need to set that value when zoning
    - If **sv_allowbunnyhopping 1** is present, you will need to consider globally allowing bhop on the map, this generally only matters for **Surf**
3. If by the end of that process the entity doesn't have any outputs, you can remove it completely

![Delete Server Commands](/images/map_porting/delete_server_commands.png)

## logic_timer
This entity is generally used for displaying time on Rocket Jump / Sticky Jump / KZ maps.  
Old surf maps however, often use it to teleport players to jail after set amount of time.
If used for jail, this and all associated entities need to be removed.

1. Determine if **logic_timer** is used for jail, either by playing the map or reading keyvalues such as **targetname**
2. If it's used for jail, copy the **Target Entity Name** to **Value** field
    - Make sure to clear out all other search fields so that no entities are filtered out
3. Remove **all** entities that come up
4. Remove the original **logic_timer**

![Lumper Timer](/images/map_porting/lumper_timer.png)

![Lumper Teleports](/images/map_porting/lumper_teleports.png)

# Other entities
This section lists various entities that have changed behavior in Momentum Mod and require modifications.

## trigger_teleport
Teleports in Momentum Mod retain player speed by default. This needs to be changed depending on gamemode.

### Surf
In surf it's important that teleports to map/stage/bonus start completely disable player movement until they hit the ground.  
While it may seem that surf maps deal with that using small cages, Momentum Mod requires a different fix.
1. Open entity tools by typing `devui_show entitytools` in console
    - You can bind this to a key for ease of access, `bind <key> "devui_show entitytools"` in console
2. Select a teleport destination. You can check where it's located by clicking 'Teleport to Destination'
    {{<hint warning>}}

    Make sure you only select destinations at the start of a stage/map/bonus. Do not edit other teleports such as those mid-stage

    {{</hint>}}
3. Select 'Keep Negative Z'
4. Redo steps 2-3 for all appropriate destinations
5. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)

![Keep Negative Z](/images/map_porting/keep_negative_z.png)

### Bhop
Some maps force the player to constantly jump to not get teleported.   
This can cause issues when rapidly jumping/sliding up slopes or jumping up a ledge when the triggers are sticking out.
1. Open entity tools by typing `devui_show entitytools` in console
2. Open the 'Bhop trigger fix' section
3. Click 'Fix Bhop Triggers'
4. [Export to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper)

![Fix Bhop Triggers](/images/map_porting/fix_bhop_triggers.png)

### Rocket Jump
Sometimes it's possible to hit a teleport while going upwards. This can lead to the player being launched off the platform right after failing.  
You should not blindly edit these using 'Entity Tools' as that may lead to breaking teleport jumps.

1. Identify the teleport in Lumper by using **Sync Target** or **Sync Pos** option in 'Entity Editor' tab
    - Sync Target will display all entities you are looking at in the game
    - Sync Pos will display all entities in the specified radius around you
    - Sometimes it can be difficult to find proper triggers because of other entities overlapping them, ask on our [Discord](https://discord.gg/momentummod) in **#map-porting** channel if you need help
2. Click on **Add KeyValue**
3. In the 'newproperty' field type **velocitymode**
4. In the 'newvalue' field type **1**  

![Fix Preserving Speed](/images/map_porting/fix_preserving_speed.png)

## func_button
Shooting a button to activate it in **Rocket Jump** can have a different effect in Momentum Mod compared to TF2.
1. Check all func_button in Lumper
2. Identify those that have an 'OnDamaged' output
3. Change 'OnDamaged' to **OnPressed**
3. Add **512** to **spawnflags** if it's not already enabled
    - To determine if this flag is enabled divide the value in **spawnflags** by 512 and round down the result. If it's **odd** the flag is enabled
4. If the button is moving very slowly add **1** to **spawnflags** to disable it's movement  

![Fix Buttons](/images/map_porting/fix_buttons.png)

## trigger_gravity
Sometimes **trigger_gravity** is meant to apply permanent gravity changes to the player.   

{{<hint warning>}}

These changes are usually reverted later in the map. Make sure you modify all permanent gravity changes in this way.  
If the player is meant to have modified gravity **only** when inside of the trigger, you don't need to modify it at all.

{{</hint>}}

1. Identify the trigger in Lumper by using **Sync Target** or **Sync Pos** option in 'Entity Editor' tab
    - Sync Target will display all entities you are looking at in the game
    - Sync Pos will display all entities in the specified radius around you
    - Sometimes it can be difficult to find proper triggers because of other entities overlapping them, ask on our [Discord](https://discord.gg/momentummod) in **#map-porting** channel if you need help
2. Click on **Add KeyValue**
3. In the 'newproperty' field type **persist**
4. In the 'newvalue' field type **1**

{{<hint info>}}

It's also possible to resolve this using issue the 'Gravity Triggers' section in 'Entity Tools' and [exporting to Lumper](/guide/map_submission/fixing_entities/#export-to-lumper).  
That however, only offers an option to modify **all** gravity trigges on the map.  
Please make sure that this fix is applicable to **all of them** before using it.


{{</hint>}}

![Fix Gravity Triggers](/images/map_porting/fix_gravity_triggers.png)

    
# Export to Lumper
The changes made with 'Entity Tools' will be reverted once you exit the map.  
Lumper can be used to apply these changes permanently.
1. While still in 'Entity Tools' click 'Export To Lumper'
    - This will automatically create a config with all changes you made and switch your tab to 'Jobs'
2. Run the Job

{{<hint info>}}

Alternatively you can 'Export To File' and then apply it in Lumper using the 'Stripper(File)' job.  
These files are saved to **/momentum/maps/entitytools_stripper** folder.

{{</hint>}}  

![Apply Patches](/images/map_porting/apply_patches.png)


 
