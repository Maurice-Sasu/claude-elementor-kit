# Elementor Pro + ACF Pro Skill

You are operating against a WordPress site with the **elementor-mcp** server connected via the WordPress MCP Adapter. This site uses **Elementor Pro**, **Advanced Custom Fields Pro (ACF Pro)**, and **WP-CLI** for full autonomy. This skill file is the single source of truth for how to build, edit, and manage this site.

---

## 🛑 First Action Protocol — ASK BEFORE DOING

When this skill is invoked, do not start running tools. Ask the user what they want first.

If the invocation message already contains a clear task — *"build the single property template"*, *"add the enquiry form to the contact page"* — proceed directly.

Otherwise respond with this menu and wait:

```
What would you like to do?

  1. Build       — create a new page, template, or section
  2. Edit        — change something on an existing page or template
  3. Reference   — inspect current state (pages, templates, fields, colors)
  4. Portfolio   — set up or manage the Properties CPT, ACF fields, or templates
  5. Explore     — show me what's possible
```

Wait for the user to choose before invoking any tool other than the read-only smoke tests below.

### Safe read-only calls (always fine to run first)

```
mcp__elementor__elementor-mcp-list-pages
mcp__elementor__elementor-mcp-get-global-settings
```

---

## Core Principles — Read Before Anything Else

### 1. Elementor Pro Only — No HTML Widgets

This site has **Elementor Pro**. Every section, layout, content block, form, and navigation element must be built with native Elementor or Elementor Pro widgets. The HTML widget is **banned** except for one narrow exception defined at the bottom of this file.

Do not think "I'll just drop this as HTML — it's faster." That produces pages the user cannot edit visually, cannot reuse globally, and cannot maintain. Always find the native Pro widget first.

### 2. Theme Builder for All Global Templates

Because this site has Elementor Pro, **all** of the following are built exclusively through the Theme Builder — not as regular pages:

- Site-wide Header
- Site-wide Footer
- Single Property template (single post for the Properties CPT)
- Loop Item template (the card used inside Loop Grid widgets)
- Archive template (if needed for property listing pages)

Never build headers or footers as regular Elementor pages and include them via shortcode or PHP. Always use Theme Builder.

### 3. ACF Pro for All Dynamic Property Data

All property-specific data (price, location, status, specs, gallery, etc.) lives in ACF Pro custom fields attached to the Properties custom post type. In Elementor templates, all dynamic data is pulled through **Elementor's Dynamic Tags** system, which natively reads ACF fields. Never hardcode property data inside an Elementor template.

### 4. Full Autonomy — No Manual Steps for the User

When setting up CPTs, taxonomies, ACF field groups, or any WordPress configuration, handle everything via WP-CLI or the MCP. Do not ask the user to go into WP Admin and click through menus unless a task is genuinely impossible to automate (e.g. a one-time license activation). Always explain what you're doing and why.

---

## Plugin Stack

| Role | Plugin |
|---|---|
| Page Builder | Elementor Pro |
| Dynamic Fields | Advanced Custom Fields Pro |
| Theme Templates | Elementor Theme Builder (included in Pro) |
| Forms | Elementor Pro Form Widget |
| CLI Automation | WP-CLI |
| Custom Post Types | Registered via WP-CLI / `functions.php` |

---

## First-Session Setup

If the MCP is not yet connected:

1. Check whether the site is Local-by-Flywheel or a live host.
2. Run the bundled setup script:
   ```bash
   bash ~/.claude/scripts/setup-elementor-mcp.sh
   ```
3. After the script completes, tell the user to quit and reopen Claude Code so `.mcp.json` is picked up.
4. On reopen, run `list-pages` to confirm auth.

---

## Design Tokens — Global Colors and Typography

Before building any page, establish the design tokens that will be reused everywhere.

### Brand Palette

```
--navy-900 / Dark Green:  #0d130c   (darkest background)
--navy-800:               #141d13
--navy-700:               #1a2619
--navy-500 / Brand Green: #243421   (primary brand green)
--gold-500 / Brand Gold:  #7e745c   (primary accent)
--gold-400:               #9a8f76
--gold-300:               #b3a992
--stone-500 / Grey:       #606060
--ivory:                  #f8f4ed   (light background / text on dark)
--white:                  #ffffff
--black:                  #000000
```

### Typography

- **Display / Headings:** Cormorant Garamond (Google Fonts) — weights 300, 400, 500; italic variants enabled
- **Body / UI:** Manrope (Google Fonts) — weights 300, 400, 500, 600, 700
- **Letter spacing (eyebrows/labels):** 0.2em–0.28em, uppercase, Manrope 600
- **Base font size:** 16px

Set these via:
```
mcp__elementor__elementor-mcp-update-global-colors
mcp__elementor__elementor-mcp-update-global-typography
```

---

## The Widget-First Rule — Native Pro Widgets

Every content need maps to a native Elementor Pro widget. Use this table before reaching for anything else.

