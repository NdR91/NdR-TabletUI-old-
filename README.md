# NdR-TabletUI

Hello guys, this is a new UI created for Tablet only.
As usual, any suggestion is appreciated.

This UI is based on the following custom components:

Essential:

- [x] [Layout Card](https://github.com/thomasloven/lovelace-layout-card)
- [x] [Card Mod](https://github.com/thomasloven/lovelace-card-mod)
- [x] [Button Card](https://github.com/custom-cards/button-card)
- [x] [Browser Mod](https://github.com/thomasloven/hass-browser_mod)

Optional: 

- [ ] [Mini Graph Card](https://github.com/kalkih/mini-graph-card)
- [ ] [Flexible Horseshoe Card](https://github.com/AmoebeLabs/flex-horseshoe-card)
- [ ] [Light Popup Card](https://github.com/DBuit/light-popup-card)
- [ ] [Stack In Card](https://github.com/custom-cards/stack-in-card)
- [ ] [Atomic Calendar Revive](https://github.com/marksie1988/atomic-calendar-revive)
- [ ] [Mini Climate Card](https://github.com/artem-sedykh/mini-climate-card)

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
 
The second one uses an automation to switch the Light and Dark themes:
<details><summary><b>Show automation code</b></summary>
    
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
</details>

### Card Mod and Button Cards

To apply our new style, we need to use the following code for each card:

```yaml
style: |
  ha-card {
      background-color: var(--primary-background-color);
      border-radius: 15px;
      margin: 10px;
      box-shadow:
        {% if is_state('sun.sun', 'above_horizon') %}
          -4px -4px 8px rgba(255, 255, 255, .5), 5px 5px 8px rgba(0, 0, 0, .03);
        {% elif is_state('sun.sun', 'below_horizon') %}
          -5px -5px 8px rgba(50, 50, 50, .2), 5px 5px 8px rgba(0, 0, 0, .08);
        {% endif %}
   }
```
Notice that this is the minimum code you need to apply the style, but of course you can customize it with more variables.
The way how to use those themes and button cards, is also well explained by @N-l1 on his thread of [SoftUI](https://github.com/N-l1/lovelace-soft-ui).

### Layout Card

Another important thing is the structure of the UI, because every view has 3 main zones:
- The Left sidebar;
- The Main area;
- The Right sidebar.

To achieve this result, you have to start every view with the following code:

```yaml
  - title: Home #Choose the name you want
    path: home #Choose the path you want
    icon: 'mdi:home-roof' #Choose the icon you want
    panel: false
    type: custom:grid-layout
    layout:
      grid-template-columns: 7% auto 23% #You can customize the size of both sidebars
      grid-template-rows: 100%
      grid-template-areas: |
        "sidebarleft main sidebarright"
    badges: []
    cards:
```
You also will see a grid template inside the 3 zones. This is used on the Main area in order to set the order of the cars, or on the Right sidebar to leave some empty space. See the examples below.

<details><summary><b>Main area config:</b></summary>

```yaml
- type: vertical-stack
  layout:
      grid-area: main #The position of this card
  cards:
      - type: vertical-stack
      cards:
        - type: 'custom:layout-card'
          layout_type: grid
          layout_options:
              grid-template-columns: 40% 60%
              grid-template-rows: 
              grid-template-areas: |
                "wheater calendar"
                "climate camera"
                "power power"
          cards:
```
</details>

<details><summary><b>Right Sidebar config:</b></summary>

```yaml
- type: custom:stack-in-card #We need the Stack-in-Card only to apply the background color
  layout:
      grid-area: sidebarright #The position of this card
  card_mod:
      style: |
      ha-card {
          height: 100%;
          background-color: var(--background-sidebar-sx);
          } 
  cards: 
    - type: 'custom:layout-card'
      layout_type: grid
      layout_options:
          grid-template-columns: 5% 95%
          grid-template-rows: 65% 35%
          grid-template-areas: |
          "free1 sidebar"
          "graph graph"
      cards:
```
</details>

You can go deeper on how the Grid Layout works on the [Layout Card](https://github.com/thomasloven/lovelace-layout-card) page.

### Popups

Currently, popups are a work in progress, due to the fact that recently the custom component was updated with some new features wich will be added to this UI.
Further update will become available soon, but meanwhile you an use this setup to get it working.

The first few lines of the UI, must contain the path where you will store your popup cards:

```yaml
title: Home 
popup_cards: !include NdRUI/ndrui_tablet_popup.yaml # <--- Use your path
views: 
```

In order to use a popup, you need to add the following code to the card:

```yaml
tap_action:
  action: call-service
  service: browser_mod.popup
  service_data: !include NdRUI_Tablet_Popup/clima.yaml # <--- Use your path
```

The popup itself is another view, with the usual configuration:

```yaml
title: Clima
style:  # This is the style applied for this UI
  $: |
    .mdc-dialog {
      backdrop-filter: blur(17px);
      -webkit-backdrop-filter: blur(17px);
      background: rgba(0,0,0,0.25);
    }
    .mdc-dialog .mdc-dialog__container .mdc-dialog__surface {
      background: none !important;
      box-shadow: none;
      border-radius: 0px;
    }
hide_header: true
auto_close: false
large: true
card:
  type: vertical-stack
  cards: 
    < Card...
  ```
  More info on Popups are available on [Browser Mod](https://github.com/thomasloven/hass-browser_mod) page.

### Floorplan

Creating a floorplan is quite long to explain. I suggest to see the [lukevink](https://github.com/lukevink/hass-config-lajv) page to have a full explanation of the whole procedure.

Here I will give a quick cue:

1- Create your floorplan with SweetHome3d - I suggest this [Guide](https://aarongodfrey.dev/home%20automation/tips_for_creating_a_3d_floorplan_using_sweethome3d/) to figure out how to do it;
2- Using SweetHome3D, choose a good positioning to save some pictures of your floorplan - Use the previous guide, section [Rendering](https://aarongodfrey.dev/home%20automation/tips_for_creating_a_3d_floorplan_using_sweethome3d/#rendering). You will need the following pictures:

- One pic with your floorplan during daytime (Note: this is the only one Pic which is optional)