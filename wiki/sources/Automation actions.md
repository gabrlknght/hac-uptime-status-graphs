---
title: "Automation actions"
source: "https://www.home-assistant.io/docs/automation/services/"
author:
  - "[[Home Assistant]]"
published:
created: 2026-06-17
description: "Reference for the actions you can call from an automation, including how to pass data, target a specific entity, and chain multiple actions together."
tags:
  - "clippings"
---
The automation integration has actions to control automations, like turning automations on and off. This can be useful if you want to disable an automation from another automation.

## Action automation.turn\_on

This action enables the automation’s triggersA trigger is a set of values or conditions of a platform that are defined to cause an automation to run. [\[Learn more\]](https://www.home-assistant.io/docs/automation/trigger/).

| Data attribute | Optional | Description |
| --- | --- | --- |
| `entity_id` | no | Entity ID of automation to turn on. Can be a list. `none` or `all` are also accepted. |

## Action automation.turn\_off

This action disables the automation’s triggersA trigger is a set of values or conditions of a platform that are defined to cause an automation to run. [\[Learn more\]](https://www.home-assistant.io/docs/automation/trigger/), and optionally stops any currently active actionsActions are used in several places in Home Assistant. As part of a script or automation, actions define what is going to happen once a trigger is activated. In scripts, an action is called *sequence*. [\[Learn more\]](https://www.home-assistant.io/docs/automation/action/).

| Data attribute | Optional | Description |
| --- | --- | --- |
| `entity_id` | no | Entity ID of automation to turn off. Can be a list. `none` or `all` are also accepted. |
| `stop_actions` | yes | Stop any currently active actions (defaults to true). |

## Action automation.toggle

This action enables the automation’s triggers if they were disabled, or disables the automation’s triggers, and stops any currently active actions, if the triggers were enabled.

| Data attribute | Optional | Description |
| --- | --- | --- |
| `entity_id` | no | Entity ID of automation to turn on. Can be a list. `none` or `all` are also accepted. |

## Action automation.trigger

This action will trigger the actionActions are used in several places in Home Assistant. As part of a script or automation, actions define what is going to happen once a trigger is activated. In scripts, an action is called *sequence*. [\[Learn more\]](https://www.home-assistant.io/docs/automation/action/) of an automationAutomations in Home Assistant allow you to automatically respond to things that happen in and around your home. [\[Learn more\]](https://www.home-assistant.io/docs/automation/). By default it bypasses any conditions, though that can be changed via the `skip_condition` attribute.

| Data attribute | Optional | Description |
| --- | --- | --- |
| `entity_id` | no | Entity ID of automation to trigger. Can be a list. `none` or `all` are also accepted. |
| `skip_condition` | yes | Whether or not the condition will be skipped (defaults to true). |

## Action automation.reload

*This action is only required if you create/edit automations in YAML. Automations via the UI do this automatically.*

This action reloads all automations, stopping all currently active automation actions.