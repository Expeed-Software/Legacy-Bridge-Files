# UI Design System — Hotel Booking System

## Atomic Design Structure

---

## 1. ATOMS

### 1.1 Color Tokens

All colors reference the palette defined in the project. Tokens to be used for backgrounds, text, borders, states, etc. are listed below.

#### Primary Palette
- **Primary 50:** `#f2f6fa`
- **Primary 100:** `#dde7f5`
- **Primary 200:** `#b6c8ed`
- **Primary 300:** `#8ca6df`
- **Primary 400:** `#5e85c6`
- **Primary 500:** `#234581`
- **Primary 600:** `#1e3a70`
- **Primary 700:** `#19325e`
- **Primary 800:** `#16274a`
- **Primary 900:** `#111d36`

#### Secondary Palette
- **Secondary 50:** `#f4f6fe`
- **Secondary 100:** `#e3e8fb`
- **Secondary 200:** `#c5d0f7`
- **Secondary 300:** `#a2b0ea`
- **Secondary 400:** `#7585dc`
- **Secondary 500:** `#4052b5`
- **Secondary 600:** `#344297`
- **Secondary 700:** `#2c377f`
- **Secondary 800:** `#21295e`
- **Secondary 900:** `#181c40`

#### Accent Palette
- **Accent 50:** `#f8faf2`
- **Accent 100:** `#eef7d9`
- **Accent 200:** `#ddeea8`
- **Accent 300:** `#c3e06c`
- **Accent 400:** `#a9cd46`
- **Accent 500:** `#7db435`
- **Accent 600:** `#5e902a`
- **Accent 700:** `#487023`
- **Accent 800:** `#325019`
- **Accent 900:** `#243810`

#### Status Colors

| Status    | 50        | 100       | 200       | 500        | 700        |
|-----------|-----------|-----------|-----------|------------|------------|
| Success   | #f3faf6   | #d7f3e4   | #a7e8c8   | #1eae69    | #168552    |
| Warning   | #fffaf5   | #fdefd9   | #ffddb1   | #f79e3c    | #b56d20    |
| Error     | #fef7f6   | #fde0dd   | #fab7b0   | #e44833    | #ab2d1b    |
| Info      | #f4f9fb   | #d8edf7   | #9dd6ec   | #199adf    | #136fa9    |

#### Neutrals & Surfaces

| Token                   | Hex        | Purpose            |
|-------------------------|------------|--------------------|
| Neutral 0               | #ffffff    | Top cards, modals, etc.|
| Neutral 50              | #f7f9fa    | Page background     |
| Neutral 100             | #f0f2f4    | Inputs bg          |
| Neutral 200             | #e4e7ea    | Lines, diff bg     |
| Neutral 300             | #cfd4da    | Dividers           |
| Neutral 400             | #b3bac3    | Disabled/Border    |
| Neutral 500             | #9098a3    | Subtle text        |
| Neutral 600             | #6e7681    | Secondary text     |
| Neutral 700             | #4b535e    | Main text/Dark bg text|
| Neutral 800             | #323740    | Side nav bg        |
| Neutral 900             | #23262b    | Heading/Main nav bg|
| Neutral 950             | #141619    | Deepest, overlays  |

| Surface Token           | Hex        | Use                |
|-------------------------|------------|--------------------|
| Surface Page BG         | #f7f9fa    | Layout background  |
| Surface Card BG         | #ffffff    | Cards              |
| Surface Sidebar BG      | #23262b    | Sidebar            |
| Surface Modal BG        | #ffffff    | Modal/dialog       |
| Surface Input BG        | #f0f2f4    | Input fields       |
| Surface Hover BG        | #dde7f5    | Hover states       |

| Text Token              | Hex        | Purpose            |
|-------------------------|------------|--------------------|
| Text Primary            | #23262b    | Main text          |
| Text Secondary          | #6e7681    | Secondary text     |
| Text Disabled           | #b3bac3    | Disabled           |
| Text Inverse            | #ffffff    | On dark surfaces   |
| Text Link               | #234581    | Links              |
| Text Link Hover         | #4052b5    | Hover, focus       |

---

### 1.2 Typography Scale

**Font Family:**  
- Stack: Inter, Segoe UI, Arial, sans-serif

**Headings:**  
- Use a modular scale (1.25)—each step is 1.25× larger than the next, rounded:
- Font-weight: 700 for headings; 400 for base text

