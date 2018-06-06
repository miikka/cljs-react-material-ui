# cljs-react-material-ui

This library is interop to get [Material-UI](http://www.material-ui.com/#/) working in Clojurescript.

Current Material-UI version: `0.19.2`

###### See Om.Next example app here 
https://github.com/madvas/cljs-react-material-ui-example
git

## Warning version update >= 0.2.43
When using [AutoComplete](http://www.material-ui.com/#/components/auto-complete) use props `:dataSource` and 
`:dataSourceConfig` in camelCase form, since `data-` is interpreted as HTML data attribute

## Warning version update >= 0.2.48
Since this version you don't need to exclude `cljsjs/react` and `cljsjs/react-dom`. Material-ui bundle doesn't contain own React anymore.
Also don't forget to rename your `on-touch-tap` into `on-click`. 
Update your Clojurescript version (>= 1.9.908) 

## Installation
- Add `[cljs-react-material-ui "0.2.50"]` to your dependencies
- Add `[cljsjs/react "15.6.1-1"]` or newer version to your dependencies
- Add `[cljsjs/react-dom "15.6.1-1"]` or newer version to your dependencies

## Usage

  ```clojure
  (ns cljs-react-material-ui-example.core
    (:require [cljsjs.material-ui]  ; I recommend adding this at the beginning of core file
                                    ;  so React is always loaded first. It's not always needed
              [cljs-react-material-ui.core :as ui]
              [cljs-react-material-ui.icons :as ic]))   ; SVG icons that comes with MaterialUI
                                                        ; Including icons is not required
  ```

You must start your MaterialUI component tree with [ui/mui-theme-provider](http://www.material-ui.com/v0.15.0-beta.2/#/customization/themes), which must have exactly one direct child and defined theme. Use the same pattern when you want to change theme for some children, see example app.
```clojure
(ui/mui-theme-provider
    {:mui-theme (ui/get-mui-theme)}
    (ui/paper "Hello world"))
    
(ui/mui-theme-provider 
    {:mui-theme (ui/get-mui-theme 
        {:palette                   ; You can use either camelCase or kebab-case
            {:primary1-color (ui/color :deep-orange-a100)} 
         :raised-button 
            {:primary-text-color (ui/color :light-black) 
             :font-weight 200}})}
    (ui/raised-button
        {:label   "Click me"
         :primary true}))
         
(ui/mui-theme-provider
    {:mui-theme (ui/get-mui-theme (aget js/MaterialUIStyles "DarkRawTheme"))}
    (ui/paper "Hello dark world"))
```

You can use all components (icons also) in their kebab-case form. Either with props or without.
```clojure
(ui/radio-button
    {:value          "some_val"
     :label          "Yes"
     :class-name     "my-radio-class"
     :checked-icon   (ic/action-favorite)
     :unchecked-icon (ic/action-favorite-border)})
     
 (ui/table-row
    (ui/table-header-column "Name")
    (ui/table-header-column "Date"))
```

##### Global objects
```clojure
js/MaterialUI ; Contains constructors to all components. No need to use directly.
js/MaterialUIStyles ; Contains everything from material-ui/src/styles/index.js
js/MaterialUISvgIcons ; Contains constructors to all icons. Exists only when you
                      ; include icons in your code. No need to use directly.
js/MaterialUIUtils ; Contains some of util functions provided by MaterialUI
```

##### Using with Reagent
Works with `reagent "0.6.0-alpha"` and up. So the dependency may be specified like this:

`[reagent "0.7.0"]`

A simple Reagent example is as follows:

```clojure
(ns crmui-reagent.core
  (:require
    [cljsjs.material-ui]
    [cljs-react-material-ui.core :refer [get-mui-theme color]]
    [cljs-react-material-ui.reagent :as ui]
    [cljs-react-material-ui.icons :as ic]
    [reagent.core :as r]))
    
; Example with various components
(defn home-page []
  [ui/mui-theme-provider
   {:mui-theme (get-mui-theme
                 {:palette {:text-color (color :green600)}})}
   [:div
    [ui/app-bar {:title "Title"
                  :icon-element-right
                   (r/as-element [ui/icon-button
                                    (ic/action-account-balance-wallet)])}]
    [ui/paper
     [:div "Hello"]
     [ui/mui-theme-provider
      {:mui-theme (get-mui-theme {:palette {:text-color (color :blue200)}})}
      [ui/raised-button {:label "Blue button"}]]
     (ic/action-home {:color (color :grey600)})
     [ui/raised-button {:label        "Click me"
                         :icon         (ic/social-group)
                         :on-click     #(println "clicked")}]]]])
    
```
&nbsp;
##### Using with Rum
&nbsp;
```clojure
(ns crmui-rum.core
  (:require
    [cljs-react-material-ui.core :refer [get-mui-theme color]]
    [cljs-react-material-ui.icons :as ic]
    [cljs-react-material-ui.rum :as ui]
    [rum.core :as rum]))
    
(rum/defc thing1
          []
          [:div "content1"])

(defn home-page []
  (ui/mui-theme-provider
    {:mui-theme (get-mui-theme)}
    [:div
     (ui/app-bar {:icon-element-right (ui/icon-button (ic/action-accessibility))})
     (ui/tabs
       (ui/tab {:label "one"}
                [:div ["hey"
                       (ui/paper "yes")]])
       (ui/tab {:label "two"} (thing1))
       (ui/tab {:label "drei"}
                [:div
                 (ui/paper {} "Ima paper")]))]))
    
```

## Selectable List
This library provides pre-made selectable list, whrereas in MaterialUI has to be created manually.
You can access orig `makeSelectable` function as `cljs-react-material-ui.core/make-selectable`
See example in reagent:
```clojure
(defn selectable-list-example []
  (let [list-item-selected (atom 1)]
    (fn []
      [ui/selectable-list
       {:value @list-item-selected
        :on-change (fn [event value]
                     (reset! list-item-selected value))}
       [ui/subheader {} "Selectable Contacts"]
       [ui/list-item
        {:value 1
         :primary-text "Brendan Lim"
         :nested-items
         [(r/as-element
            [ui/list-item
             {:value 2
              :key 8
              :primary-text "Grace Ng"}])]}]
       [ui/list-item
        {:value 3
         :primary-text "Kerem Suer"}]
       [ui/list-item
        {:value 4
         :primary-text "Eric Hoffman"}]
       [ui/list-item
        {:value 5
         :primary-text "Raquel Parrado"}]])))
```


## MaterialUI Chip Input
If you feel like using [MaterialUIChipInput](https://github.com/TeamWertarbyte/material-ui-chip-input) all you need to
do is add `[cljsjs/material-ui-chip-input "0.17.0-0"]` (or newer version) into your project.clj. 
And now you can use chip-input according to your favorite framework namespace.
```clojure
(ns my.app
    (:require 
      [cljs-react-material-ui.chip-input.core :refer [chip-input]]
      [cljs-react-material-ui.chip-input.reagent :refer [chip-input]]
      [cljs-react-material-ui.chip-input.rum :refer [chip-input]]))
```


## Troubleshooting
##### Caret moves to the end when editing a text field
This happens due to async rendering of clojurescript react libraries.
Luckily, there is a workaround, which fixes most of use cases: Instead of `:value` prop use `:default-value` e.g:
```clojure
(defn simple-text-field [text]
  (let [text-state (r/atom text)]
    (fn []
      [rui/text-field
       {:id "example"
        :default-value @text-state
        :on-change (fn [e] (reset! text-state (.. e -target -value)))}])))
```