| Content Need | Elementor Pro Widget |
|---|---|
| Headings (H1–H6) | Heading widget |
| Body text / rich text | Text Editor widget |
| Single image | Image widget |
| Image gallery | Gallery widget (or Media Carousel) |
| Button / CTA | Button widget |
| Layout rows / columns | Container (Flexbox) |
| Navigation | Nav Menu widget (Pro) |
| Site logo | Site Logo widget (Pro) |
| Forms | Form widget (Pro) |
| Icon + text list | Icon List widget |
| Tabs | Tabs widget (Pro) |
| Accordion / FAQ | Accordion widget |
| Slider / carousel | Slides widget or Media Carousel |
| Video | Video widget |
| Dynamic post grid / loop | Loop Grid widget (Pro) |
| Single ACF text field | Text widget + Dynamic Tag |
| Single ACF image | Image widget + Dynamic Tag |
| ACF gallery | Gallery widget + Dynamic Tag |
| Post title (in template) | Post Title widget (Pro) |
| Post featured image | Featured Image widget (Pro) |
| Post content | Post Content widget (Pro) |
| Post excerpt | Post Excerpt widget (Pro) |
| Breadcrumbs | Breadcrumbs widget (Pro) |
| Archive title | Archive Title widget (Pro) |
| Search form | Search Form widget |
| Social icons | Social Icons widget |
| Divider / separator | Divider widget |
| Spacer | Spacer widget |
| Icon | Icon widget |
| Star rating | Star Rating widget |
| Price list | Price List widget |
| Call to action box | Call to Action widget (Pro) |
| Flip box | Flip Box widget (Pro) |
| Animated headline | Animated Headline widget (Pro) |
| Countdown timer | Countdown widget (Pro) |
| Progress bar | Progress Bar widget |
| Table of contents | Table of Contents widget (Pro) |
| Blockquote | Blockquote widget |

### The Only Permitted HTML Widget Exception

The HTML widget may only be used for **scoped CSS overrides** when a native Elementor Pro widget has a specific styling detail that cannot be reached through the widget's own Style tab controls. Examples:

- Overriding the active state colour of a Nav Menu item that the widget's controls don't expose
- Adding a CSS custom property that Elementor's inline controls don't support
- A keyframe animation referenced by a widget that Elementor Motions can't produce

**Rules for this exception:**
- The HTML widget must contain **only a `<style>` block** — no markup, no rendered content whatsoever
- Every selector must be scoped to the specific Elementor element ID (e.g. `.elementor-element-f8d1545 .e-n-tab-title`)
- Add a comment explaining why a native widget control couldn't handle this
- Tell the user this widget is styling-only and cannot be edited visually

If you find yourself adding `<div>`, `<a>`, `<img>`, `<p>`, or any content markup inside an HTML widget — stop immediately. That content belongs in native widgets.

---

## Container (Flexbox) Layout Convention

All layout is done with Elementor's Container widget in Flexbox mode. Key settings:

```
Content Width:     Boxed (for centred content) or Full Width (for edge-to-edge sections)
Boxed Width:       1280px (standard) or 1440px (wide)
Flex Direction:    Row (columns) or Column (stacked)
Justify Content:   flex-start | center | space-between | space-around
Align Items:       flex-start | center | stretch
Gap:               set in px — standard column gap 32px, section gap 80px
Min Height:        use vh for full-screen hero sections (e.g. 100vh)
Padding:           set per side; standard section padding 100px top/bottom, 32px sides on mobile
```

Always set `typography_typography: "custom"` before setting any other typography control — without it, font family, size, weight, and line-height settings are silently ignored.

---

## Typography Control Convention

When setting typography on any widget, always include the enable flag first:

```js
typography_typography: "custom",       // required — enables all other typography_* keys
typography_font_family: "Cormorant Garamond",
typography_font_size: { size: 64, unit: "px" },
typography_font_weight: "300",
typography_line_height: { size: 1.05, unit: "em" },
typography_letter_spacing: { size: 0, unit: "px" },
typography_font_style: "normal",       // or "italic"
```

For eyebrow labels (small uppercase tracking text):
```js
typography_typography: "custom",
typography_font_family: "Manrope",
typography_font_size: { size: 11, unit: "px" },
typography_font_weight: "600",
typography_letter_spacing: { size: 2.8, unit: "px" },
typography_text_transform: "uppercase",
```

---

## Theme Builder — Headers and Footers

### Step 1 — Create the Header Template

```
mcp__elementor__elementor-mcp-create-theme-template({
  type: "header",
  title: "Site Header",
  conditions: [{ type: "general", name: "general" }]   // displays site-wide
})
```

### Step 2 — Build the Header Layout

The header is a full-width container with three children:

**Left — Site Logo**
- Use the **Site Logo widget** (Pro). Point it to `images/logo-white.png`. Set a max-height of 72px.

**Centre — Nav Menu**
- Use the **Nav Menu widget** (Pro). Select the WordPress menu by name (e.g. "Main Navigation"). Set layout to Horizontal. Style the active item with `--gold-500` underline. Configure the hamburger breakpoint for mobile.

**Right — CTA Button**
- Use the **Button widget**. Label: "Private Consultation →". Style as outlined (transparent background, `--ivory` border, `--ivory` text). On hover: fill with `--gold-500`.

Header container settings:
```
Background: #0d130c (--navy-900) with 0.92 opacity + backdrop-filter blur (set via Motion > Sticky)
Position: Sticky (set via Advanced > Motion Effects > Sticky: Top)
Z-Index: 100
Padding: 16px top/bottom
```

### Step 3 — Create the Footer Template

```
mcp__elementor__elementor-mcp-create-theme-template({
  type: "footer",
  title: "Site Footer",
  conditions: [{ type: "general", name: "general" }]
})
```

Footer layout: full-width dark container (`--navy-900` background), inner boxed container with:
- **Row 1:** 5-column grid — Brand column (Site Logo + tagline Text Editor + italic motto) + 4 nav link columns (Icon List widgets for each group)
- **Row 2:** Single-row container with copyright Text Editor (left), Social Icons widget (centre), and legal links Nav Menu or Text Editor (right)

