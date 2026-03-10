---
name: goalscape
description: |
  How to operate the Goalscape web app (goalscape.app) efficiently using browser automation. Use this skill whenever the user mentions Goalscape, goal diagrams, radial goal charts, or wants to create/edit/manage goals and projects in Goalscape. Also trigger when the user references a goalscape.app URL, wants to add subgoals, set priorities/importance, track progress, add notes or knowledge blocks to goals, or navigate a Goalscape project. Even if the user just says "add a goal" or "update progress" while a Goalscape tab is open, use this skill.
---

# Goalscape Browser Automation Skill

Goalscape is a visual goal management app at `goalscape.app`. It uses a unique **radial diagram** where goals are displayed as pie-chart segments — the larger the segment, the more important the goal relative to its siblings. This skill teaches you how to operate Goalscape efficiently through browser automation.

## Core Concepts

### The Radial Diagram
Goalscape’s main visualization is a circular chart:
- The **center circle** represents the project’s main goal (root goal)
- **Subgoals** appear as segments in concentric rings around the center
- Each segment’s **angular size** reflects its **relative importance** (bigger = more important)
- Goals are arranged **clockwise** to represent sequence/priority order
- The **clockwise order can only be changed via drag and drop** — there is no sort menu or reorder button. To move a goal, click and drag its segment to a new position along the ring.
- **Clicking a segment** selects it and shows details in the right panel
- **Blue highlight** indicates the currently selected goal
- When a goal is selected, a small **blue circle (handle)** appears at the outer edge of the segment boundary. This handle is for **adjusting importance by dragging** — do NOT confuse it with a reorder handle. Dragging this circle changes the goal’s importance relative to its siblings.
- **Double-clicking a goal** that has subgoals will **center** it — making it the new center of the diagram so its subgoals become the main ring. If the goal has no subgoals (leaf goal), double-click does nothing special.
- **Double-clicking the center goal** navigates **one level up** to the parent goal — but only if you have previously drilled into a subgoal. At the project root level, double-clicking the center does nothing because there is no parent to navigate to.
- When drilled into a subgoal, a small **overview diagram** appears in the top-left corner showing the full project structure with the current focus area highlighted.

### Goal Hierarchy (Tree Structure)
Goals form a tree with **unlimited depth** — break goals down as much as needed ("so wenig wie möglich, so viele wie nötig"):
- **Project** = the root goal (shown in the center)
- Each goal can have **subgoals** (children), which appear as segments in the next ring
- Subgoals can themselves have sub-subgoals, creating deeper rings — there is **no limit** on how many levels deep you can go. The idea is to keep breaking goals down until each leaf goal is small and achievable.
- The **importance** of subgoals is always relative to their siblings (they share 100% of their parent’s space)
- **Progress** rolls up: a parent’s progress is the weighted average of its children’s progress (weighted by importance)

### Importance vs. Progress
- **Importance** (0–100%): How much of the parent’s "weight" this goal carries. Sibling goals share 100%. If one goal goes up, others must go down proportionally. A goal that is the only child of its parent will always have 100% importance — you can only set a specific importance like 25% if at least one sibling goal exists.
- **Progress** (0–100%): How complete this goal is. For leaf goals, set manually. For parent goals, calculated automatically from children.

## URL Structure

Goalscape URLs follow this pattern:
```
https://goalscape.app/#/projects/{projectId}?sid={selectedGoalId}&fid={focusGoalId}&tid={tabId}
```
- `projectId`: UUID of the project
- `sid`: UUID of the currently selected goal
- `fid`: UUID of the focused (center) goal
- `tid`: which tab is active in the right panel (empty = Notes & Actions, `comments`, etc.)

## App Layout

### Home Screen (Project List)
The Home screen is the first thing you see after logging in, or when clicking the Goalscape logo / Home icon. It lists all your projects.

