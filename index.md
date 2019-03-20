---
layout: default
---

# Module `ScenEdit`

Lua is a case sensitive language.

When accessing object properties directly as in 'unit.name', the property should be in lower which will match the Lua generated code. There may be a few special cases (e.g. mission.SISH=true which is a shortcut for scrub_if_side_human) which will be documented below.

However, when accessing the properties through the module functions below, both the keyword/property and the value are case insensitive; the code will worry about matching them up.

**Selector**  
These define the information required as part of the 'select' process for the functions. In the case of functions that 'add' things, these are also key elements to the adding process. Other properties may be included in the 'selector' such as when updating an existing table.

When selecting units, it is preferable to use the GUID as the identifier for a precise match. If not, then the side and name for a more limited search. And as a last option, just the name which search all units in the scenario. When using just the name, usually the first matching name is returned. This is okay if the names are unique. Thus including the side, it will only check the units on that side for a match.

**Wrapper**  
These define the information that is returned from some functions. This information can be usually modified either directly (object.field) or by a wrapper Set(..) function. The particular wrapper Set(..) function is preferred as some validation is performed on the input to ensure that it is within the bounds of the field being updated.

**Data type**  
These cover any special considerations for the data, such as longitude/latitude; degrees, DMS, N/S, E/W, etc.

**Error handling**  
Usually when a Lua script fails, an error is thrown that ends the script at that point. While this may be okay in most cases, it is not often desired. 
Whenever a Command Lua function gets an error, it will normally return a 'nil', and the error message will be available as a Lua global variables; '_errmsg_' will have the last error message, '_errfnc_' the Command Lua function that gave the error, and '_errnum_' the error code (0 is no error, and any value >0 will be an error). Thus if you get back a 'nil', you can check to see if that is due to an error or no data.

Example:  
without the new error handling, the script below would probably terminate after the SE_AddMission() and the rest of script would not run.
```
local mission = ScenEdit_AddMission('USA','Marker strike','strike',{type='land'})
if mission == nil then
 if _errnum_ ~= 0 then print('Failed to add:' .. _errmsg_) else print('Something else') end
else
 print(mission)
 do some more command lua stuff...
end
```

Note: Due to how errors are now handle from SR7, if a command fails in the console window, it will show an error. If the command runs outside the console (as in an event script), it will not fail with a visible error but return a nil or or some other value. One issue with commands running in an event is that sometimes they fail with an in-game message that actually stops the rest of the script from running. Now, these event scripts will run without any in-game message showing, and the designer should check the result of the command and handle any error condition, and let the remaining script run as needed.

My intent is have all command errors behave in the same fashion in the console window; and the command errors outside a console behave without stopping the script. Which requires the designer to cater for the specific error conditions.

To emulate the expected outcome from an event, put `Tool_EmulateNoConsole(true)` at the start of the script to be tested; it is not required in the event code as the script is already not running in a console.

Note also, that the Lua history log should also record the event script errors.

## Selector

