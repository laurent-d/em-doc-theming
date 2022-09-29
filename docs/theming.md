# Eventmaker website theme squeleton

This is a starting point for an Eventmaker Website theme.

## Folder structure

    ├── themes
    │   ├── grand-conference
    |   |   ├── assets
    |   |   |   ├── // assets files
    |   |   |   ├── ...
    |   |   ├── config
    |   |   |   ├── main_settings_schema.json
    |   |   |   ├── presets.en.json
    |   |   |   ├── presets.fr.json
    |   |   |   ├── sections-groups.json
    |   |   |   ├── translations.json
    |   |   ├── layouts
    |   |   |   ├── // layout files
    |   |   |   ├── ...
    |   |   ├── sections
    |   |   |   ├── // section files
    |   |   |   ├── ...
    |   |   ├── snippets
    |   |   |   ├── // snippet files
    |   |   |   ├── ...
    |   |   ├── templates
    |   |   |   ├── // files used as templates for generic pages (such as 401, 404, ...)
    |   |   |   ├── ...
    |   |   ├── package.json
    |   |   ├── specs.yml
    └── README.md

# Working on a theme

The language used is the "Liquid" developed by Shopify. Documentation is available [here](https://shopify.github.io/liquid).

URL parameters are accessible using the Liquid object `url_params` (ex: with https://mywebsite.eventmaker.io/index?superParams=true to access the value of `superParams` you can do `url_params.superParams` which will return the String `true`).

# Config

## specs.yml

The specs file describes the theme and its components. Components described in this file are your theme Components.

It lists the paths to the theme files (sections, snippets, presets, translations, layouts, etc...).

When adding a new section for example, you need to add it in the YAML file so that it can be selected inside the website customization interface.

## package.json

This file allows you to configure the execution of the Yarn script as well as to install theme specific dependencies during Webpack builds.

## config/main_settings_schema.json

This file allows you to define global parameters of the site (social networks, title color, background color, text color, section spacing, font, etc...). Settings that are not specific to a section or that must appear in the layout must be defined in this file.

You can access these settings from a Liquid with `settings.id_of_setting` (e.g. to call the text color of the website: `settings.color_body_text`).

## config/presets.fr.json and presets.en.json

These are the default settings when creating a website with this theme.

In order to directly have a functional website you can set the default settings of your theme in this file.

## config/sections-groups.json

This file allows you to group sections by thematics and order them. Then when you access the list of sections inside the builder, they are displayed group by group (basic, media, registration, etc.).

## config/translations.json

This file allows you to configure all the texts which are not customizable, but which must be translated into the language of the website.

Each language is represented by its ISO key (en for english, fr for french, it for italian, etc...) and each translation is in the form `"key": "value"`.

The `t` Liquid object contains all the website static translations, and each translation can be accessed in Liquid files by doing `t.key_of_translation`.

If you need dynamic content in a translation use the `%{}` placeholder: `"key": "My name is %{ variable_text }"`. The variable text must be a Liquid variable. For example: `{% capture variable_text %}{{ guest.first_name }}{% endcapture %}`).

# Assets

This folder contains the CSS and Javascript resources of the theme. They are compiled by Webpack and can make dynamic calls to files contained in other folders of the repository by using @import (CSS) or import (JS).
Webpack entry point is `main.js` and `main.scss`.

# Layout

Layouts are skeletons for each page of the website. It contains the elements that must be present on each page (CSS, JS, Favicon, etc. ...).

# Sections

The sections are the sets of components that can be dynamically added to a page via the builder.

Each section consists of two distinct parts: the HTML/Liquid code and the schema.

## HTML of a section

In the HTML you can use you can use the global parameters `settings.id_of_the_setting`. But also and especially the settings of the section defined in the schema: `section.settings.id_of_the_section_setting`.

## Schema of a section

The schema gives details about the section and describes all the options the user will be able to use to customize the section.

    {% schema %}
      {
        ...
      }
    {% endschema %}


The first elements are generally information about the section.

    {% schema %}
      {
        "name": "English title - show on the website builder",
        "name_translations": { "fr": "Titre en français - afficher dans l'interface de personnalisation du site web" },
        "icon": "fa fa-font", // Font-awesome classes for the icon display in the customization interface. https://fontawesome.com/v4/icons/
        "hidden_from_user": false,
        "group_key": "basic", // In which group the section will be displayed
        "group_rank": 1000, // Its position within the group
        "settings": [
          ...
        ]
      }
    {% endschema %}

## Settings

The `settings` are described in an array of the different settings of the section. In general a setting is presented in this form:

    {
      "type": "text",
      "id": "title",
      "label": "Title",
      "label_translations": { "fr": "Titre" },
      "info": "Info display under this setting",
      "info_translations": { "fr": "Info afficher sous ce paramètre" },
      "default": "Title",
      "default_translations": { "fr": "Titre" }
    }

- `type` to choose from the following list:

| Type                    | Description |
| ----------------------- | ----------- |
| header                  | Returns a title for a better presentation of the parameters.
| text                    | Returns a simple text field.
| paragraph               | Returns a multiline text field.
| rte                     | Returns a Rich Text Editor (RTE).
| image\_picker           | Returns an image uploader.
| select                  | Returns a drop-down list of options you will provide.
| checkbox                | Returns a checkbox. |
| color                   | Returns a palette of color choices.
| guest\_category\_picker | Returns a drop-down list of the event categories.
| segments\_picker        | Returns a drop-down list of the event segments.
| website\_page\_picker   | Returns a drop-down list of the website pages.
| guest\_field\_picker    | Returns a drop-down list of the available guest fields.

- `id` is the key defining how you will access the value of this setting within the section file: ie `section.settings.title` when `id` equals `title`.
- `label` defaults to English.
- `label_translations` is a translation array. The platform is available in English, Dutch and French.
- `info` information displayed under the setting component (ie under a textfield) inside the builder (English only).
- `info_translations` works the same way as `label_translations`.
- `default` is the default value of the setting when adding the section to the website page (English only).
- `default_translations` works the same way as `label_translations`.

#### Specificities

Some types of settings need more than a `label`, an `id` and a `type` to work.

- `select`: settings of type _select_ need options to work. The empty option is generated automatically.

Example:

    {
      "type": "select",
      "id": "text_size",
      "label": "Text size",
      "label_translations": { "fr": "Taille du texte" },
      "default": "medium",
      "options": [
        {
          "label": "Medium",
          "label_translations": { "fr": "Moyen" },
          "value": "medium"
        },
        {
          "label": "Large",
          "label_translations": { "fr": "Gros" },
          "value": "large"
        }
      ]
    }

- `checkbox`: its default value will be a boolean (`true` / `false`), not a String.
- `color`: its default value must be in hexadecimal format.

#### Display condition - Show_if

For each setting it is possible to add conditional display (`show_if`).

For example we will allow the possibility to customize the **Color of the button** only if the checkbox **"Apply a custom style "** is checked:

    {
      "type": "checkbox",
      "id": "enable_custom_css",
      "label": "Apply a custom style",
      "label_translations": { "fr": "Appliquer un style personnalisé" },
      "default": false
    },
    {
      "type": "color",
      "id": "button_color",
      "label": "Button color",
      "label_translations": { "fr": "Couleur du bouton" },
      "default": "#000",
      "show_if": [
        [
          {
            "source_id": "enable_custom_css",
            "operator": "==",
            "value": true
          }
        ]
      ]
    }

It is possible to make multiple conditions with AND:

If `checkbox_1` == true AND `checkbox_2` == true

    "show_if": [
      [
        {
          "source_id": "checkbox_1",
          "operator": "==",
          "value": true
        },
        {
          "source_id": "checkbox_1",
          "operator": "==",
          "value": true
        }
      ]
    ]

But also ORs:

If `checkbox_1` == true OR `checkbox_2` == true

    "show_if": [
      [
        {
          "source_id": "checkbox_1",
          "operator": "==",
          "value": true
        }
      ],
      [
        {
          "source_id": "checkbox_1",
          "operator": "==",
          "value": true
        }
      ]
    ]

### Blocks

Blocks are elements that the user of the builder can add within the available limit (if `max_blocks` is defined). This allows to create elements of the same nature several times (e.g. in the section `feature-colums.liquid` the blocks are used to create the different columns. We can make 1, 2, 4, or more and manage their display without making several sections).

type: "text_block"` allows to make presets so that a certain number of blocks are created when adding the section to a page.

display_property` takes as value the id of one of the block settings to display its value in the customization interface (ex: `"display_property": "title"` to display the title of a block to name it and find it more easily in the customization interface of the section).

    {% schema %}
      {
        "max_blocks": 4,
        "settings": [
          ...
        ],
        "blocks": [
          {
            "type": "text_block",
            "name": "Blocks",
            "name_translations": { "fr": "Blocs" },
            "add_block": "Text for a new block",
            "add_block_translations": { "fr": "Texte pour un Nnuveau bloc" },
            "remove_block": "Text to remove this block",
            "remove_block_translations": { "fr": "Texte pour retirer ce bloc" },
            "display_property": "id_of_block_settings",
            "settings": [
              ...
            ]
          }
        ],
        "presets": [
          {
            "name": "Name of the section",
            "name_translations": { "fr": "Nom de la section" },
            "blocks": [
              {
                "type": "text_block"
              },
              {
                "type": "text_block"
              },
              {
                "type": "text_block"
              }
            ]
          }
        ]
      }
    {% endschema %}

# Snippets

Snippets are code fragments that can be used in several sections or layouts. To avoid having the same code in multiple sections, you can use a snippet by including it inside the section where it is supposed to appear.

To do this you just have to do `{% include "filename_of_the_snippet" %}`.

You can also pass parameters to this snippet: `{% include "filename_of_the_snippet", section_settings: section.settings %}` and access them in the snippet with `section_settings`.

Unlike sections, snippets do not have a schema.

# Templates

The `templates` folder contains page templates for different cases (normal operation, 401 error, 404 error, Offline, etc...).

# Liquid variables

You have access to different Liquid variables:

## Global variables

Here is the list of Liquid variables available:

    {
      "page" => {
        "name" => "Page name",
        "path_name" => "information",
        "registration_form_page?" => false,
        "registration_confirmation_page?" => false
      },
      "settings" => {},
      "pages" => {},
      "checkin_points" => [],
      "products" => [],
      "accommodations" => [],
      "accesspoints_by_id" => {},
      "event" => {},
      "menus" => {
        "name" => "Navigation",
        "key" => "navigation",
        "menu_items" => {
          "name" => "Let's go on Google",
          "type" => "external_link",
          "localized_name" => "Let's go on Google",
          "url" => "https://www.google.com/",
          "displayable?" => true
        },
      },
      "t" => {},
      "locale" => "en",
      "current_resource" => {}
    }

## Event
    {{ event }}
    {
      "title" => "My Event Name",
      "organizer" => "Eventmaker Developers",
      "description" => "This event has not begun yet",
      "timezone" => "Paris",
      "id" => "5408886b4f6905cb83000001",
      "address" => "38 Rue Laffitte, 75009 Paris",
      "grey_label_enabled" => true,
      "primary_color" => nil,
      "secondary_color" => nil,
      "cover" => nil,
      "background_url" => nil
      "photo" => nil,
      "photo_original" => nil,
      "start_date" => Wed, 31 Dec 2014 22:00:00 CET +01:00,
      "end_date" => Thu, 01 Jan 2015 07:00:00 CET +01:00,
      "guest_categories" => {}
    }

## Guests Categories

    {{ guest_category }}
    {
      "name" => "Category 1",
      "id" => "5408886b4f6905cb83000002"
      "badge_enabled" => true,
      "traits" => {},
      "price" => 0.0,
      "vat" => 0.0,
      "price_without_vat" => 0.0,
      "price_with_vat" => 0.0,
      "vat_amount" => 0.0
    }

## Guests
    {{ guest }}
    {
      "uid" => "12345654324",
      "status" => "registered",
      "email" => "john.smith@gmail.com",
      "avatar" => nil,
      "first_name" => "John",
      "last_name" => "Smith",
      "position" => "Director",
      "company_name" => "Eventmaker",
      "phone_number" => "1234567890",
      "address" => "38 Rue Laffitte, 75009 Paris",
      "country_name" => "France",
      "country" => "FRA",
      "payment_promo_code" => "MY_PROMO_CODE",
      "utm_source" => "",
      "utm_medium" => "",
      "utm_campaign" => "",
      "event_id" => "540f0cf24f6905a277000005",
      "guest_category_id" => "540992164f690552c7000004",
      "has_access_privileges" => true,
      "send_email_on_guest_category_change" => true,
      "confirmation_email_sent" => true,
      "discount_code_label" => "My discount code label",
      "price_incl_vat" => 120.00,
      "price_excl_vat" => 100.00,
      "total_vat" => 20.00,
      "guest_category" => "The associated guest category",
      "event" => "The associated event",
      "person_parent" => {},
      "guest_metadata" => {}
    }

## Sessions

    {{ session }}
    {
      "id" => "5e74f62a6864431d56271212",
      "name" => "Session title",
      "uid" => "SESSIONUID",
      "type" => "session",
      "session_type" => "conference",
      "session_type_label" => "Conférence",
      "description" => "<div><p>Ceci est la description de la session.</p></div>",
      "start_date" => "Wed, 31 Dec 2014 22:00:00 CET +01:00",
      "end_date" => "Wed, 31 Dec 2014 23:00:00 CET +01:00",
      "price" => "10.0",
      "vat" => "5.0",
      "location" => "Grande salle",
      "capacity_reached?" => false,
      "remaining_slots" => 249,
      "price_excluding_vat" => "9.5",
      "price_excluding_taxes" => "9.5",
      "traits" => {},
      "illustration_url" => nil,
      "formatted_start_day" => "31/12/2014",
      "speakers" => [],
      "exhibitors" => "[],
      "thematic_ids" => {},
      "thematics" => [],
      "speaker_roles" => [],
      "exhibitor_roles" => []
    }

# Guests List

You can see an advanced example of a guest list in the `guests-list.liquid` section.

To display the list of guests of the event you have to use the Liquid tag `{% guests_paginate %}`:

    {% guests_paginate segment_id: section.settings.segment_id.first.segment_id, per_page: section.settings.per_page %}
      {% if paginate.total_entries > 0 %}
        {% for guest in guests %}
          <p>Hello, my name is {{ guest.first_name }} {{ guest.last_name }}.</p>
        {% endfor %}
      {% endif %}
    {% endguests_paginate %}

The `{% guests_paginate %}` tag takes multiple arguments:

| Argument        | Description |
| --------------- | ----------- |
| segment\_id      | Takes the ID of the segment containing the guests you want to display. Comes from a `segments_picker` setting that takes `.first.segment_id` to get the ID. |
| per\_page        | Takes a number between 1 and 100 (if over 100, only the first 100 will be displayed). Defaults to 20. |

In this tag you also have access to several Liquid variables:

| Variable | Description |
| -------- | ----------- |
| paginate | An object containing these properties: `current_page`, `previous_page`, `next_page`, `total_entries`, `total_pages`, `per_page`, `parts`. |
| guests   | An array of guests that can be looped over to display them. |

# List of program sessions

You can see an extended example of a program session list in the `sessions-list.liquid` section.

To display the list of guests of the event you have to use the Liquid tag `{% sessions_list %}`:

    {% sessions_list session_type: section.settings.session_type_global_filter, display_speakers: section.settings.display_speakers, display_exhibitors: section.settings.display_exhibitors %}
      {% for session in sessions %}
        {{ session.name }}
      {% endfor %}
    {% endsessions_list %}

The `{% sessions_list %}` tag takes multiple arguments:

| Argument            | Description |
| ------------------- | ----------- |
| session\_type       | Comes from a setting of type `accesspoint_session_type_picker`. By default, displays all session types combined. |
| display\_speakers   | If `true`, this returns `speaker_roles`, so a hash that returns the list of guest ids that are speakers for a session. |
| display\_exhibitors | If `true`, it returns `exhibitor_roles`, which is a hash that returns the list of guest ids that are exhibitors for a session. |

In this tag you also have access to several Liquid variables:

| Variable          | Description |
| ----------------- | ----------- |
| sessions          | An array of sessions that can be looped over to display them. |
| speaker\_roles    | If the `display_speakers` argument to `sessions_list` is set to `true`, this returns a hash which lists the ids of guests who are speakers for a session. Otherwise an empty array. |
| exhibitor\_roles  | If the `display_exhibitors` argument to `sessions_list` is set to `true`, this returns a hash that lists the guest ids that are exhibitors for a session. Otherwise an empty array. |
| guests            | An array of guests that can be looped over to display them. |
| thematics\_by\_id | An object containing all thematics accessible through their ID. |