**Top area:**
- **"Create New Goalscape Project"** text field (default placeholder: "My new goal") — type a project name here
- **"Create"** button — creates a new blank project with the typed name
- **"Create with Goalscape AI"** button — creates a project using AI assistance
- **Templates** button — opens the template gallery
- **Import** button — imports an existing project
- **Search** (magnifying glass icon, far right)

**Project tabs:**
- **"All my Goalscape projects"** tab — shows all projects
- **"+"** button next to the tab — adds a new custom tab for organizing projects into groups

**Project list columns:** Name, Created, Modified (sortable), Role

**Hover actions on each project row:**
When you hover over a project name, **5 icon buttons** appear to the right of the name (left to right):
1. **Open in new tab** (external-link icon) — opens the project in a new browser tab
2. **Duplicate** (copy icon) — creates a copy of the project
3. **Add to custom tab** (arrow-into-box icon) — adds the project to a custom tab group
4. **Archive** (download-box icon) — archives the project (moves it out of the active list)
5. **Delete** (red trash icon) — permanently deletes the project

> **Note:** These buttons are only visible on hover. They have no text labels, only icons with tooltips. The app uses custom `<tooltip>` elements (not standard HTML `title` attributes), so tooltips appear after a short hover delay.

> **⚠ Automation tip:** To reveal these buttons, first hover over the project row and wait ~0.5s for the icons to appear, then hover over or click the desired icon. Use JavaScript to programmatically trigger `mouseenter` events if the icons are hard to target.

### Top Bar
- **Goalscape logo**: navigates to Home
- **Home icon** (house): opens Home dialog with project list
- **Undo/Redo** buttons
- **Three dots menu** (⋮) in the top-left area: project-level actions:
  - **Project History**: view the change history of the project
  - **Download As** → submenu with export formats: Image (PNG), Vector Image (SVG), Excel Sheet (XLSX), Data Values (CSV), Word Document (DOCX), Goalscape Project (GSP)
- **Focus button** (top-left): shows current focus path, click ">" to drill into selected goal
- **Navigation arrows** (top-right of diagram area): navigate between sibling goals
- **Three dots menu** (⋮) near navigation arrows: opens a context menu for the currently selected goal — same actions as the right-click context menu (Add Goal, Add Subgoal, Rename, Cut, Copy, Paste, Delete, progress shortcuts, Equalize Subgoals, color palette)
- **Search** (magnifying glass icon): opens an inline search bar. Results appear in a dropdown with two sections: **Goal names** (matches in goal titles) and **Notes** (matches in note content with excerpts). Clicking a result selects that goal.
- **Theme toggle** (sun/moon icon): switch between dark and light themes
- **Share** button: opens the Share dialog — add people by email, view people with access and their roles, toggle "Anyone with the link can view", Copy Public link, Copy Embed code
- **User avatar** (initials): profile dropdown with First/Last name, Email, Settings, Support, Logout

### Right Panel (Goal Details)
When a goal is selected, the right panel shows:

**Header:**
- Goal name (editable)
- Parent breadcrumb (click to navigate up)
- Lock icon (to lock editing)

**Properties:**
- **Importance**: slider showing percentage. Drag to adjust (affects siblings proportionally).
- **Progress**: slider showing percentage. For leaf goals, drag to set manually. For parent goals, this is auto-calculated.
- **Responsible**: assign a person
- **Due**: set a due date
- **Tags**: add tags
- **Color**: set goal color

**Content Tabs:**
1. **Notes & Actions** (text icon): Rich text field for notes, knowledge blocks, checklists. This is where you write detailed content for a goal.
2. **Comments** (chat icon): Comment thread with formatting (bold, italic, lists, links, images, video). Can notify by email.
3. **Attachments** (paperclip icon): Attach files to the goal.
4. **+** (plus icon): Create a **Named Note** — opens a "Name Note" dialog to create an additional named note tab for the goal. Multiple named notes can be added to organize different content.

### Bottom Panel
The bottom has two toggleable panels: **Goal List** and **Gantt**. Click their names to expand/collapse. A drag handle between the panels and the diagram allows resizing.