<table class="function_list">
	<tbody><tr>
	<td class="name"><a href="#ActionUpdate">ActionUpdate</a></td>
	<td class="summary">Action update.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ConditionUpdate">ConditionUpdate</a></td>
	<td class="summary">Condition update.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ContactSelector">ContactSelector</a></td>
	<td class="summary">Contact selector.</td>
	</tr>
	<tr>
	<td class="name"><a href="#DamageOptions">DamageOptions</a></td>
	<td class="summary">Unit damage.</td>
	</tr>
	<tr>
	<td class="name"><a href="#DoctrineSelector">DoctrineSelector</a></td>
	<td class="summary">Selects a doctrine.</td>
	</tr>
	<tr>
	<td class="name"><a href="#DoctrineWRASelector">DoctrineWRASelector</a></td>
	<td class="summary">Selects a WRA doctrine.</td>
	</tr>
	<tr>
	<td class="name"><a href="#EventTCAUpdate">EventTCAUpdate</a></td>
	<td class="summary">Event Trigger/Condition/Action update.</td>
	</tr>
	<tr>
	<td class="name"><a href="#EventUpdate">EventUpdate</a></td>
	<td class="summary">Event update.</td>
	</tr>
	<tr>
	<td class="name"><a href="#NewMission">NewMission</a></td>
	<td class="summary">New mission options.</td>
	</tr>
	<tr>
	<td class="name"><a href="#NewUnit">NewUnit</a></td>
	<td class="summary">New unit selector.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ReferencePointSelector">ReferencePointSelector</a></td>
	<td class="summary">Reference point selector.</td>
	</tr>
	<tr>
	<td class="name"><a href="#SideOption">SideOption</a></td>
	<td class="summary">Side options.</td>
	</tr>
	<tr>
	<td class="name"><a href="#SpecialAction">SpecialAction</a></td>
	<td class="summary">Special action selector.</td>
	</tr>
	<tr>
	<td class="name"><a href="#TriggerUpdate">TriggerUpdate</a></td>
	<td class="summary">Trigger update.</td>
	</tr>
	<tr>
	<td class="name"><a href="#UnitSelector">UnitSelector</a></td>
	<td class="summary">Select a unit.</td>
	</tr>
	<tr>
	<td class="name"><a href="#UpdateUnit">UpdateUnit</a></td>
	<td class="summary">Update unit sensor/mount/weapon selector.</td>
	</tr>
	<tr>
	<td class="name"><a href="#UpdateUnitCargo">UpdateUnitCargo</a></td>
	<td class="summary">Update unit cargo selector.</td>
	</tr>
	<tr>
	<td class="name"><a href="#VPContactSelector">VPContactSelector</a></td>
	<td class="summary">Contact selector.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Weapon2Magazine">Weapon2Magazine</a></td>
	<td class="summary">Select magazine and weapon.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Weapon2Mount">Weapon2Mount</a></td>
	<td class="summary">Select mount and weapon.</td>
	</tr>
</tbody></table>

## Wrapper/Descriptor

<table class="function_list">
	<tbody><tr>
	<td class="name"><a href="#AttackOptions">AttackOptions</a></td>
	<td class="summary">Attack options.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Contact">Contact</a></td>
	<td class="summary">Contact details.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Doctrine">Doctrine</a></td>
	<td class="summary">Doctrine options.</td>
	</tr>
	<tr>
	<td class="name"><a href="#DoctrineWRA">DoctrineWRA</a></td>
	<td class="summary">WRA Doctrine options.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Event">Event</a></td>
	<td class="summary">Event details.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Group">Group</a></td>
	<td class="summary">Group details.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Loadout">Loadout</a></td>
	<td class="summary">Loadout.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Mission">Mission</a></td>
	<td class="summary">Mission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ReferencePoint">ReferencePoint</a></td>
	<td class="summary">Reference point information.</td>
	</tr>
	<tr>
	<td class="name"><a href="#RefuelOptions">RefuelOptions</a></td>
	<td class="summary">Unit refueling options.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Side">Side</a></td>
	<td class="summary">Side perspective.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Unit">Unit</a></td>
	<td class="summary">Represents a active unit.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Zone">Zone</a></td>
	<td class="summary">Exclusion/No navigation zone.</td>
	</tr>
</tbody></table>

## Data types