| Element   | Size (rem/px) | Weight | Line-height |
|-----------|---------------|--------|-------------|
| H1        | 2.25rem/36px  | 700    | 1.22        |
| H2        | 1.8rem/29px   | 700    | 1.22        |
| H3        | 1.5rem/24px   | 700    | 1.22        |
| H4        | 1.25rem/20px  | 700    | 1.25        |
| H5        | 1.125rem/18px | 700    | 1.3         |
| H6        | 1rem/16px     | 700    | 1.3         |
| Body L    | 1.125rem/18px | 400    | 1.7         |
| Body      | 1rem/16px     | 400    | 1.6         |
| Body S    | 0.875rem/14px | 400    | 1.5         |
| Caption   | 0.75rem/12px  | 400    | 1.4         |
| Button    | 1rem/16px     | 600    | 1.3         |
| Overline  | 0.75rem/12px  | 700    | 1.6         |

**Letter Spacing:**  
- Headings: -0.5px
- Caption/overline: 0.5px

---

### 1.3 Spacing Scale

- Spacing tokens to be used for margin, padding, and gap.
- Unit: 4px base

| Token    | Value (px) | Value (rem) |
|----------|------------|-------------|
| spacing-0| 0          | 0           |
| spacing-1| 4          | 0.25        |
| spacing-2| 8          | 0.5         |
| spacing-3| 12         | 0.75        |
| spacing-4| 16         | 1           |
| spacing-5| 24         | 1.5         |
| spacing-6| 32         | 2           |
| spacing-7| 40         | 2.5         |
| spacing-8| 48         | 3           |
| spacing-10| 64        | 4           |

---

### 1.4 Border Radius Scale

| Token            | px  | Use case                                  |
|------------------|-----|-------------------------------------------|
| radius-none      | 0   | No radius                                 |
| radius-xs        | 2   | Input fields, minimal rounding            |
| radius-sm        | 4   | Buttons, tags, small cards                |
| radius-md        | 8   | Cards, modal/dialogs                      |
| radius-lg        | 16  | Large banners, page sections if needed    |
| radius-full      | 999 | Avatars, round icons                      |

**Default:**
- Inputs, buttons: radius-sm (4px)
- Cards, modal: radius-md (8px)
- Avatars: radius-full

---

### 1.5 Shadow Scale

All shadows use RGBA black for subtlety.

| Token        | Value                                                                 | Use                |
|--------------|-----------------------------------------------------------------------|--------------------|
| shadow-0     | none                                                                  | No shadow          |
| shadow-xs    | 0 1px 2px 0 rgba(50,55,64,0.04)                                       | Input, control fuzz|
| shadow-sm    | 0 2px 4px 0 rgba(50,55,64,0.08)                                       | Buttons, tags      |
| shadow-md    | 0 4px 16px 0 rgba(50,55,64,0.12)                                      | Cards, popovers    |
| shadow-lg    | 0 8px 32px 0 rgba(50,55,64,0.16)                                      | Modals, drawers    |
| shadow-focus | 0 0 0 3px #b6c8ed (Primary 200, semi-transparent for focus ring)      | Focus ring         |

---

### 1.6 Icon Sizes

- icon-xs: 12x12px
- icon-sm: 16x16px
- icon-md (default): 20x20px
- icon-lg: 24x24px
- icon-xl: 32x32px

---

### 1.7 Z-Index Scale

- z-0: background/base
- z-1: content
- z-10: popovers/tooltip
- z-20: dropdowns
- z-30: overlay background
- z-40: modal/dialog
- z-50: toast/notifications

---

### 1.8 Breakpoints

| Alias  | px   | rem     | Usage                       |
|--------|------|---------|-----------------------------|
| xs     | 0    | 0       | (mobile, not default focus) |
| sm     | 600  | 37.5    | Small tablets/compact       |
| md     | 900  | 56.25   | Main desktop app min width  |
| lg     | 1200 | 75      | Large desktop/break columns |
| xl     | 1800 | 112.5   | Wide screens/extra columns  |

Default design targets desktop viewport (md: 900px+)  
App must be usable at sm (600px), but optimized for desktop use.

---

## 2. MOLECULES

---

### 2.1 Buttons

#### General Button Specifications

- **Font:** Inter, Button style (1rem/16px, font-weight 600)
- **Border Radius:** radius-sm (4px)
- **Padding:**
  - sm: 6px 16px (top/bottom: 6px, left/right: 16px)
  - md (default): 10px 20px
  - lg: 14px 28px