#### Goal List
Hierarchical tree of all goals with expand/collapse arrows. Click a goal name to select it.

**Filter panel** (above the goal list):
- **Read-only mode** (default): Shows active filter chips for Start, Due, Persons, Tags
- **Edit mode** (click "Edit ✏️"): Reveals all filter categories with "+ Add filter" dropdowns. Click "Close Edit ✖" to exit.
- **Pin mode** (click "Pin 📌"): Keeps the filter panel pinned open

**Filter categories:**
- **Start**: Has Started, Has Not Started, Yesterday, Today, Tomorrow, This Week, Last Week, Next Week, This Month, Last Month, Next Month, This Quarter, Last Quarter, Next Quarter, Start Before (custom date), Start After (custom date)
- **Due**: same time-based options as Start, plus Past Due
- **Progress**: Pending, In Progress, Achieved, 10% or less through 90% or less (in 10% increments)
- **Persons**: filter by assigned person
- **Tags**: filter by tag

Each active filter shows as a chip with ✖ to remove.

#### Gantt
Timeline view with month/year headers.

**Creating Gantt bars**: **Click and drag** horizontally on the timeline next to a goal row to create a date range (start + due date). This is the fastest way to assign dates.

**Modifying bars**: Drag a bar to move its date range. Drag an edge to resize (change start or due date).

**Gantt toolbar**: Goal List button, Show/Hide parent goals toggle, detailed list icon, print icon, copy icon, "Gantt" dropdown (collapses panel)

**Gantt controls** (right side): "Now" button (scroll to current date), Zoom in/out (+/−), three-dots menu with: Zoom slider, Show Now Line toggle, Zoom to fit, Scroll to Now

## How to Perform Common Actions

### Navigate to a Goalscape Project
If the user gives you a goalscape.app URL, navigate directly to it. If a Goalscape tab is already open, use it.

### Select a Goal
- **Click** on a segment in the radial diagram, OR
- **Click** on a goal name in the Goal List (bottom-left panel)
- After selecting, the right panel shows that goal’s details

### Create a New Subgoal
1. **Right-click** on the parent goal segment in the diagram → "Add Subgoal"
2. Or click the **three-dot menu** (⋮) in the top-right while the parent goal is selected → "Add Subgoal"
3. Keyboard shortcut: **⌘ →** (Mac)
4. Type the new goal name and press Enter

### Create a New Sibling Goal
1. **Right-click** on any goal segment → "Add Goal"
2. Or three-dot menu → "Add Goal"
3. Keyboard shortcut: **⌘ ⇧ →** (Mac)
4. This adds a goal at the same level as the selected one

### Rename a Goal
1. **Use form_input on the right panel header textbox:** After selecting the goal, use `read_page(filter="interactive")` to find the goal name `textbox` (placeholder "New Goal") in the right panel, then use `form_input(ref=..., value="Goal Name")` followed by pressing Enter.
2. **Right-click → "Rename Goal"** (or press **T** after selecting) — this opens an inline edit popup on the diagram. Then use `form_input` on the popup’s textbox.
3. **Click on the goal name in the right panel header** to activate the edit field, then use `form_input`.

> **⚠ CRITICAL: Never use `type` to enter a goal name unless you have confirmed a text input field has focus.** Typing without a focused input field will inject text into the SVG diagram, corrupting goal labels.

### Set Importance
1. Select the goal
2. In the right panel, drag the **Importance** slider
3. Or click on the percentage value and type a number
4. Or drag the **blue circle handle** on the outer edge of the segment in the radial diagram — this directly adjusts importance visually
5. Remember: changing one goal’s importance automatically adjusts its siblings

> **⚠ Caution:** The blue circle handle on the segment edge adjusts **importance**, NOT position. Do not drag it when trying to reorder goals — you will accidentally change importance values.

### Set Importance on Multiple Sibling Goals (Lock/Unlock Workflow)

When you need to set **specific importance percentages** on multiple sibling goals, you must use the lock feature. Without locking, changing one goal’s importance redistributes the remaining percentage proportionally among all unlocked siblings — so previously set values will drift.