<table class="function_list">
	<tbody><tr>
	<td class="name"><a href="#Altitude">Altitude</a></td>
	<td class="summary">Altitude.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Arc">Arc</a></td>
	<td class="summary">Arcs.</td>
	</tr>
	<tr>
	<td class="name"><a href="#DateTime">DateTime</a></td>
	<td class="summary">DateTime.</td>
	</tr>
	<tr>
	<td class="name"><a href="#KeyStore">KeyStore</a></td>
	<td class="summary">KeyStore.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Latitude">Latitude</a></td>
	<td class="summary">Latitude.</td>
	</tr>
	<tr>
	<td class="name"><a href="#LoadoutRole">LoadoutRole</a></td>
	<td class="summary"></td>
	</tr>
	<tr>
	<td class="name"><a href="#LoadoutTimeOfDay">LoadoutTimeOfDay</a></td>
	<td class="summary"></td>
	</tr>
	<tr>
	<td class="name"><a href="#LoadoutWeather">LoadoutWeather</a></td>
	<td class="summary"></td>
	</tr>
	<tr>
	<td class="name"><a href="#Longitude">Longitude</a></td>
	<td class="summary">Longitude.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Size">Size</a></td>
	<td class="summary">Size.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Stance">Stance</a></td>
	<td class="summary">Stance/Posture.</td>
	</tr>
	<tr>
	<td class="name"><a href="#TargetTypeWRA">TargetTypeWRA</a></td>
	<td class="summary">Target type.</td>
	</tr>
	<tr>
	<td class="name"><a href="#TimeStamp">TimeStamp</a></td>
	<td class="summary">TimeStamp.</td>
	</tr>
	<tr>
	<td class="name"><a href="#WeaponDoctrine">WeaponDoctrine</a></td>
	<td class="summary"></td>
	</tr>
	<tr>
	<td class="name"><a href="#WeaponType">WeaponType</a></td>
	<td class="summary"></td>
	</tr>
	<tr>
	<td class="name"><a href="#Weather">Weather</a></td>
	<td class="summary">weather conditions.</td>
	</tr>
</tbody></table>

## Notes

<table class="function_list">
	<tbody><tr>
	<td class="name"><a href="#WrapperFields">WrapperFields</a></td>
	<td class="summary">List fields in a wrapper.</td>
	</tr>
</tbody></table>

## Functions

<table class="function_list">
	<tbody><tr>
	<td class="name"><a href="#GetBuildNumber">GetBuildNumber ()</a></td>
	<td class="summary">Gets the exe build number.</td>
	</tr>
	<tr>
	<td class="name"><a href="#GetScenarioTitle">GetScenarioTitle ()</a></td>
	<td class="summary">Name of the scenario.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_AddExplosion">ScenEdit_AddExplosion (explosion)</a></td>
	<td class="summary">Detonates a warhead at a specified location.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_AddMission">ScenEdit_AddMission (SideNameOrId, MissionNameOrId, MissionType, MissionOptions)</a></td>
	<td class="summary">Add new mission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_AddReferencePoint">ScenEdit_AddReferencePoint (descriptor)</a></td>
	<td class="summary">Add new reference point(s).</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_AddReloadsToUnit">ScenEdit_AddReloadsToUnit (descriptor)</a></td>
	<td class="summary">Adds weapons into a mount.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_AddSide">ScenEdit_AddSide (table)</a></td>
	<td class="summary">Add side.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_AddUnit">ScenEdit_AddUnit (unit)</a></td>
	<td class="summary">Adds a unit based on a descriptor.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_AddWeaponToUnitMagazine">ScenEdit_AddWeaponToUnitMagazine (descriptor)</a></td>
	<td class="summary">Adds weapons into a magazine.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_AddZone">ScenEdit_AddZone (sideName, zoneType, table)</a></td>
	<td class="summary">Add no-nav or exclusion zone.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_AssignUnitAsTarget">ScenEdit_AssignUnitAsTarget (AUNameOrIDOrTable, MissionNameOrID)</a></td>
	<td class="summary">Assigns targets to a Strike mission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_AssignUnitToMission">ScenEdit_AssignUnitToMission (unitname, mission, escort)</a></td>
	<td class="summary">Assign a unit to a mission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_AttackContact">ScenEdit_AttackContact (attackerID, contactId, options)</a></td>
	<td class="summary">Attack a contact</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_ClearKeyValue">ScenEdit_ClearKeyValue (key, forCampaign)</a></td>
	<td class="summary">Clears a key<p></p>