Footer Social Icons widget: use the Social Icons widget (Pro). Add Instagram, LinkedIn, X, Facebook. Style: size 18px, colour `--ivory` at 60% opacity, hover colour `--gold-400`.

---

## Elementor Pro Form Widget

All forms on this site use the native **Elementor Pro Form widget**. Never use a third-party form plugin, an HTML `<form>` tag, or a shortcode-based form.

### Standard Enquiry Form Structure

Add a Form widget and configure the following fields:

| Field Label | Field Type | Required |
|---|---|---|
| Full Name | Text | Yes |
| Email | Email | Yes |
| Phone / WhatsApp | Tel | No |
| Unit Interest | Select (options: 1-Bed, 2-Bed, 3-Bed, 4-Bed+, Penthouse, Open to recommendation) | No |
| Investment Budget | Select (options: Up to $500K, $500K–$1M, $1M–$2M, $2M–$5M, $5M+) | No |
| Intent | Select (options: Own Use, Investment / Rental, Both) | No |
| Message | Textarea | No |

**Submit button:** Label "Submit Enquiry →", full-width, background `--navy-500`, text `--ivory`, no border radius.

**Actions After Submit:** Configure Email action — send to site admin email. Subject: "New Enquiry — [field id="full_name"]". Add a Success Message action: "Thank you. We'll be in touch within one business day."