- **Gap:** 8px between icon/text
- **Icon size:** icon-md (20px)
- **Line-height:** 1.3
- **Min width:** 40px
- **No outline except on :focus-visible (see focus state below)**
- **Cursor:**
  - `pointer` when enabled
  - `not-allowed` when disabled

#### Button Variants

---

#### Primary Button
- **Background:** Primary 500 `#234581`
- **Text:** Text Inverse `#ffffff`
- **Border:** none
- **Hover:** Background Primary 600 `#1e3a70`
- **Active:** Background Primary 700 `#19325e`
- **Focus:** Shadow-focus
- **Disabled:** Background Primary 100 `#dde7f5`, Text Disabled `#b3bac3`, cursor

---

#### Secondary Button
- **Background:** Secondary 100 `#e3e8fb`
- **Text:** Secondary 700 `#2c377f`
- **Border:** 1px solid Secondary 400 `#7585dc`
- **Hover:** Background Secondary 200 `#c5d0f7`
- **Active:** Background Secondary 300 `#a2b0ea`
- **Focus:** Shadow-focus
- **Disabled:** Background Neutral 100 `#f0f2f4`, Text Disabled, Border Neutral 200

---

#### Ghost Button
- **Background:** transparent
- **Text:** Primary 500 `#234581`
- **Border:** none
- **Hover:** Background Surface Hover BG `#dde7f5`
- **Active:** Background Primary 100 `#dde7f5`
- **Focus:** Shadow-focus
- **Disabled:** Text Disabled, Background none, Border none

---

#### Danger Button
- **Background:** Error 500 `#e44833`
- **Text:** Text Inverse `#ffffff`
- **Border:** none
- **Hover:** Background Error 700 `#ab2d1b`
- **Active:** Background Error 700 `#ab2d1b`
- **Focus:** Shadow-focus (or custom shadow in Error 200)
- **Disabled:** Background Error 100 `#fde0dd`, Text Disabled

---

#### Button Sizes

| Size | Height | Padding              | Font Size | Icon Size |
|------|--------|----------------------|-----------|-----------|
| sm   | 32px   | 6px 16px             | 0.875rem  | 16px      |
| md   | 40px   | 10px 20px            | 1rem      | 20px      |
| lg   | 48px   | 14px 28px            | 1.125rem  | 24px      |

#### Button States
- **:hover**: Use `background` color as specified per variant
- **:focus-visible**: Add shadow-focus token
- **:active**: Darken background by one scale step, no extra border/shadow
- **:disabled**: opacity: 1, use color tokens as specified, always `cursor: not-allowed`

---

### 2.2 Inputs / Textarea / Select

#### Base Style for All Inputs

- **Font:** Body (1rem/16px, 400)
- **Background:** Surface Input BG `#f0f2f4`
- **Text:** Text Primary `#23262b`
- **Placeholder:** Text Disabled `#b3bac3`
- **Border:** 1px solid Neutral 200 `#e4e7ea`
- **Border-radius:** radius-sm (4px)
- **Padding:** 10px 12px
- **Height:** 40px
- **Line-height:** 1.5
- **Box-shadow:** shadow-xs
- **Transitions:** border, box-shadow 0.15s

**:hover**
- Border-color: Primary 200 `#b6c8ed`
- Background: Surface Hover BG `#dde7f5`

**:focus**
- Border-color: Primary 500 `#234581`
- Box-shadow: shadow-focus

**:disabled**
- Background: Neutral 100 `#f0f2f4`
- Text: Text Disabled `#b3bac3`
- Border: Neutral 200 `#e4e7ea`
- Cursor: not-allowed

---

#### Textarea

- **Same as input**, but min-height: 96px, vertical padding 10px

---

#### Select

- Same as input; dropdown arrow icon positioned right, icon color: Text Secondary
- Options/dropdown popover:
  - Background: Card BG `#fff`
  - Border: 1px solid Primary 200 `#b6c8ed`
  - Option height: 40px
  - Hover: Background Surface Hover BG `#dde7f5`
  - Selected: Background Secondary 100 `#e3e8fb`, bold text

---

### 2.3 Badge / Tag

#### **Badge (Status Indicator, e.g., status cell in table)**