<p> ..</p></td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_CurrentTime">ScenEdit_CurrentTime ()</a></td>
	<td class="summary">Get the current scenario time.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_DeleteMission">ScenEdit_DeleteMission (SideNameOrId, MissionNameOrId)</a></td>
	<td class="summary">Delete mission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_DeleteReferencePoint">ScenEdit_DeleteReferencePoint (selector)</a></td>
	<td class="summary">Delete a reference point.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_DeleteUnit">ScenEdit_DeleteUnit (unit)</a></td>
	<td class="summary">Deletes unit.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_EndScenario">ScenEdit_EndScenario ()</a></td>
	<td class="summary">Ends the current scenario.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_EventX">ScenEdit_EventX ()</a></td>
	<td class="summary">Active Event</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_ExecuteEventAction">ScenEdit_ExecuteEventAction (EventDescriptionOrID)</a></td>
	<td class="summary">Execute a Lua Event action script.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_ExecuteSpecialAction">ScenEdit_ExecuteSpecialAction (eventNameOrId)</a></td>
	<td class="summary">Execute a Lua Special action script</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_ExportInst">ScenEdit_ExportInst (side, unitList, fileData)</a></td>
	<td class="summary">Export unit(s) to an inst file.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_ExportMission">ScenEdit_ExportMission (SideNameOrId, MissionNameOrId)</a></td>
	<td class="summary">Export mission parameters.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_FillMagsForLoadout">ScenEdit_FillMagsForLoadout (unit, loadoutid, quantity)</a></td>
	<td class="summary">Fill a unit's magazine(s) with aircraft stores</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetContact">ScenEdit_GetContact (contact)</a></td>
	<td class="summary">Fetches a contact based on a selector.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetContacts">ScenEdit_GetContacts (side)</a></td>
	<td class="summary">Contacts from a side.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetDoctrine">ScenEdit_GetDoctrine (selector)</a></td>
	<td class="summary">Gets the doctrine of the designated object.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetDoctrineWRA">ScenEdit_GetDoctrineWRA (selector)</a></td>
	<td class="summary">Gets the WRA doctrine.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetEvent">ScenEdit_GetEvent (EventDescriptionOrID, level)</a></td>
	<td class="summary">Gets the properties of an event.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetEvents">ScenEdit_GetEvents (level)</a></td>
	<td class="summary">Gets all events.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetKeyValue">ScenEdit_GetKeyValue (key, forCampaign)</a></td>
	<td class="summary">Gets the value for a key from the persistent key store.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetLoadout">ScenEdit_GetLoadout (loadoutinfo)</a></td>
	<td class="summary">Gets the loadout for a unit</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetMission">ScenEdit_GetMission (SideNameOrId, MissionNameOrId)</a></td>
	<td class="summary">Get details of a mission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetReferencePoints">ScenEdit_GetReferencePoints (selector)</a></td>
	<td class="summary">Get a set of reference point(s).</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetScenHasStarted">ScenEdit_GetScenHasStarted ()</a></td>
	<td class="summary">Has the scenario started?</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetScore">ScenEdit_GetScore (side)</a></td>
	<td class="summary">Get a given side's score.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetSideIsHuman">ScenEdit_GetSideIsHuman (sidename)</a></td>
	<td class="summary">Returns true if side is played by a human player</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetSideOptions">ScenEdit_GetSideOptions (options)</a></td>
	<td class="summary">Get side options.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetSidePosture">ScenEdit_GetSidePosture (sideA, sideB)</a></td>
	<td class="summary">Gets the posture of one side towards another.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetSpecialAction">ScenEdit_GetSpecialAction (action_info)</a></td>
	<td class="summary">Gets the properties of an existing special action.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetUnit">ScenEdit_GetUnit (unit)</a></td>
	<td class="summary">Fetches a unit based on a selector.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_GetWeather">ScenEdit_GetWeather ()</a></td>
	<td class="summary">Get the current weather conditions.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_HostUnitToParent">ScenEdit_HostUnitToParent (host_info)</a></td>
	<td class="summary">Host/base</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_ImportInst">ScenEdit_ImportInst (side, filename)</a></td>
	<td class="summary">Imports an inst file.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_ImportMission">ScenEdit_ImportMission (SideNameOrId, MissionNameOrId)</a></td>
	<td class="summary">Import mission parameters.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_InputBox">ScenEdit_InputBox (string)</a></td>
	<td class="summary">Open an input box with the passed prompt.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_KillUnit">ScenEdit_KillUnit (unit)</a></td>
	<td class="summary">Kill unit.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_MsgBox">ScenEdit_MsgBox (string, style)</a></td>
	<td class="summary">Show a message box with a passed string.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_PlayerSide">ScenEdit_PlayerSide ()</a></td>
	<td class="summary">The player's current side.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_RefuelUnit">ScenEdit_RefuelUnit (unitOptions)</a></td>
	<td class="summary">Cause unit to refuel.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_RemoveSide">ScenEdit_RemoveSide (table)</a></td>
	<td class="summary">Remove side from play.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_RemoveUnitAsTarget">ScenEdit_RemoveUnitAsTarget (AUNameOrIDOrTable, MissionNameOrID)</a></td>
	<td class="summary">Removes targets from a Strike mission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_RunScript">ScenEdit_RunScript (script)</a></td>
	<td class="summary">Runs a script from a file.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetAction">ScenEdit_SetAction (options)</a></td>
	<td class="summary">Sets the attributes of an action.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetCondition">ScenEdit_SetCondition (options)</a></td>
	<td class="summary">Sets the attributes of an condition.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetDoctrine">ScenEdit_SetDoctrine (selector, doctrine)</a></td>
	<td class="summary">Set the doctrine of the designated object.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetDoctrineWRA">ScenEdit_SetDoctrineWRA (selector, options)</a></td>
	<td class="summary">