**Workflow:**

1. **Select** the first goal
2. **Set** its importance (click the percentage value, type the number, press Enter)
3. **Lock** it by clicking the **padlock icon** to the right of the Importance slider — the icon changes from an open padlock to a closed padlock with a blue circle, and the slider turns grey
4. **Repeat** steps 1–3 for each subsequent goal, working through siblings one at a time
5. The **last sibling** will automatically receive whatever percentage remains (since locked goals are protected from redistribution)
6. **Unlock all goals** by clicking each locked padlock icon to return it to the open state

> **⚠ Important:** Always unlock goals after you’ve finished setting all importance values. Leaving goals locked can cause unexpected behaviour when adding, removing, or reorganising goals later — locked goals refuse to adjust, which can make the remaining unlocked goals absorb all changes disproportionately.

> **Why this matters:** Sibling importance values always sum to 100% of their parent. When you change one unlocked sibling, Goalscape redistributes the difference across all other *unlocked* siblings proportionally. Locking freezes a goal’s importance so it is excluded from redistribution.

**Example — setting four siblings to 35%, 30%, 25%, 10%:**
1. Select Goal A → set to 35% → lock
2. Select Goal B → set to 30% → lock
3. Select Goal C → set to 25% → lock
4. Select Goal D → it should now be at 10% (the remainder); set to 10% if needed
5. Unlock Goal A, Goal B, Goal C (Goal D was never locked)

### Set Progress
1. Select the goal (must be a leaf goal for manual editing)
2. Drag the **Progress** slider in the right panel
3. Quick options via right-click: "Zero Progress" or "Complete Progress"

### Add Notes / Knowledge Blocks
1. Select the goal
2. Click the **Notes & Actions** tab in the right panel (first tab, text icon)
3. Click in the text area and type/paste content
4. Supports rich text formatting

### Add Comments
1. Select the goal
2. Click the **Comments** tab (chat bubble icon)
3. Type in the "Add comment..." field at the bottom
4. Supports formatting: Bold, Italic, Lists, Links, Images, Video
5. Toggle "Notify by email" if needed

### Set Due Date
1. Select the goal
2. Click "Add date" next to **Due** in the right panel
3. Pick a date from the date picker

### Assign Responsible Person
1. Select the goal
2. Click "Add person" next to **Responsible**
3. Select or type a person’s name

### Set Color
1. Right-click on a goal → pick a color from the palette at the bottom of the context menu
2. Or use the **Color** field in the right panel

### Focus / Drill Into a Goal
There are two ways to drill into a goal (make it the center):
1. **Double-click** on a goal segment that has subgoals — this is the fastest method
2. Click the **Focus >** button at the top-left to drill into the currently selected goal

To navigate **back up** (only possible when you have previously drilled into a subgoal):
1. **Double-click the center goal** — this goes one level up to the parent
2. Click the **breadcrumb trail** in the right panel or the Focus path at the top-left
3. Click the small **overview diagram** (top-left corner, visible when drilled in)

### Navigate Between Goals
- Use the **arrow buttons** at the top-right of the diagram to move to sibling goals
- Use the Goal List to jump to any goal
- Click parent breadcrumb in the right panel to go up

### Right-Click Context Menu (Full Reference)
Right-clicking a goal segment shows:
1. **Add Goal** (⌘ ⇧ ↵) — add sibling
2. **Add Subgoal** (⌘ ↵) — add child
3. **Rename Goal** (T ↵)
4. **Cut Goal** (⌘ X)
5. **Copy Goal** (⌘ C)
6. **Paste Goal** (⌘ ⇧ V) — paste as sibling
7. **Paste as Subgoal** (⌘ V) — paste as child
8. **Delete Goal** (⌘ ←)
9. **Zero Progress** — set to 0% (greyed if already zero)
10. **Complete Progress** — set to 100%
11. **Equalize Subgoals** — set all children to equal importance
12. **Center on this Goal** (⌘ ⇧ ↓) — drill in, making it the diagram center
13. **Color palette** — two rows of color swatches (red, yellow, green, blue, purple, orange, grey, teal, pink, light blue)
14. **Apply to Subgoals** — applies selected color recursively to all subgoals

