# NdR-TabletUI

Hello guys, this is a new UI created for Tablet only.
As usual, any suggestion is appreciated.

This UI is based on the following custom components:

Essential:

- [Layout Card](https://github.com/thomasloven/lovelace-layout-card)
- [Card Mod](https://github.com/thomasloven/lovelace-card-mod)
- [Button Card](https://github.com/custom-cards/button-card)

Optional: 

- [Mini Graph Card](https://github.com/kalkih/mini-graph-card)
- [Flexible Horseshoe Card](https://github.com/AmoebeLabs/flex-horseshoe-card)
- [Light Popup Card](https://github.com/DBuit/light-popup-card)
- [Stack In Card](https://github.com/custom-cards/stack-in-card)
- [Atomic Calendar Revive](https://github.com/marksie1988/atomic-calendar-revive)
- [Mini Climate Card](https://github.com/artem-sedykh/mini-climate-card)

## Getting Started

### Themes
First of all, we need to set-up two themes (Light and Dark mode). You can create your own or use both [Clear](https://github.com/naofireblade/clear-theme) and [Slate](https://github.com/seangreen2/slate_theme) you find in this repo.
If you want to create your own theme, be sure to set the following values:

```yaml
background-color:
background-sidebar-sx:
primary-background-color: 'var(--background-color)'
```
Those values are highly used on the UI code.

### Automation

Now, we need to set our two Themes to be used by HomeAssistant as Light and Dark themes.
To do this, we have two options.

The first one, is supported only by Browser with dark mode detection:

- Go to Developer Tools;
- Select Services;
- Find the service called ```frontend.set_theme```;
- Insert the following data:
    ```yaml
    name: clear #or the name of your light theme
      mode: light ```
- Reteat the same with the following data:
    ```yaml
    name: slate #or the name of your dark theme
      mode: dark ``` 
 
The second one uses an automation used to switch the Light and Dark themes:
```yaml
automation:
  - id: set_theme_ndrui
    alias: 'Frontend: Set NdRUI Theme'
    trigger:
    - platform: homeassistant
      event: start
    - platform: state
      entity_id: sun.sun
    action:
    - choose:
      - conditions:
        - condition: state
          entity_id: sun.sun
          state: "above_horizon"
        sequence:
        - service: frontend.set_theme
          data:
            name: clear
      - conditions:
        - condition: state
          entity_id: sun.sun
          state: "below_horizon"
        sequence:
        - service: frontend.set_theme
          data:
            name: slate
```

The way how to use those two themes, is also well explained by @N-l1 on his thread of [SoftUI](https://github.com/N-l1/lovelace-soft-ui).
