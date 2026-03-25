# EasySchedule_HTML
EasySchedule is a self-contained, local-only HTML stripboard tool built to quickly generate production schedules for video production. It's designed for small production teams, indie projects, and productions that need to build schedules on the fly under short turnarounds. The focus is speed, clarity, and direct control without unnecessary layers.
EasySchedule runs entirely in your browser. It doesn't store, transmit, or track any information you enter. Everything exists locally in your session unless you explicitly export it.

You can save your project as a CSV file and load it later to continue working. All strip data, project settings, and Boneyard state are preserved exactly as you left them.

## **Features**

### Scene & Banner Strips
Create scene strips with full metadata:
 - INT/EXT
 -  Set 
 - Location 
 - Cast 
 - Time of Day (DAY / NIGHT / DAWN / DUSK)
 - Script length (pages and eighths)
 - Estimated time

Banner strips can be used for lunch, company moves, breaks, or special events. Banner beginning times are calculated automatically based on call time and cumulative schedule duration.

## Drag-and-Drop Reordering
Rearrange your shooting order by dragging strips via their handles.

Once finalized, lock the strip order to prevent accidental changes. When locked, reordering and structural edits are disabled.

## Cast, Location & Set Management
Assign:

 - Cast members by number 
 - Locations 
 - Sets

Reference these in scene strips without retyping. Cast legend appears on the printed stripboard with circled numbers.

## Boneyard
Move unused or cut scenes to the Boneyard. Boneyard strips are removed from the main schedule but preserved for review or restoration.

## Color Coding
Scenes are automatically color-coded by time of day:

⚪ Day Interior (DAY + INT) 
🟡Day Exterior (DAY + EXT)
🔵Night Interior (NIGHT + INT)
🟢Night Exterior (NIGHT + EXT)
🩷Dawn (DAWN)
🟠Dusk (DUSK)

Banner strips have three color options:

 - Beige 
 - Lavender
 -  Salmon

## Export & Print
Preview generates a printable stripboard with:

 - Full scene details 
 - Cast legend 
 - Day totals 
 - Timeline 
 - Industry-standard colors

Export to browser print or PDF. All color coding is preserved.

## What This Tool Isn't
EasySchedule was originally designed for single day schedules. Multi-day functionality is there, but is still limited to daybreak logic. That means:

 - No autodaybreak
 - No union timing
 - No weekend logic
 - No calendar
**(yet!)**

EasySchedule is not a breakdown tool, nor a call sheet tool.

## **Typical Usage**
1. Import FDX or load CSV
2. Click "Sort Strips"
3. Choose mode (uncheck for Global, check for Within Days)
4. Confirm dialog (shows scene count, criteria, warning)
5. Brief loading indicator
6. Stripboard sorted and displayed
7. Review problematic strips alert (if any)
8. Manually adjust if needed
9. Save CSV to preserve sorted order

## Roadmap

1. Include autodaybreak functions based on scene length/ Est. time
2. Cast, Set and Location assignment consolidation engine (eliminates duplicates)
3. More scene strip sort options
4. Greyscale print mode
5. Scene, Cast, Set and Location filters for Print
6. Weekend traversal logic
7. Expand FDX parser