### Delete a Goal
1. Right-click → "Delete Goal"
2. Keyboard: **⌘ ←** (backspace)
3. Be careful: this deletes the goal and all its subgoals

### Cut / Copy / Paste Goals
- Right-click provides Cut, Copy, Paste Goal, and "Paste as Subgoal"
- Useful for reorganizing the goal tree

## Tips for Efficient Automation

1. **Use the Goal List** for precise selection — clicking segments in the radial diagram can be tricky since segments vary in size. The Goal List at the bottom-left is more reliable for selecting goals by name.

2. **Use right-click context menus** for most actions — they provide the most direct path to adding goals, subgoals, and setting colors.

3. **Use `read_page` or `find`** to locate specific UI elements when the layout changes.

4. **Wait after actions** — Goalscape sometimes has short animations. Add a brief wait (0.5–1s) after clicking to ensure the UI has updated.

5. **Check the URL** to understand which project and goal are currently selected.

6. **Importance is relative** — when the user says "make goal X more important", you need to increase its importance slider, understanding that sibling goals will automatically decrease.

7. **Clockwise = sequence** — goals arranged clockwise represent the order/sequence. This order can only be changed by dragging segments to a new position — there is no sort button.

8. **Progress propagation** — leaf goal progress is manual; parent goals auto-calculate from children weighted by importance.

9. **Double-click to drill in/out** — double-click a goal with subgoals to center it; double-click the center to go back up (only works if you’ve previously drilled in — at the root level there’s nowhere to go up to). Goals without subgoals won’t respond to double-click for centering.

10. **Unlimited depth** — goals can be broken down into as many levels as needed. Encourage users to break goals into smaller, more achievable pieces.

11. **Use the Undo button in the top bar** (left arrow icon) instead of ⌘Z when the Notes editor has focus, as keyboard undo may be captured by the text editor instead of the Goalscape app.

12. **Blue circle handle = importance** — The small blue circle at the outer edge of a selected segment is for adjusting importance by dragging. It is NOT for reordering goals. Be careful not to accidentally drag it.

13. **Always use `form_input` for renaming goals** — Never use `type` after creating or selecting a goal. Use `read_page(filter="interactive")` to find the goal name textbox in the right panel, then `form_input(ref=..., value="Name")`. Typing without a confirmed text input focus will inject characters into the SVG diagram.

14. **Use the lock/unlock workflow for setting multiple importance values** — When setting specific percentages on multiple siblings, set each value and lock it before moving to the next. Unlock all when done. See "Set Importance on Multiple Sibling Goals" above.

15. **Fix shifted UI with `document.body.scrollLeft = 0`** — Browser automation clicks (via Chrome DevTools Protocol) can cause `document.body.scrollLeft` to become stuck at a non-zero value (e.g. 560px), which shifts the entire UI to the left so the diagram and top bar are partially or fully off-screen. **Root cause:** Goalscape's inactive Angular Material tab bodies (`mat-tab-body`) in the right panel generate `scrollWidth` wider than `offsetWidth`, and their parent `mat-tab-body-wrapper` has `overflow: visible`, so this overflow propagates up to `<body>`. Normally this is invisible because `body` has `overflow-x: hidden`. However, when a browser-level (trusted) click is dispatched — as automation tools do — the browser's native focus-scroll behavior scrolls the body to bring the focused element into view within the overflowing content. JavaScript `.click()` does NOT trigger this because it's not a trusted user gesture. **The fix:** Run `document.body.scrollLeft = 0` via JavaScript after any automation click that triggers a modal or dialog. **Prevention:** When opening dialogs like Share, prefer using `javascript_tool` to call `.click()` on the button instead of using the `computer` tool's `left_click` action.