<blockquote>
    <p>Sets the WRA doctrine.</p>
</blockquote>
</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetEMCON">ScenEdit_SetEMCON (type, name, emcon)</a></td>
	<td class="summary">Sets the EMCON of the selected object.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetEvent">ScenEdit_SetEvent (EventDescriptionOrID, options)</a></td>
	<td class="summary">Sets the attributes of an event.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetEventAction">ScenEdit_SetEventAction (EventDescriptionOrID, options)</a></td>
	<td class="summary">Sets the attributes of a T/C/A.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetEventCondition">ScenEdit_SetEventCondition (EventDescriptionOrID, options)</a></td>
	<td class="summary">Sets the attributes of a T/C/A.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetEventTrigger">ScenEdit_SetEventTrigger (EventDescriptionOrID, options)</a></td>
	<td class="summary">Sets the attributes of a T/C/A.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetKeyValue">ScenEdit_SetKeyValue (key, value, forCampaign)</a></td>
	<td class="summary">Sets the value for a key in the persistent key store.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetLoadout">ScenEdit_SetLoadout (loadoutinfo)</a></td>
	<td class="summary">Sets the loadout for a unit</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetMission">ScenEdit_SetMission (SideName, MissionNameOrId, MissionOptions)</a></td>
	<td class="summary">Set details for a mission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetReferencePoint">ScenEdit_SetReferencePoint (descriptor)</a></td>
	<td class="summary">Update a reference point(s) with new values.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetScore">ScenEdit_SetScore (side, score, reason)</a></td>
	<td class="summary">Sets a given side's score.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetSideOptions">ScenEdit_SetSideOptions (options)</a></td>
	<td class="summary">Set side options.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetSidePosture">ScenEdit_SetSidePosture (sideAName, sideBName, posture)</a></td>
	<td class="summary">Set the posture of a side towards another.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetSpecialAction">ScenEdit_SetSpecialAction (action_info)</a></td>
	<td class="summary">Sets the properties of an existing special action.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetTime">ScenEdit_SetTime (new_time)</a></td>
	<td class="summary">Sets the scenario time (UTC).</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetTrigger">ScenEdit_SetTrigger (options)</a></td>
	<td class="summary">Sets the attributes of a trigger.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetUnit">ScenEdit_SetUnit (unit)</a></td>
	<td class="summary">Sets the properties of a unit that already exists.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetUnitDamage">ScenEdit_SetUnitDamage (options)</a></td>
	<td class="summary">Set unit damage.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetUnitSide">ScenEdit_SetUnitSide (sidedesc)</a></td>
	<td class="summary">Changes the side of a unit</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SetWeather">ScenEdit_SetWeather (temperature, rainfall, undercloud, seastate)</a></td>
	<td class="summary">Set the current weather conditions.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_SpecialMessage">ScenEdit_SpecialMessage (side, message, location)</a></td>
	<td class="summary">Displays a special message consisting of the HTML text <code>message</code> to side `side.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_TransferCargo">ScenEdit_TransferCargo (fromUnit, toUnit, cargoList)</a></td>
	<td class="summary">Transfer cargo</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_UnitC">ScenEdit_UnitC ()</a></td>
	<td class="summary">Detected Contact</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_UnitX">ScenEdit_UnitX ()</a></td>
	<td class="summary">Activating Unit</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_UnitY">ScenEdit_UnitY ()</a></td>
	<td class="summary">Detecting Unit</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_UnloadCargo">ScenEdit_UnloadCargo (fromUnit, cargoList)</a></td>
	<td class="summary">Unload cargo</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_UpdateUnit">ScenEdit_UpdateUnit (options)</a></td>
	<td class="summary">Update items on a unit.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_UpdateUnitCargo">ScenEdit_UpdateUnitCargo (options)</a></td>
	<td class="summary">Update cargo on a unit.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_UseAttachment">ScenEdit_UseAttachment (attachment)</a></td>
	<td class="summary">Import an attachment into the scene.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ScenEdit_UseAttachmentOnSide">ScenEdit_UseAttachmentOnSide (attachment, sidename)</a></td>
	<td class="summary">Use an attachment on a side (used for .inst files as attachments).</td>
	</tr>
	<tr>
	<td class="name"><a href="#Tool_Bearing">Tool_Bearing ()</a></td>
	<td class="summary">Get bearing between points.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Tool_DumpEvents">Tool_DumpEvents ()</a></td>
	<td class="summary">Dump scenario events.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Tool_EmulateNoConsole">Tool_EmulateNoConsole ()</a></td>
	<td class="summary">Emulates no console.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Tool_Range">Tool_Range ()</a></td>
	<td class="summary">Get range between points.</td>
	</tr>
	<tr>
	<td class="name"><a href="#VP_ExportUnits">VP_ExportUnits (table, filename)</a></td>
	<td class="summary">Export unit details ></td>
	</tr>
	<tr>
	<td class="name"><a href="#VP_GetContact">VP_GetContact (ContactGUID)</a></td>
	<td class="summary">Get contact details</td>
	</tr>
	<tr>
	<td class="name"><a href="#VP_GetSide">VP_GetSide (side)</a></td>
	<td class="summary">Side information from player's perspective.</td>
	</tr>
	<tr>
	<td class="name"><a href="#VP_GetUnit">VP_GetUnit (ActiveOrContact)</a></td>
	<td class="summary">Get unit details</td>
	</tr>
	<tr>
	<td class="name"><a href="#World_GetCircleFromPoint">World_GetCircleFromPoint (table)</a></td>
	<td class="summary">Returns a circle around point.</td>
	</tr>
	<tr>
	<td class="name"><a href="#World_GetElevation">World_GetElevation (location)</a></td>
	<td class="summary">Returns the elevation in meters of a given position</td>
	</tr>
	<tr>
	<td class="name"><a href="#World_GetPointFromBearing">World_GetPointFromBearing (table)</a></td>
	<td class="summary">Returns a location based on bearing.</td>
	</tr>
</tbody></table>

## Tables

<table class="function_list">
	<tbody><tr>
	<td class="name"><a href="#AAR">AAR</a></td>
	<td class="summary">AAR.</td>
	</tr>
	<tr>
	<td class="name"><a href="#CargoMission">CargoMission</a></td>
	<td class="summary">CargoMission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Component">Component</a></td>
	<td class="summary">Component.</td>
	</tr>
	<tr>
	<td class="name"><a href="#EMmatch">EMmatch</a></td>
	<td class="summary">EM matches</td>
	</tr>
	<tr>
	<td class="name"><a href="#Emissions">Emissions</a></td>
	<td class="summary">Contact emission</td>
	</tr>
	<tr>
	<td class="name"><a href="#Explosion">Explosion</a></td>
	<td class="summary">Defines the warhead to detonate.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Facility">Facility</a></td>
	<td class="summary">Facility.</td>
	</tr>
	<tr>
	<td class="name"><a href="#FerryMission">FerryMission</a></td>
	<td class="summary">FerryMission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Fuel">Fuel</a></td>
	<td class="summary">Fuel.</td>
	</tr>
	<tr>
	<td class="name"><a href="#FuelState">FuelState</a></td>
	<td class="summary">Status of a fuel <code>type</code>.</td>
	</tr>
	<tr>
	<td class="name"><a href="#HostUnit">HostUnit</a></td>
	<td class="summary">Requests that a unit be hosted/based to another</td>
	</tr>
	<tr>
	<td class="name"><a href="#LatLon">LatLon</a></td>
	<td class="summary">A Position on the map.</td>
	</tr>
	<tr>
	<td class="name"><a href="#LoadoutInfo">LoadoutInfo</a></td>
	<td class="summary">Information on a loadout to add/alter</td>
	</tr>
	<tr>
	<td class="name"><a href="#Magazine">Magazine</a></td>
	<td class="summary">Magazine.</td>
	</tr>
	<tr>
	<td class="name"><a href="#MineClearMission">MineClearMission</a></td>
	<td class="summary">MineClearMission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#MineMission">MineMission</a></td>
	<td class="summary">MineMission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Mount">Mount</a></td>
	<td class="summary">Mount.</td>
	</tr>
	<tr>
	<td class="name"><a href="#PatrolMission">PatrolMission</a></td>
	<td class="summary">PatrolMission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#SideContact">SideContact</a></td>
	<td class="summary">Side's contact.</td>
	</tr>
	<tr>
	<td class="name"><a href="#SideDescription">SideDescription</a></td>
	<td class="summary">Information on how to change a unit's side.</td>
	</tr>
	<tr>
	<td class="name"><a href="#SideSelector">SideSelector</a></td>
	<td class="summary">Information to select a particular side.</td>
	</tr>
	<tr>
	<td class="name"><a href="#SideUnit">SideUnit</a></td>
	<td class="summary">Side's unit.</td>
	</tr>
	<tr>
	<td class="name"><a href="#StrikeMission">StrikeMission</a></td>
	<td class="summary">StrikeMission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#SupportMission">SupportMission</a></td>
	<td class="summary">SupportMission.</td>
	</tr>
	<tr>
	<td class="name"><a href="#WRA">WRA</a></td>
	<td class="summary">WRA settings.</td>
	</tr>
	<tr>
	<td class="name"><a href="#Waypoint">Waypoint</a></td>
	<td class="summary">Waypoint</td>
	</tr>
	<tr>
	<td class="name"><a href="#WeaponLoaded">WeaponLoaded</a></td>
	<td class="summary">Weapon loads on mount or in magazine.</td>
	</tr>
	<tr>
	<td class="name"><a href="#ZoneMarker">ZoneMarker</a></td>
	<td class="summary">Zone marker.</td>
	</tr>
</tbody></table>


## Selectors <a name="Selector"></a>

----

**ActionUpdate**<a name="ActionUpdate"></a>  

- `ID:` **string** The GUID of the action [READONLY]
- `Description:` **string** Description or GUID of action
- `NewName:` **string** If specified, the new name of the action
- `Mode:` **string** Operation to do - 'list', 'add', 'remove', 'update' (default) SE_Set...()
- `Type:` **string** Type of action [required only for 'add' option]
- `Other:` **various** values apply to Type of action.

---

**ConditionUpdate** <a name="ConditionUpdate"></a> 

- `ID` **string** The GUID of the condition [READONLY]
- `Description` **string** Description or GUID of condition
- `NewName` **string** If specified, the new name of the condition
- `Mode` **string** Operation to do - 'list', 'add', 'remove', 'update' (default) SE_Set...()
- `Type` **string** Type of condition [required only for 'add' option]
- `Other` **various** values apply to Type of condition.