| Status    | Background      | Text        |
|-----------|----------------|-------------|
| Default   | Neutral 100    | Neutral 700 |
| Success   | Success 100    | Success 700 |
| Warning   | Warning 100    | Warning 700 |
| Error     | Error 100      | Error 700   |
| Info      | Info 100       | Info 700    |
| Primary   | Primary 100    | Primary 700 |
| Secondary | Secondary 100  | Secondary 700 |

- **Font:** 0.75rem/12px, 600, uppercase
- **Padding:** 0 8px
- **Height:** 22px
- **Border:** none
- **Border-radius:** radius-xs (2px)
- **Icon (optional):** icon-xs (12px), color follows text

---

#### **Tag (Dismissible, e.g., multi-select display)**

- **Background:** Neutral 200 `#e4e7ea`
- **Text:** Neutral 700 `#4b535e`
- **Font:** 0.875rem/14px, 500
- **Padding:** 0 8px
- **Height:** 24px
- **Border:** none
- **Border-radius:** radius-sm (4px)
- **Icon (remove):** icon-xs (12px), color: Neutral 500

---

### 2.4 Card Base Style

- **Background:** Surface Card BG `#fff`
- **Border:** 1px solid Neutral 200 `#e4e7ea`
- **Shadow:** shadow-md
- **Border-radius:** radius-md (8px)
- **Padding:** 24px (spacing-5), can be adjusted for specific use
- **Gap:** spacing-4 (16px) between card sections
- **Header font:** 1.125rem/18px, 700
- **Footer (if present):** border-top: 1px solid Neutral 100

---

### 2.5 Avatar

- **Shape:** Circular (border-radius: radius-full)
- **Default size:** 32x32px
- **Variants:** sm: 24px, md: 32px, lg: 40px, xl: 48px
- **Text:** Initials if image is missing, font 700, size 14px–20px scaled with container
- **Background:** Primary 200 `#b6c8ed` if letter / fallback
- **Text:** Primary 800 `#16274a`

---

### 2.6 Tooltip

- **Font:** 0.875rem/14px, 400
- **Background:** Neutral 900 `#23262b`
- **Text:** Text Inverse `#fff`
- **Padding:** 8px 12px
- **Border-radius:** radius-xs (2px)
- **Box-shadow:** shadow-sm
- **Arrow:** match background color
- **Z-index:** z-10

---

### 2.7 Spinner / Loader

- **Type:** Circular spinner, SVG or CSS
- **Size:** 20x20px (md), 32x32px (lg)
- **Stroke color:** Primary 500 `#234581`
- **Track color:** Neutral 200 `#e4e7ea`
- **Animation duration:** 1s for full rotation, infinite
- **Accessible label:** Always include aria-label

---

## 3. ORGANISMS

---

### 3.1 Top Navigation Bar

- **Height:** 64px
- **Background:** Surface Card BG `#fff`
- **Border-bottom:** 1px solid Neutral 200 `#e4e7ea`
- **Shadow:** shadow-xs
- **Z-index:** z-20
- **Logo:** Left, 40px height, margin spacing-4 (16px)
- **App Name:** H4 (1.25rem/20px, 700), color: Primary 500 `#234581`
- **Nav items:** Spacing-5 (24px) horizontal gap, font-weight 600, font-size 1rem
- **Nav text:** Text Primary `#23262b`
- **User menu/avatar:** Right side, avatar in circle (32px), username (optional) Text Secondary
- **Icon buttons:** Ghost button style
- **Active nav**: underline 2px Primary 500, color: Primary 500

#### Hover/Focus/Active

- **Nav item hover/focus:** Background Surface Hover BG `#dde7f5`, no shadow
- **Avatar hover/focus:** Shadow-sm

---

### 3.2 Sidebar Navigation

- **Width:** 248px
- **Background:** Surface Sidebar BG `#23262b`
- **Border-right:** none
- **Text:** Text Inverse `#fff`
- **Icon color:** Text Inverse `#fff`
- **Nav items:** Height 48px; font-size 1rem/16px; font-weight 600
- **Active item:** Background Primary 500 `#234581`, text: `#fff`, no border
- **Hover:** Background Primary 600 `#1e3a70`, text: `#fff`
- **Spacing:** Items separated by spacing-1 (4px)
- **Collapsed sidebar:** 64px wide, icons only, tooltip on hover, background unchanged

#### Divider
- **Divider color:** Neutral 900 `#23262b` (self background), spaced with spacing-3 (12px)

---