**Form styling** (via the Form widget's Style tab — no HTML widget needed):
- Labels: Manrope, 11px, uppercase, letter-spacing 2.8px, colour `--stone-500`
- Inputs: Cormorant Garamond, 18px, transparent background, border-bottom only (`--stone-300`), no border radius, padding 12px 0
- Focus state: border-bottom colour `--gold-500`
- Submit button: Manrope, 13px, uppercase, letter-spacing 2px, background `--navy-500`, hover `--gold-500`

---

## Properties Custom Post Type — Full Setup

The Properties CPT powers the entire portfolio section. Set it up in this exact order.

### Step 1 — Register the Custom Post Type via WP-CLI

Run the following WP-CLI command to register the CPT by adding code to `functions.php`:

```bash
wp eval '
function pegasus_register_properties_cpt() {
  register_post_type("properties", [
    "label"               => "Properties",
    "labels"              => [
      "name"               => "Properties",
      "singular_name"      => "Property",
      "add_new_item"       => "Add New Property",
      "edit_item"          => "Edit Property",
      "view_item"          => "View Property",
      "search_items"       => "Search Properties",
      "not_found"          => "No properties found",
    ],
    "public"              => true,
    "has_archive"         => true,
    "show_in_rest"        => true,
    "supports"            => ["title", "editor", "thumbnail", "excerpt", "revisions"],
    "menu_icon"           => "dashicons-building",
    "rewrite"             => ["slug" => "properties"],
    "show_in_nav_menus"   => true,
  ]);
}
add_action("init", "pegasus_register_properties_cpt");
'
```

Then add this function permanently to `functions.php`:

```bash
wp eval-file /path/to/register-cpt.php
# OR directly append to functions.php via bash
```

After registering, flush rewrite rules:

```bash
wp rewrite flush
```

### Step 2 — Register Taxonomies via WP-CLI

Register three taxonomies: **Property Location**, **Property Status**, and **Property Type**.

```bash
wp eval '
function pegasus_register_property_taxonomies() {

  // Location taxonomy
  register_taxonomy("property_location", "properties", [
    "label"        => "Locations",
    "labels"       => ["singular_name" => "Location", "add_new_item" => "Add New Location"],
    "hierarchical" => true,
    "public"       => true,
    "show_in_rest" => true,
    "rewrite"      => ["slug" => "property-location"],
  ]);

  // Status taxonomy
  register_taxonomy("property_status", "properties", [
    "label"        => "Statuses",
    "labels"       => ["singular_name" => "Status", "add_new_item" => "Add New Status"],
    "hierarchical" => true,
    "public"       => true,
    "show_in_rest" => true,
    "rewrite"      => ["slug" => "property-status"],
  ]);

  // Type taxonomy
  register_taxonomy("property_type", "properties", [
    "label"        => "Types",
    "labels"       => ["singular_name" => "Type", "add_new_item" => "Add New Type"],
    "hierarchical" => true,
    "public"       => true,
    "show_in_rest" => true,
    "rewrite"      => ["slug" => "property-type"],
  ]);
}
add_action("init", "pegasus_register_property_taxonomies");
'
```

### Step 3 — Populate Default Taxonomy Terms via WP-CLI

```bash
# Property Location terms
wp term create property_location "Accra" --slug=accra
wp term create property_location "Dubai" --slug=dubai
wp term create property_location "Airport City" --slug=airport-city --parent=$(wp term get property_location accra --field=term_id)
wp term create property_location "Airport Residential" --slug=airport-residential --parent=$(wp term get property_location accra --field=term_id)
wp term create property_location "Central Business District" --slug=cbd --parent=$(wp term get property_location accra --field=term_id)
wp term create property_location "East Legon" --slug=east-legon --parent=$(wp term get property_location accra --field=term_id)
wp term create property_location "Dzorwulu" --slug=dzorwulu --parent=$(wp term get property_location accra --field=term_id)
wp term create property_location "La Palm Coastal Corridor" --slug=la-palm --parent=$(wp term get property_location accra --field=term_id)
wp term create property_location "Downtown Dubai" --slug=downtown-dubai --parent=$(wp term get property_location dubai --field=term_id)
wp term create property_location "Dubai Marina" --slug=dubai-marina --parent=$(wp term get property_location dubai --field=term_id)
wp term create property_location "Business Bay" --slug=business-bay --parent=$(wp term get property_location dubai --field=term_id)

# Property Status terms
wp term create property_status "Off-Plan" --slug=off-plan
wp term create property_status "Under Construction" --slug=under-construction
wp term create property_status "Completed" --slug=completed
wp term create property_status "Resale" --slug=resale
wp term create property_status "Coming Soon" --slug=coming-soon
wp term create property_status "By Invitation" --slug=by-invitation
wp term create property_status "Launching 2026" --slug=launching-2026

# Property Type terms
wp term create property_type "Branded Residences" --slug=branded-residences
wp term create property_type "Mixed-Use" --slug=mixed-use
wp term create property_type "Boutique Apartments" --slug=boutique-apartments
wp term create property_type "Townhouses" --slug=townhouses
wp term create property_type "Penthouses" --slug=penthouses
wp term create property_type "Hotel-Branded" --slug=hotel-branded
wp term create property_type "Resale Inventory" --slug=resale-inventory
```

### Step 4 — Create ACF Pro Field Group via WP-CLI or JSON Import

Create the ACF field group for the Properties CPT. The cleanest method is importing via JSON. Write this JSON to a file and import it:

```bash
wp acf import --json-file=/path/to/properties-fields.json
```

The JSON defines the following fields:

**Group name:** Property Details
**Location rule:** Post Type is equal to `properties`

```json
{
  "key": "group_properties",
  "title": "Property Details",
  "fields": [
    {
      "key": "field_property_tagline",
      "label": "Tagline",
      "name": "property_tagline",
      "type": "text",
      "instructions": "Short descriptor shown below the title. E.g. 'Hilton-Adjacent · Under Construction'"
    },
    {
      "key": "field_property_location_text",
      "label": "Location (Display Text)",
      "name": "property_location_text",
      "type": "text",
      "instructions": "Full location string for display. E.g. 'Central Business District, Accra, Ghana'"
    },
    {
      "key": "field_property_price_from",
      "label": "Price From (USD)",
      "name": "property_price_from",
      "type": "text",
      "instructions": "E.g. $620,000 or AED 3.2M / $870,000"
    },
    {
      "key": "field_property_total_units",
      "label": "Total Units",
      "name": "property_total_units",
      "type": "text"
    },
    {
      "key": "field_property_bua",
      "label": "Built-Up Area (sqm)",
      "name": "property_bua",
      "type": "text",
      "instructions": "E.g. 17,435 sqm"
    },
    {
      "key": "field_property_handover",
      "label": "Handover / Delivery",
      "name": "property_handover",
      "type": "text",
      "instructions": "E.g. Q3 2027 or 2028"
    },
    {
      "key": "field_property_branding",
      "label": "Hotel Branding",
      "name": "property_branding",
      "type": "text",
      "instructions": "E.g. Ritz-Carlton, Hilton-Adjacent, BWH Hotels. Leave blank if none."
    },
    {
      "key": "field_property_yield",
      "label": "Projected Yield",
      "name": "property_yield",
      "type": "text",
      "instructions": "E.g. 9–11% Gross"
    },
    {
      "key": "field_property_payment_plan",
      "label": "Payment Plan",
      "name": "property_payment_plan",
      "type": "text",
      "instructions": "E.g. On Request, Available, Stage-based"
    },
    {
      "key": "field_property_unit_types",
      "label": "Unit Types",
      "name": "property_unit_types",
      "type": "text",
      "instructions": "E.g. 1–4 Bed + Penthouses, Studios to 4-Bedrooms"
    },
    {
      "key": "field_property_stat_1_value",
      "label": "Hero Stat 1 — Value",
      "name": "property_stat_1_value",
      "type": "text",
      "instructions": "E.g. 142"
    },
    {
      "key": "field_property_stat_1_label",
      "label": "Hero Stat 1 — Label",
      "name": "property_stat_1_label",
      "type": "text",
      "instructions": "E.g. Units"
    },
    {
      "key": "field_property_stat_2_value",
      "label": "Hero Stat 2 — Value",
      "name": "property_stat_2_value",
      "type": "text"
    },
    {
      "key": "field_property_stat_2_label",
      "label": "Hero Stat 2 — Label",
      "name": "property_stat_2_label",
      "type": "text"
    },
    {
      "key": "field_property_stat_3_value",
      "label": "Hero Stat 3 — Value",
      "name": "property_stat_3_value",
      "type": "text"
    },
    {
      "key": "field_property_stat_3_label",
      "label": "Hero Stat 3 — Label",
      "name": "property_stat_3_label",
      "type": "text"
    },
    {
      "key": "field_property_stat_4_value",
      "label": "Hero Stat 4 — Value",
      "name": "property_stat_4_value",
      "type": "text"
    },
    {
      "key": "field_property_stat_4_label",
      "label": "Hero Stat 4 — Label",
      "name": "property_stat_4_label",
      "type": "text"
    },
    {
      "key": "field_property_gallery",
      "label": "Property Gallery",
      "name": "property_gallery",
      "type": "gallery",
      "instructions": "Upload additional property images (separate from the featured image). Used in the gallery section of the single property page.",
      "return_format": "array",
      "preview_size": "medium",
      "library": "all",
      "min": 0,
      "max": 20
    },
    {
      "key": "field_property_overview_heading",
      "label": "Overview Heading",
      "name": "property_overview_heading",
      "type": "text",
      "instructions": "E.g. Accra's most architecturally ambitious address."
    },
    {
      "key": "field_property_overview_body",
      "label": "Overview Body",
      "name": "property_overview_body",
      "type": "wysiwyg",
      "instructions": "Full property description — 3 paragraphs. Supports basic HTML formatting."
    },
    {
      "key": "field_property_investment_case",
      "label": "Investment Case",
      "name": "property_investment_case",
      "type": "repeater",
      "instructions": "Add up to 4 investment reasons.",
      "min": 1,
      "max": 4,
      "layout": "block",
      "sub_fields": [
        {
          "key": "field_invest_title",
          "label": "Title",
          "name": "invest_title",
          "type": "text"
        },
        {
          "key": "field_invest_body",
          "label": "Body",
          "name": "invest_body",
          "type": "textarea"
        }
      ]
    },
    {
      "key": "field_property_highlights",
      "label": "Key Features / Highlights",
      "name": "property_highlights",
      "type": "repeater",
      "instructions": "Add up to 6 key feature cards.",
      "min": 1,
      "max": 6,
      "layout": "block",
      "sub_fields": [
        {
          "key": "field_highlight_title",
          "label": "Title",
          "name": "highlight_title",
          "type": "text"
        },
        {
          "key": "field_highlight_body",
          "label": "Body",
          "name": "highlight_body",
          "type": "textarea"
        }
      ]
    },
    {
      "key": "field_property_enquiry_price_label",
      "label": "Enquiry Section — Price Label",
      "name": "property_enquiry_price_label",
      "type": "text",
      "instructions": "Text shown next to the form. E.g. From $620,000 or Pricing on Qualification"
    }
  ],
  "location": [
    [{ "param": "post_type", "operator": "==", "value": "properties" }]
  ],
  "menu_order": 0,
  "position": "normal",
  "style": "default",
  "label_placement": "top",
  "instruction_placement": "label",
  "active": true
}
```

Verify the import worked:
```bash
wp acf field-group list
```

### Step 5 — Verify the Full Setup

```bash
# Confirm CPT is registered
wp post-type get properties --fields=name,label,has_archive,public

# Confirm taxonomies are registered
wp taxonomy list --fields=name,label,hierarchical

# Confirm terms exist
wp term list property_location --fields=name,slug,count
wp term list property_status --fields=name,slug,count
wp term list property_type --fields=name,slug,count

# Confirm ACF field group is active
wp acf field-group list

# Flush rewrites
wp rewrite flush
```

---

## Adding a Property Post via WP-CLI

When the user wants to create a property, do it fully via WP-CLI:

```bash
# 1. Create the post
wp post create \
  --post_type=properties \
  --post_title="The Octagon" \
  --post_status=publish \
  --post_excerpt="Hilton-adjacent branded residences in Accra's Central Business District." \
  --porcelain

# Note the returned post ID — use it for all subsequent commands

# 2. Set taxonomy terms
wp post term set <post_id> property_location cbd
wp post term set <post_id> property_status under-construction
wp post term set <post_id> property_type branded-residences

# 3. Set ACF fields
wp post meta update <post_id> property_tagline "Hilton-Adjacent · Under Construction"
wp post meta update <post_id> property_location_text "Central Business District, Accra, Ghana"
wp post meta update <post_id> property_price_from "$620,000"
wp post meta update <post_id> property_total_units "142"
wp post meta update <post_id> property_bua "17,435 sqm"
wp post meta update <post_id> property_branding "Hilton-Adjacent"
wp post meta update <post_id> property_yield "9–12% Gross"
wp post meta update <post_id> property_unit_types "1–4 Bed + Penthouses"
wp post meta update <post_id> property_stat_1_value "142"
wp post meta update <post_id> property_stat_1_label "Units"
wp post meta update <post_id> property_stat_2_value "8"
wp post meta update <post_id> property_stat_2_label "Penthouses"
wp post meta update <post_id> property_stat_3_value "17,435"
wp post meta update <post_id> property_stat_3_label "sqm BUA"
wp post meta update <post_id> property_enquiry_price_label "From $620,000"

# 4. Set featured image (if image already in media library)
wp post meta update <post_id> _thumbnail_id <attachment_id>
```

For ACF Repeater fields (highlights, investment case), set the serialised value via PHP eval or the REST API since WP-CLI meta update doesn't handle ACF's serialisation automatically:

```bash
wp eval '
$post_id = <post_id>;
update_field("property_highlights", [
  ["highlight_title" => "Hilton-Adjacent Branding", "highlight_body" => "Co-located with Hilton hospitality infrastructure..."],
  ["highlight_title" => "8 Sky Penthouses", "highlight_body" => "The top eight floors house bespoke sky penthouses..."],
], $post_id);

update_field("property_investment_case", [
  ["invest_title" => "First-Mover in CBD Branded Stock", "invest_body" => "There is no comparable branded residential product..."],
  ["invest_title" => "Corporate Tenant Pipeline", "invest_body" => "Ghana hosts the AfCFTA Secretariat..."],
], $post_id);
'
```

---

## Theme Builder — Single Property Template

### When to build this

Build the Single Property template **once** — it applies automatically to every property post. When the user asks to build or redesign the single property page, always do it through Theme Builder, never as a regular page.

### Step 1 — Create the template

```
mcp__elementor__elementor-mcp-create-theme-template({
  type: "single",
  title: "Single Property",
  conditions: [{ type: "singular", name: "properties" }]
})
```

### Step 2 — Get the design reference

Ask the user: "Please share the URL of the design reference for the single property page." When the user provides a URL, open it and map every section to the native widget strategy below.

### Step 3 — Build the template section by section

The standard single property template structure (based on the existing HTML portfolio pages) is:

**Section 1: Hero**
- Full-screen Container (100vh, `min_height: {size: 100, unit: "vh"}`)
- Background: Featured Image (Dynamic Tag → Featured Image) with dark overlay (`background_overlay` settings, opacity ~0.75)
- Inside: Breadcrumbs widget (Pro) → Heading widget (Post Title, Dynamic Tag) → Text Editor (tagline, ACF Dynamic Tag `property_tagline`) → 4-column stats row using Text Editor or Heading widgets with ACF Dynamic Tags for each stat value and label

**Section 2: Sticky Nav Bar**
- Sticky container (position: sticky, top: 80px, z-index: 50)
- Background: `rgba(13,19,12,0.97)` with backdrop blur
- Left: Heading widget (Post Title, Dynamic Tag)
- Right: Button widget → "Enquire Now →" (anchor to enquiry section), Button widget → "Gallery" (anchor to gallery section)

**Section 3: Overview + Specs Panel**
- Two-column container (70% / 30%)
- Left column: Eyebrow Text widget, Heading widget (ACF `property_overview_heading`), Text Editor widget (ACF `property_overview_body` with WYSIWYG Dynamic Tag), two Button widgets (Request Floor Plans, Download Brochure)
- Right column (sticky): Heading widget (specs title), then repeated Icon List or Text widgets showing each spec — use ACF Dynamic Tags for property_price_from, property_total_units, property_bua, property_unit_types, property_branding, property_handover, property_payment_plan. Add two Button widgets at the bottom (Register Interest, Speak to Advisor).

**Section 4: Gallery**
- Gallery widget (Pro) — source: ACF Field (Dynamic Tag → `property_gallery`). Layout: Grid, columns: 3. Lightbox: enabled.

**Section 5: Key Features / Highlights**
- Dark background container (`--navy-900`)
- Heading widgets for section title
- Loop Grid widget (Pro) — powered by a second field group query on the highlights repeater, OR manually build 6 card containers using ACF Dynamic Tags on a repeater-aware widget. Use the Elementor Pro Repeater Field dynamic tag to loop through `property_highlights`.

**Section 6: Feature Split Panels**
- Two alternating full-width 50/50 containers
- Left: Image widget (source: ACF gallery item or specific image field)
- Right: Text Editor container with eyebrow, Heading, Text Editor body

**Section 7: Investment Case**
- Light background container (`--ivory`)
- Heading widgets for section title
- 2-column grid of investment reason cards using Repeater Field dynamic tags on `property_investment_case`

**Section 8: Enquiry Form**
- Dark background container (`--navy-900`) with ID anchor `enquiry`
- Two-column layout: left column (Heading widget with ACF enquiry_price_label, Text Editors for location/contact details), right column (Elementor Pro Form widget — standard enquiry form structure defined in the Forms section above)

**Section 9: Related Properties**
- Light grey background container
- Heading widgets for section title
- Loop Grid widget (Pro) — query: Properties post type, exclude current post, limit 3, order by date. Loop Item template: Property Card (built separately — see Loop Item section below)

### Dynamic Tags Reference for Single Property Template

| Widget | Dynamic Tag | ACF Field Key |
|---|---|---|
| Featured Image (hero bg) | Post Featured Image | — |
| Post Title | Post Title | — |
| Tagline | ACF Field → Text | property_tagline |
| Location display | ACF Field → Text | property_location_text |
| Price From | ACF Field → Text | property_price_from |
| Total Units | ACF Field → Text | property_total_units |
| BUA | ACF Field → Text | property_bua |
| Branding | ACF Field → Text | property_branding |
| Yield | ACF Field → Text | property_yield |
| Unit Types | ACF Field → Text | property_unit_types |
| Handover | ACF Field → Text | property_handover |
| Payment Plan | ACF Field → Text | property_payment_plan |
| Overview Heading | ACF Field → Text | property_overview_heading |
| Overview Body | ACF Field → WYSIWYG | property_overview_body |
| Gallery images | ACF Field → Gallery | property_gallery |
| Highlights (repeater) | ACF Repeater Field | property_highlights |
| Investment Case (repeater) | ACF Repeater Field | property_investment_case |
| Enquiry price label | ACF Field → Text | property_enquiry_price_label |
| Stat 1 Value | ACF Field → Text | property_stat_1_value |
| Stat 1 Label | ACF Field → Text | property_stat_1_label |
| Stat 2 Value | ACF Field → Text | property_stat_2_value |
| Stat 2 Label | ACF Field → Text | property_stat_2_label |
| Stat 3 Value | ACF Field → Text | property_stat_3_value |
| Stat 3 Label | ACF Field → Text | property_stat_3_label |
| Stat 4 Value | ACF Field → Text | property_stat_4_value |
| Stat 4 Label | ACF Field → Text | property_stat_4_label |

---

## Theme Builder — Loop Item Template (Property Card)

The Loop Item template defines what a single property card looks like when used inside a Loop Grid widget (on archive pages, the portfolio page, the homepage featured grid, and the related properties section).

### Step 1 — Create the Loop Item template

```
mcp__elementor__elementor-mcp-create-theme-template({
  type: "loop-item",
  title: "Property Card",
  conditions: []    // conditions are set on the Loop Grid widget itself, not the item template
})
```

### Step 2 — Build the card

The property card structure:

**Outer container:** Full height, dark overlay, relative positioning, cursor pointer (link the whole card via the container's link setting → Dynamic Tag → Post URL)

**Media area:**
- Featured Image widget (Pro) — Dynamic Tag: Post Featured Image. Object-fit: cover. Aspect ratio: 4:3.
- Overlay div (inner container with semi-transparent dark background, absolute positioning via custom positioning controls)

**Badge:**
- Button widget (no click action, styled as a badge) — text from ACF Dynamic Tag `property_status` taxonomy term OR a Text widget with taxonomy term Dynamic Tag. Style: small, uppercase, gold border.

**Info area (bottom of card):**
- Text Editor: eyebrow (taxonomy term — property_type, Dynamic Tag: Post Terms)
- Heading widget: Post Title (Dynamic Tag)
- Text Editor: location (ACF Dynamic Tag `property_location_text`)
- Stats row: 2-column container with Text Editors showing price (ACF `property_price_from`) and yield (ACF `property_yield`)
- Button widget: "View Property →" — link via Dynamic Tag → Post URL

**Card hover effect:** Use Elementor's Motion Effects or Transform controls on the outer container for a subtle translateY(-6px) on hover. Add box-shadow via the Border tab.

### Step 3 — Use the Loop Item in Loop Grid widgets

Wherever a grid of property cards is needed:

```
Widget: Loop Grid
Template: Property Card (the loop item template just created)
Query:
  - Post Type: Properties
  - Filters: by taxonomy if filtering by location/status/type
  - Order: Date (newest first) or Menu Order
  - Posts Per Page: as needed (3 for related, 12 for archive)
Columns: 3 (desktop), 2 (tablet), 1 (mobile)
Gap: 24px
```

---

## Building Non-Portfolio Pages

For non-portfolio pages (Contact, Investors, International, Services, Sell With Us, etc.), build as regular Elementor pages (not Theme Builder templates). The design reference will be provided by the user as a URL.

### Build Order for Any Page

1. `list-pages` — check if the page already exists
2. If not: `create-page({ title, status: "draft", template: "elementor_canvas" })` — Canvas template removes theme chrome if building standalone; use default template if the global header/footer should display
3. `get-global-settings` — confirm design tokens are set
4. Ask the user for the design reference URL
5. Map every section in the design to native Elementor Pro widgets using the widget table above
6. Build top-down, one section at a time
7. After each section: `get-page-structure(post_id)` to verify nesting
8. When complete, set `status: "publish"` and share the page URL for review

### Common Non-Portfolio Sections and Their Widget Mappings

**Hero sections:**
- Full-screen Container, background image + overlay, Heading widget (display serif), Text Editor (lead copy), Button widgets (CTAs)

**Stats strips:**
- Single-row Container, repeated Heading + Text Editor pairs for each stat

**Two-column image/text splits:**
- 50/50 Container, Image widget on one side, Container with Heading + Text Editor + Button on the other

**Numbered feature grids:**
- Multi-column Container, each cell: Text Editor (number in display font), Heading (feature title), Text Editor (body)

**Testimonials:**
- Testimonial widget (Pro) or Media Carousel widget

**Team/people grids:**
- Loop Grid widget if using a People CPT, or manually built card Containers with Image + Heading + Text Editor widgets

**Contact page:**
- Two-column layout: left column has Heading + Text Editor (contact details, office addresses, map embed via Google Maps widget), right column has Elementor Pro Form widget

**FAQ / Accordion sections:**
- Accordion widget (Pro) — each item has a title and the expanded content uses the Text Editor within the accordion item

---

## Page Building — Detailed Widget Call Convention

### Heading Widget

```js
mcp__elementor__elementor-mcp-add-heading({
  post_id: <id>,
  parent_id: "<container_id>",
  title: "Your heading text — use <em> for italic emphasis",
  header_size: "h1",                    // h1, h2, h3, h4, h5, h6
  title_color: "#f8f4ed",               // --ivory
  align: "left",
  typography_typography: "custom",       // REQUIRED
  typography_font_family: "Cormorant Garamond",
  typography_font_size: { size: 80, unit: "px" },
  typography_font_weight: "300",
  typography_line_height: { size: 1.0, unit: "em" },
  typography_letter_spacing: { size: -0.02, unit: "em" },
})
```

### Text Editor Widget

```js
mcp__elementor__elementor-mcp-add-text-editor({
  post_id: <id>,
  parent_id: "<container_id>",
  editor: "<p>Your paragraph text here.</p>",
  text_color: "#606060",                // --stone-500
  typography_typography: "custom",
  typography_font_family: "Manrope",
  typography_font_size: { size: 16, unit: "px" },
  typography_font_weight: "400",
  typography_line_height: { size: 1.8, unit: "em" },
})
```

### Button Widget

```js
mcp__elementor__elementor-mcp-add-button({
  post_id: <id>,
  parent_id: "<container_id>",
  text: "Register Interest →",
  link: { url: "#enquiry", is_external: false },
  button_type: "custom",
  background_color: "#243421",          // --navy-500
  button_text_color: "#f8f4ed",         // --ivory
  border_radius: { unit: "px", size: 0 },
  typography_typography: "custom",
  typography_font_family: "Manrope",
  typography_font_size: { size: 12, unit: "px" },
  typography_font_weight: "600",
  typography_letter_spacing: { size: 2, unit: "px" },
  typography_text_transform: "uppercase",
  padding: { top: 16, right: 32, bottom: 16, left: 32, unit: "px", isLinked: false },
})
```

### Container Widget

```js
mcp__elementor__elementor-mcp-add-container({
  post_id: <id>,
  parent_id: "<parent_container_id>",
  settings: {
    content_width: "boxed",
    boxed_width: { unit: "px", size: 1280 },
    flex_direction: "row",
    flex_justify_content: "space-between",
    flex_align_items: "center",
    flex_gap: { unit: "px", size: 32 },
    padding: { top: 100, right: 0, bottom: 100, left: 0, unit: "px", isLinked: false },
    background_background: "classic",
    background_color: "#0d130c",
    min_height: { unit: "vh", size: 100 },    // for hero sections
  }
})
```

### Image Widget

```js
mcp__elementor__elementor-mcp-add-image({
  post_id: <id>,
  parent_id: "<container_id>",
  image: { url: "<image_url>", id: <attachment_id> },
  image_size: "full",
  width: { unit: "%", size: 100 },
  object_fit: "cover",
})
```

For dynamic featured image (in templates):
```js
// Set the image source to Dynamic Tag: Post Featured Image
// This is configured in the Elementor editor UI after the widget is placed,
// or via the dynamic_image setting if the MCP supports it
```

---

## Inspecting and Editing Existing Content

### Finding what's on a page

```
mcp__elementor__elementor-mcp-get-page-structure({ post_id: <id> })
```

This returns the full widget tree. Read it to find:
- Container and widget IDs (use for `parent_id` in add calls and `element_id` in update calls)
- Current widget settings
- Nesting structure

### Editing a specific widget

```
mcp__elementor__elementor-mcp-update-element({
  post_id: <id>,
  element_id: "<widget_id>",
  settings: {
    // only include the settings you want to change
    title_color: "#7e745c",
  }
})
```

Never rebuild a section to change one setting. Always use `update-element` for surgical edits.

### Finding a specific element

```
mcp__elementor__elementor-mcp-find-element({
  post_id: <id>,
  search: "Register Interest"    // searches by content text or widget type
})
```

---

## Tool-Loading Discipline

The MCP exposes many deferred tools. Load lazily:

- **Always safe first:** `list-pages`, `get-global-settings`
- **Before building containers:** load `get-container-schema`, `add-container`
- **Before placing widgets:** load `add-heading`, `add-text-editor`, `add-button`, `add-image` in one batch
- **Before Theme Builder work:** load `create-theme-template`, `get-theme-templates`
- **Before Loop Grid work:** load `add-loop-grid`, `get-loop-item-templates`
- **Specific as needed:** `add-tabs`, `add-accordion`, `add-form`, `add-gallery`, `add-nav-menu`, `add-social-icons`, `add-divider`, `add-spacer`, `add-icon-list`, `add-breadcrumbs`, `add-posts`, `add-site-logo`, `add-featured-image`, `add-post-title`

Use `ToolSearch` with `select:tool1,tool2,tool3` to load multiple in one call.

---

## What the MCP Cannot Do — Managed Exceptions

These tasks require WP-CLI or WP Admin and must be handled via those routes, not the MCP:

| Task | Method |
|---|---|
| Register a custom post type | WP-CLI `wp eval` + `functions.php` |
| Register taxonomies | WP-CLI `wp eval` + `functions.php` |
| Create taxonomy terms | WP-CLI `wp term create` |
| Import ACF field groups | WP-CLI `wp acf import` |
| Set post taxonomy terms | WP-CLI `wp post term set` |
| Set ACF field values | WP-CLI `wp post meta update` or `wp eval update_field()` |
| Set static front page | WP-CLI `wp option update` |
| Set Theme Builder display conditions | MCP `create-theme-template` with conditions array |
| Upload images to media library | WP-CLI `wp media import` or WP Admin |
| Create WordPress nav menus | WP-CLI `wp menu create` + `wp menu item add-post` |

---

## Quick Reference — Build Flows

### New Portfolio Property (full flow)

```
1. wp post create (Properties CPT)                    → get post ID
2. wp post term set (location, status, type)
3. wp post meta update (all ACF fields)
4. wp eval update_field (repeater fields)
5. wp media import (featured image) → set _thumbnail_id
6. Single Property template auto-applies (Theme Builder)
7. Verify at /properties/<slug>/
```

### New Non-Portfolio Page (full flow)

```
1. list-pages                                          → check if exists
2. create-page (draft, default template)
3. get-global-settings                                 → confirm design tokens
4. User provides design reference URL
5. Map design → native Pro widgets
6. Build section by section, verify with get-page-structure
7. Set status: publish
8. Share URL for user review
```

### New Theme Builder Template (full flow)

```
1. create-theme-template (type, title, conditions)     → get template post ID
2. get-container-schema                                → read layout keys
3. Build layout top-down with native Pro widgets
4. Use Dynamic Tags for all data-driven content
5. get-page-structure to verify
6. Confirm template is displaying via conditions
```

### Loop Grid Setup (full flow)

```
1. create-theme-template (type: loop-item)             → build Property Card template
2. Add Loop Grid widget to target page/template
3. Set Loop Grid query: post type = properties, filters, order
4. Set Loop Grid template: Property Card
5. Set columns, gap, pagination
6. Verify cards display correct dynamic data
```
