# NdR TabletUI

<img src="/www/ui_tablet/screen/dashboard-d.png" width="1000" /> 
<img src="/www/ui_tablet/screen/floorplan-l.png" width="1000" /> 

<details><summary><b>More Screenshots</b></summary>
<img src="/www/ui_tablet/screen/system-d.png" width="400" /> <img src="/www/ui_tablet/screen/dashboard-l.png" width="400" />     
<img src="/www/ui_tablet/screen/system-l.png" width="400" /> <img src="/www/ui_tablet/screen/floorplan-d.png" width="400" />     
</details>

## 
If you like my work...

<a href="https://www.buymeacoffee.com/Ndr91" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>

##

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
- [ ] [Gap Card](https://github.com/thomasloven/lovelace-gap-card)
- [ ] [Simple Weather Card](https://github.com/kalkih/simple-weather-card)
- [ ] [Config Template Card](https://github.com/iantrich/config-template-card)

## Getting Started

### Themes
First of all, we need to set-up two themes (Light and Dark mode). 
If you want to use the same stuff shown on this repo, download the whole [Theme](https://github.com/NdR91/NdR-TabletUI/tree/master/themes) folder and place it in your /config Home Assistant path.
Those themes are modified in order to use Card-Mod and remove the header. They also have the string ```background-sidebar-sx``` which is used on this UI.

#### Alternatives
You can also create your own themes or use both [Clear](https://github.com/naofireblade/clear-theme) and [Slate](https://github.com/seangreen2/slate_theme).
If you want to create your own theme, be sure to set the following values:

```yaml
background-color:
background-sidebar-sx:
primary-background-color: 'var(--background-color)'
```
Those values are highly used on the UI code.

#### Header
A little explanation is necessay for the header too. Inside the [Theme](https://github.com/NdR91/NdR-TabletUI/tree/master/themes) you'll find two files:
- ```no-header.yaml```
- ```no-top-header.yaml```

Those two files contain the necessary instructions to hide (no-header) or compress (no-top-header) the Header.
You can pick one of them by changing the last line of both theme files. See below:

```yaml
  #HEADER
  card-mod-root-yaml: |
    paper-tabs$: |
      .not-visible {
        display: none;
      }
    .: |
      app-toolbar {
        display: none;
      }
  card-mod-theme: no-header #This removes the Header. Change to no-top-header if you want Compact Header
  ```

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
    mode: light 
    ```
- Repeat the same with the following data:
    ```yaml
    name: slate #or the name of your dark theme
    mode: dark 
    ``` 
 
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

<img src="/www/ui_tablet/screen/button-l.png" width="400" /> <img src="/www/ui_tablet/screen/button-d.png" width="400" /> 

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
  view_layout:
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
  view_layout:
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
Further update will become available soon, but meanwhile you can use this setup to get it working.

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

<img src="/www/ui_tablet/screen/climate-popup.png" width="400" /> <img src="/www/ui_tablet/screen/light-popup.png" width="400" /> 

  More info on Popups are available on [Browser Mod](https://github.com/thomasloven/hass-browser_mod) page.

### Floorplan

Creating a floorplan is quite long to explain, but I will give you a quick cue:

1. Create your floorplan with SweetHome3d - I suggest this [Guide](https://aarongodfrey.dev/home%20automation/tips_for_creating_a_3d_floorplan_using_sweethome3d/) and this [Guide](https://community.home-assistant.io/t/3d-floorplan-using-lovelace-picture-elements-card/123357) to figure out how to do it;
2. Using SweetHome3D, choose a good positioning to save some pictures of your floorplan - Use the previous guide, section [Rendering](https://aarongodfrey.dev/home%20automation/tips_for_creating_a_3d_floorplan_using_sweethome3d/#rendering). You will need the following pictures:

    - [ ] One pic of your floorplan during daytime, lights turned off (Note: this is the only one Pic which is optional);
    - [x] One pic of your floorplan during nighttime, lights turned off;
    - [x] One pic per each light turned on. This means that if you have 10 lights, you need 10 pictures. Use the nighttime "base" to do it;

    So at this time you might have 1 or 2 pictures (depending by what you decided for the first option) + a number of pictures equal to the number of the lights you want to show on your Floorplan.
3. Use Photoshop, Pixelmator or similar to cut the edges of the whole floorplan (daytime + nightime) and every single room. See the two examples below
    <details><summary><b>Night Time Floorplan</b></summary>
    <img src="/www/ui_tablet/floorplan/casa-notte.png" width="800" /> 
    </details>
    <details><summary><b>Livingroom Light 1 turned on</b></summary>
    <img src="/www/ui_tablet/floorplan/sala_luce1.png" width="800" /> 
    </details>
    <details><summary><b>Livingroom Light 2 turned on</b></summary>
    <img src="/www/ui_tablet/floorplan/sala_luce2.png" width="800" /> 
    </details>
    Dont worry if you have more lights per each room: Home Assistant (with Picture Elements card) will use the CSS property filter "mix-blend-mode: lighten" to blend all the pictures with an excelent result.
4. That's all! You now have all you need to build your Picture Elements card with your floorplan. Check the code on "Room Section" to see how tu build it. Remember that the order you place each element is very important, so starts with the pictures and then proceed with the "state icon" elements to control your switches.

<img src="/www/ui_tablet/screen/floorplan.gif" width="6000" /> 