### 3.3 Data Table

- **Header:** font-weight 700, font-size 0.875rem/14px, Text Secondary `#6e7681`, uppercase
- **Row:** font-size 1rem/16px, font-weight 400, Text Primary
- **Background:** Card BG `#fff`
- **Border:** 1px solid Neutral 200 `#e4e7ea`
- **Row border-bottom:** 1px solid Neutral 100 `#f0f2f4`
- **Row hover:** Surface Hover BG `#dde7f5`
- **Row selected:** Secondary 100 `#e3e8fb`
- **Row active:** Secondary 200 `#c5d0f7`
- **Cell padding:** 12px 16px
- **Row height:** 48px
- **Checkboxes:** Input style (see 2.2)
- **Badges in status cells:** Use Badge specs above
- **Pagination controls:** Secondary button style, horizontal spacing-2

---

### 3.4 Modal / Dialog

- **Background:** Surface Modal BG `#fff`
- **Border-radius:** radius-md (8px)
- **Padding:** 32px (content), header/bottom spaced by spacing-4
- **Shadow:** shadow-lg
- **Z-index:** z-40 (overlay at z-30)
- **Title:** font-size 1.125rem/18px, 700
- **Header:** border-bottom: 1px solid Neutral 100 `#f0f2f4`
- **Footer actions:** buttons right-aligned, gap spacing-3
- **Close button:** ghost button, icon color Neutral 500
- **Overlay:** rgba(20,22,25, 0.64); fills viewport
- **Focus:** Initial focus traps to modal, shadow-focus on focusable elements
- **Transition:** Fade in/out, 150ms
- **Max-width:** 480px (form/modal), 900px (large/modal)

---

### 3.5 Form Layout

- **Field vertical spacing:** spacing-4 (16px)
- **Form label:** 0.875rem/14px, 600, Text Secondary
- **Input width:** 100% (max 440px for single column), 2-column at lg/xl
- **Form section header:** font-size 1rem/16px, 700, margin-bottom spacing-2 (8px)
- **Validation errored fields:**
    - Border: 1px solid Error 500 `#e44833`
    - Background: Error 50 `#fef7f6`
    - Error text: 0.875rem/14px, 600, color Error 500
    - Helper text: Text Secondary `#6e7681`
- **Grouped inline controls:** use horizontal spacing-3 (12px)
- **Button row:** align end, spacing-3 between buttons

---

### 3.6 Page Header

- **Font:** H2 (1.8rem/29px, 700)
- **Text:** Text Primary `#23262b`
- **Subheading (optional):** 1rem/16px, Text Secondary
- **Background:** transparent or inherited from parent (no special box)
- **Breadcrumbs:** Row above title, font-size 0.875rem/14px, Text Secondary, last item Primary 500

---

### 3.7 Empty State

- **Card Base:** Surface Card BG, Card style as defined above
- **Icon:** icon-lg (24–32px), color Accent 300 `#c3e06c` (info) or contextual
- **Title:** 1.125rem/18px, 700, Text Primary
- **Description:** 1rem/16px, 400, Text Secondary, max 400px width
- **Action (if present):** Primary or Secondary button below

---

### 3.8 Notification / Toast

- **Background:** Based on type:
    - Info: Info 100 `#d8edf7`
    - Success: Success 100 `#d7f3e4`
    - Warning: Warning 100 `#fdefd9`
    - Error: Error 100 `#fde0dd`
- **Text:** Corresponding 700 color
- **Border-left:** 4px solid (type color: Info 500, Success 500, etc.)
- **Border-radius:** radius-md (8px)
- **Shadow:** shadow-md
- **Padding:** 12px 20px
- **Icon:** Left, icon-md, color type 500
- **Title:** 1rem/16px, 700
- **Description:** 0.875rem/14px, 400
- **Action button (optional):** Ghost button style, color by type
- **Close icon:** icon-sm, Neutral 500, ghost button interaction
- **Z-index:** z-50 (above modal)

- **Placement:**
    - Toasters: Fixed, top-right corner, max 360px width
    - Inline notification bar: full width, margin spacing-5 from top

- **Animation:** Slide/fade in 150ms
- **Focus:** Announce with `aria-live="polite"` for accessibility

---

# End of Design System

---

All visual and spacing decisions must reference this atomic structure and use only the established tokens, scales, and components as described. Never create visual variants outside these definitions. All new UI must layer on the above as foundation.