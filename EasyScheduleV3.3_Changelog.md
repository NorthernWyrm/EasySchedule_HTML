# CHANGELOG

## Version 3.3.0 - Strip Sorting Algorithm (2025-03-12)

### 🎯 Major Feature: Strip Sorting System

#### **Complete Sorting Implementation**
- **Sort Strips Button**: New button in action bar between Add buttons and Filters
- **Two Sorting Modes**: Global (entire stripboard) and Within Days (per day segment)
- **Three-Tier Priority**: Sorts by Set (A-Z) → Time of Day (Dawn→Day→Dusk→Night) → INT/EXT (INT→EXT→INT/EXT)
- **Movie Magic Behavior**: Global sort moves banners to top and removes daybreaks
- **Day-Aware Sorting**: Within Days mode respects daybreak boundaries, sorts each day independently
- **Stable Sort Algorithm**: Preserves original order when all sort keys are equal

### 🎯 Major Feature: Precise Daybreak Placement

#### **Add Daybreak Below Button**
- **Green "+ Daybreak" button** added to all scene and banner strips (expanded view)
- **Precise placement**: Click button to insert daybreak immediately below that strip
- **Color-coded interface**: Green button signals "add/build" action (distinct from duplicate/boneyard/delete)
- **Lock integration**: Button disabled when stripboard is locked
- **No confirmation dialog**: Quick action for rapid day structuring
- **Filter-aware**: Works correctly when filters are active, inserts in canonical array position

#### **Button Color Language**
- 🟢 **Green** (+ Daybreak) - Adds/builds structure
- 🟡 **Gold** (Duplicate) - Creates/prevents changes
- ⚪ **Gray** (Boneyard) - Deactivates
- 🔴 **Red** (Delete) - Danger/permanent removal

### 🔧 Sorting Features

#### **UI Controls**
- "Sort Strips" button with confirmation dialog (always shown)
- "Within Days" checkbox to toggle between Global and Within Days modes
- Loading indicator during sort operation (lightweight overlay)
- Lock integration: Sort disabled when stripboard is locked
- Checkbox resets to unchecked on CSV load (session-only state)

#### **Set Name Comparison**
- **Alphabetical sorting**: Case-insensitive, locale-aware using `localeCompare()`
- **Special handling for problematic strips**:
  - Valid set names (A-Z) sorted first
  - "UNNAMED SET" sorted after valid names (parser failures needing attention)
  - Null/empty strings sorted last (legitimate unpopulated scenes)
- **Sorting order**: `"AIRPORT" ... "ZEBRA" ... "UNNAMED SET" ... null/empty`

#### **Time of Day Normalization**
- **Canonical order**: Dawn (0) → Day (1) → Dusk (2) → Night (3)
- Null/undefined defaults to "Day"
- Consistent with existing ToD field values

#### **INT/EXT Normalization**
- **Canonical order**: INT (0) → EXT (1) → INT/EXT (2)
- Null/undefined defaults to "INT"
- Full support for INT/EXT value (added in V3.2)

### 📊 Sorting Modes

#### **Global Sort**
1. Extracts all banners → moves to top of stripboard
2. Extracts all scenes → sorts by priority order
3. **Removes all daybreaks** (Movie Magic standard)
4. Rebuilds strips: [all banners, sorted scenes]
5. Appends single daybreak at end
6. Results in continuous single-day stripboard

#### **Within Days Sort**
1. Identifies day segments (between daybreaks)
2. For each segment independently:
   - Extracts banners → moves to top of that day
   - Extracts scenes → sorts by priority order
   - Rebuilds segment: [segment banners, sorted scenes]
3. **Preserves all daybreaks** (segments maintained)
4. Empty segments handled gracefully (daybreak preserved or removed)
5. Results in multi-day stripboard with each day sorted internally

### 🔍 Problematic Strip Detection

- **Automatic detection** of "UNNAMED SET" and null/empty set names
- **Post-sort alert** displays counts and asks user to review
- **Alert wording**: "3 'UNNAMED SET' strips and 2 strips with no set name have been moved to the bottom. Please review and assign proper set names."
- Grammatically correct (singular/plural handling)

### 🎨 Integration

#### **Lock System**
- Sort button disabled when stripboard locked
- Sort button enabled when unlocked
- Sort button enabled on CSV load (lock reset)

#### **Filter System**
- All active filters cleared after sort
- Ensures user sees full sorted result
- Consistent with sort being a major reorganization

#### **Schedule Recalculation**
- `recalcSchedule()` called after every sort
- Daybreak metrics updated correctly
- Timeline recalculated

#### **UI Updates**
- `renderStrips()` displays sorted stripboard
- `scrollToTop()` scrolls workspace to beginning
- User immediately sees sorted result

### 📋 Data Integrity

#### **Preserved During Sort**
- Scene numbers (strips keep original scene numbers, no renumbering)
- All strip content (set, location, description, cast, ToD, page length, estimated time)
- Strip metadata (IDs, active state, original index)
- Banner content and colors
- Daybreak call times (in Within Days mode)

#### **Modified During Sort**
- Strip order (reordered according to sort algorithm)
- Daybreak structure (Global removes all, Within Days may remove empty segments)

### 🧪 Edge Cases Handled

- **Empty stripboard** (0 scenes): Confirmation shows "0 scenes", sort executes safely
- **Single scene**: Sort executes without error
- **Only banners**: Sort executes without error (no scenes to sort)
- **No daybreaks**: Within Days mode falls back to Global sort
- **Empty day segments**: Handled gracefully (daybreak preserved or cleaned up)
- **Back-to-back daybreaks**: Empty segments cleaned up automatically
- **All scenes identical**: Stable sort preserves original order
- **Repeated sorts**: Can sort multiple times, each sort starts from current state

### 🚀 Performance

- **Global Sort**: O(n log n) time complexity
- **Within Days Sort**: O(n log n) total across all segments
- **Instant execution** for typical stripboards (10-100 scenes)
- **No optimization needed** for expected use cases
- **Lightweight loading indicator**: Minimal overhead (2 DOM elements, inline CSS)

### 📝 User Workflow

#### **Typical Usage**
1. Import FDX or load CSV
2. Click "Sort Strips"
3. Choose mode (uncheck for Global, check for Within Days)
4. Confirm dialog (shows scene count, criteria, warning)
5. Brief loading indicator
6. Stripboard sorted and displayed
7. Review problematic strips alert (if any)
8. Manually adjust if needed
9. Save CSV to preserve sorted order

#### **Post-Sort Actions Available**
- Manual drag-and-drop reordering (if unlocked)
- Add/remove daybreaks to reorganize days
- Lock stripboard to prevent further changes
- Save CSV to persist sorted order
- Preview/print sorted stripboard

### 🐛 Bug Fixes

- **Within Days checkbox state**: Now resets to unchecked on CSV load (consistent with session-only state)
- **Empty day segment handling**: Properly handled in Within Days mode
- **Sort button lock state**: Correctly disabled/enabled on lock toggle and CSV load

### 🔒 Limitations & Design Decisions

- **No undo**: Sort cannot be undone (by design - user has Save/Load as safety net)
- **Confirmation always shown**: Prevents accidental sorts on large stripboards
- **Within Days checkbox not persisted**: Session-only state, always resets to Global mode
- **Empty day cleanup**: Within Days mode may remove empty segments (intentional optimization)
- **Global removes daybreaks**: Movie Magic standard behavior for cross-day sorting

### 📚 Technical Implementation

#### **New Functions** (~510 lines total)
- `compareSetNames()`: Set comparison with special handling
- `compareTimeOfDay()`: ToD comparison with canonical order
- `compareIntExt()`: INT/EXT comparison with canonical order
- `compareStrips()`: Master comparison function (stable sort)
- `detectProblematicStrips()`: Counts UNNAMED SET and null sets
- `alertProblematicStrips()`: User-friendly alert with grammar handling
- `initiateSortStrips()`: Entry point, lock check, confirmation
- `confirmSort()`: Confirmation dialog with mode-specific messaging
- `showSortingIndicator()` / `hideSortingIndicator()`: Loading overlay
- `sortStripsGlobally()`: Global sort implementation
- `sortStripsWithinDays()`: Within Days sort implementation
- `scrollToTop()`: Workspace scroll utility
- `addDaybreakBelow()`: Inserts daybreak immediately below specified strip

#### **Modified Functions**
- `toggleStripOrderLock()`: Added Sort button disable/enable
- `loadFromCSV()`: Added Sort button enable and checkbox reset

#### **CSS Added**
- `.sort-checkbox-label`: Checkbox styling consistent with UI
- `.btn-add-daybreak`: Green button styling for daybreak addition
- Hover states and spacing

#### **HTML Added**
- Sort Strips button
- Within Days checkbox with label
- "+ Daybreak" button in scene strip actions
- "+ Daybreak" button in banner strip actions

#### **HTML Added**
- Sort Strips button
- Within Days checkbox with label

---

## Version 3.2.0 - FDX Import System (2025-03-11)

### 🎯 Major Feature: FDX Import

#### **Complete Final Draft Import Pipeline**
- **Import FDX Button**: New menu bar button for importing .fdx, .fdxt, .xml files
- **Full Scene Extraction**: Parses scene headings, metadata, and cast from FDX files
- **Multi-Format Support**: Handles FDX files from Final Draft, Fade In, and other screenwriting tools
- **Robust Error Handling**: Validates XML structure and provides clear error messages
- **Smart Fallback**: Gracefully handles malformed or incomplete FDX data

### 🔧 Scene Heading Parser

#### **INT/EXT Extraction**
- Supports standard formats: INT, EXT
- **NEW: INT/EXT Support**: Added as third option to data model and UI
- Handles variations: INT./EXT., I/E, INT. / EXT. (with spaces)
- Normalizes to canonical values: INT, EXT, INT/EXT

#### **Set Name Parsing**
- Extracts set names between INT/EXT and Time of Day
- Handles multiple separator types: dashes (-, –, —) and periods (.)
- Preserves full set names: "BODEGA TEXTIL/ ZONA DE DESCARGA"
- Cleans trailing punctuation automatically
- Respects user intent from screenplay

#### **Time of Day Normalization**
- **60+ variations supported** (English + Spanish)
- Canonical mapping to 4 standard values: Day, Night, Dawn, Dusk
- English examples: DAY, NIGHT, SUNRISE→Dawn, MAGIC HOUR→Dusk, CONTINUOUS→Day
- Spanish examples: DÍA→Day, NOCHE→Night, AMANECER→Dawn, ATARDECER→Dusk
- **Smart ToD Detection**: Uses FIRST valid Time of Day found (handles "DÍA - TIEMPO ONÍRICO")
- Defaults to "Day" for ambiguous or missing values

#### **Scene Number Extraction**
- **3-Tier Priority System**:
  1. `Number` attribute in FDX (if present)
  2. Parse from text ("1. ", "5A. ", "43. ")
  3. Auto-number sequentially
- Preserves revision numbers (1A, 5B, etc.)
- Handles varied prefixes: "1. ", "1.- ", "30.- "
- Skips OMITTED scenes automatically

### 👥 Cast Extraction System

#### **Two-Source Hierarchy**
- **PRIMARY Source**: `<Paragraph Type="Character">` (characters with dialogue)
- **SECONDARY Source**: `<CharacterArcBeat>` (non-speaking roles)
- Merges both sources and deduplicates
- Prioritizes dialogue presence over metadata

#### **Cast List Auto-Population**
- Extracts unique character names from FDX
- Auto-generates sequential cast numbers (1, 2, 3...)
- Populates Cast List UI automatically
- Converts character names to cast numbers in strips
- **Clears previous cast list** on import for fresh start

### 📊 Page Length Parsing

- Handles FDX Length formats: "6/8", "2 3/8", "1"
- Converts to EasySchedule format: "whole fraction/8"
- Defaults to "A 1/8" if missing (scene must have length)
- Preserves exact page counts from screenplay

### 🎨 UI Updates

#### **INT/EXT Dropdown Enhancement**
- Added INT/EXT as third option (was only INT, EXT)
- Available in both active scenes and boneyard
- Color mapping: INT/EXT uses INT colors (white/blue scheme)

#### **Assignment List Behavior**
- **All lists start empty** (Cast, Location, Set)
- Populated only from: CSV load, FDX import, manual addition
- **Clears on FDX import**: Fresh start for each screenplay
- Auto-populates orphaned assignments with validation

### 🐛 Bug Fixes

#### **Data Model Consistency**
- Fixed INT/EXT not being selectable option in dropdowns
- Fixed assignment lists appending instead of replacing on import
- Fixed default "Cast 01", "Set 01", "Location 01" persisting after import
- All assignment lists now normalized to start empty

#### **Parser Edge Cases**
- Fixed scene headings with periods as separators ("PARTY. NIGHT.")
- Fixed scene number prefixes with dashes ("1.- ", "30.- ")
- Fixed multiple Time of Day values (uses first valid match)
- Fixed spaced INT/EXT variations ("INT. / EXT.")
- Fixed trailing punctuation in set names

### 📋 Post-Import Behavior

**Automatic Actions After FDX Import:**
- Assignment lists cleared and repopulated
- Validation warnings for orphaned references
- Filters cleared to default state
- Schedule metrics recalculated
- Workspace scrolled to top
- Success message with scene count

**User Review Required:**
- Estimated time (defaults to 15 min/scene - user adjusts)
- Scene descriptions (not in FDX - user adds)
- Locations (not parsed per production requirements)

### 🧪 Testing & Validation

#### **Comprehensive Regression Suite**
- 44 core functionality tests ✅
- 9 error handling tests ✅
- **Total: 53/53 tests passing**

#### **Test Coverage**
- Scene number extraction (all 3 priority levels)
- INT/EXT parsing (6 format variations)
- Set name extraction (5 edge cases)
- Time of Day normalization (60+ variations)
- Cast extraction (dialogue + non-speaking)
- Page length parsing (4 formats)
- Error handling (malformed XML, missing elements, empty files)

#### **Real-World Testing**
- Simple FDX: FadeIn formatted (clean structure)
- Typical FDX: FBI English screenplay (standard production)
- Complex FDX: MAC Spanish screenplay (heavy styling, multiple Text nodes)

### 📝 Technical Implementation

#### **New Functions Added** (~450 lines)
- `importFDX()`: Entry point and file picker
- `parseFDX()`: Main parser with XML validation
- `parseScenes()`: Scene iteration and extraction
- `extractFullText()`: Multi-node Text concatenation
- `extractSceneNumber()`: 3-tier priority extraction
- `parseSceneHeading()`: INT/EXT, Set, ToD parsing
- `normalizeIntExt()`: INT/EXT variation handling
- `normalizeTimeOfDay()`: Canonical ToD mapping (60+ values)
- `parseFDXLength()`: Page length conversion
- `extractCastForScene()`: Two-source cast hierarchy
- `processFDXCast()`: Cast list population and name→number conversion
- `calculateEstTime()`: Default time estimation

#### **Modified Functions**
- Color mapping: Added INT/EXT support
- Import workflow: Added assignment list clearing
- Post-import: Added validation and auto-population

### 🔒 Data Integrity

- **Session-Only Import**: FDX import replaces current stripboard
- **Confirmation Dialog**: Warns user before replacing existing data
- **Canonical Data**: Strips normalized to EasySchedule format
- **No Location Import**: Per production requirements (locations set during prep)
- **Preserve User Intent**: Full set names, scene numbers, cast assignments maintained

### 🚀 Performance

- Instant parsing for typical screenplays (<100 scenes)
- Handles complex multi-node Text structures (MAC Spanish file: 84 scenes)
- Graceful degradation for malformed scenes (logs warning, continues parsing)
- Lightweight: No external dependencies (uses native DOMParser)

---

## Version 3.1.0 - Filter System (2025-03-11)

### 🎯 Major Features

#### **Stripboard Filtering System**
- **Day Filters**: Filter strips by shooting day with multi-select checkboxes
- **Set Filters**: Filter strips by set name with multi-select checkboxes
- **Location Filters**: Filter strips by location with multi-select checkboxes
- **Combined Filtering**: AND logic across categories (Day + Set + Location), OR logic within categories
- **Visual Filter Pills**: Active filters displayed as removable blue pills with individual × buttons
- **Clear Filters Button**: One-click removal of all active filters

#### **Filter UI Design**
- **Three Separate Dropdowns**: Independent dropdowns for Days, Sets, and Locations
- **Sticky Toolbar**: Filter controls remain visible when scrolling through strips
- **Horizontal Grid Layout**: Checkboxes flow left-to-right in responsive grid (120px min columns)
- **Auto-Close Behavior**: Dropdowns close when clicking outside or selecting another dropdown
- **Active State Styling**: Gold background with black text for open dropdowns
- **Blue Filter Pills**: High-contrast blue (#4A90E2) for readability on dark backgrounds

### 🔧 Filter Logic & Behavior

#### **Day Segmentation**
- Automatic day assignment based on daybreak positions
- Day 1 starts at beginning, increments after each daybreak
- Daybreaks belong to the day they end (strips above them)
- Empty days show daybreaks for drag-and-drop targets

#### **Smart Filtering**
- **Daybreaks**: Always shown for filtered days (provides context for cross-day organization)
- **Scenes**: Shown if in filtered day AND match all active filters
- **Banners**: Shown if in filtered day AND (location matches filter OR no location filter active)
- Banners without locations excluded when location filter active
- Banners with matching locations included even if only strip in day

#### **Cross-Day Drag & Drop**
- Drag strips between visible days while filters active
- Skip hidden days seamlessly (e.g., drag from Day 1 to Day 3 with Day 2 hidden)
- Supports location consolidation workflow: filter by location, drag scenes to same day
- Insertion line shows exact drop position
- Enhanced visual feedback: 2% scale + shadow on drag-over

### 📊 Data Integrity

- **Session-Only State**: Filters never saved to CSV
- **Auto-Clear on Load**: Filters reset when loading new CSV
- **Canonical Data Preserved**: Filters only affect UI rendering, never mutate strip data
- **Preview/Print Unaffected**: Always show ALL strips regardless of active filters
- **Boneyard Exclusion**: Inactive strips never appear in filtered views

### 🐛 Bug Fixes

#### **Daybreak Issues (V2.7.4)**
- Fixed daybreaks converting to banners on CSV save/load
- Added `type='daybreak'` handling to CSV export/import
- Fixed call time not persisting (added 'Call Time' column to CSV)
- Fixed all daybreaks showing same call time except last one
- Fixed daybreak metrics showing "?? of 1" after CSV load (added `recalcSchedule()` call)

#### **Drag & Drop Improvements (V2.8.0)**
- Implemented timed hold drag: 50ms for minimized strips, 0ms for expanded strips
- Added visual insertion line (4px gold with glow)
- Fixed drag-over feedback (scale + shadow transform)
- Implemented no-negation drag: last valid position used when dropping in gaps
- Fixed drag outside container now properly cancels

#### **Filter System Bugs**
- Fixed daybreaks disappearing when filtering to empty day
- Fixed banner filter logic to check location field
- Fixed banners with matching locations not appearing if only strip in day

### 🎨 UI/UX Improvements

#### **Strip Display (V2.8.0)**
- All strips default to minimized state (`uiMinimized: true`)
- Minimized content moved inside drag handle (unified visual)
- **Description field added** to minimized strips (truncated at 200px with ellipsis)
- Data displayed in bold black for better readability
- Double-click strip border/handle to expand/collapse
- Minimized strips fully draggable (entire strip is drag zone)
- Expanded strips draggable only via handle

#### **Filter Toolbar**
- Positioned in sticky action-buttons row (always visible when scrolling)
- Visual divider separates Add buttons from Filter controls
- Responsive layout with flex-wrap for narrow viewports
- Disabled state for Clear Filters when no filters active

### 🔒 Phase Completion Status

- ✅ **Phase 1**: Day Segmentation Foundation
- ✅ **Phase 2**: Filter State & Basic Logic  
- ✅ **Phase 3**: Filter UI Component
- ✅ **Phase 4**: Cross-Day Drag & Drop
- ✅ **Phase 5**: Edge Cases & Integration
- ✅ **Phase 6**: Final Regression Suite (47/47 tests passing)
- ✅ **Phase 7**: UI Polish & Three-Dropdown Design

### 📝 Technical Details

#### **New Functions**
- `assignDaySegments(strips)`: Assigns day numbers based on daybreak positions
- `getAvailableFilterOptions()`: Collects unique days, sets, locations for checkboxes
- `applyFilters(strips)`: Core filtering logic with daybreak inclusion
- `toggleFilterDropdown(type)`: Opens/closes specific filter dropdown
- `populateFilterUI()`: Generates filter checkboxes from current strips
- `createFilterCheckbox(type, value, label)`: Creates individual checkbox element
- `toggleFilter(type, value)`: Adds/removes filter and rerenders
- `createFilterPill(type, value, label)`: Creates removable filter pill
- `clearAllFilters()`: Resets all filters and rerenders
- `updateFilterPills()`: Updates active filter pill display
- `updateClearButton()`: Enables/disables Clear Filters button
- `scrollToTop()`: Scrolls workspace to top on filter change
- `closeFilterDropdownIfClickedOutside(e)`: Auto-closes dropdowns

#### **Modified Functions**
- `renderStrips()`: Now calls `applyFilters()` before rendering
- `loadFromCSV()`: Clears filters and calls `recalcSchedule()` after load
- `recalcSchedule()`: Fixed to use each daybreak's own call time
- `getMetricsForDaybreak()`: Uses canonical strip list instead of filtered
- `handleContainerDrop()`: Enhanced for cross-day drag with filters

#### **CSV Format Changes**
- Added 'Call Time' column (scenes: empty, daybreaks: HH:MM, banners: empty)
- Explicit `type='daybreak'` handling (previously fell through to banner)
- No filter-related data stored (filters are session-only UI state)

### 🧪 Testing

**Comprehensive test matrix passed:**
- Core functionality: 16/16 tests ✅
- Filter functionality: 9/9 tests ✅
- Filter integration: 12/12 tests ✅
- Day segmentation: 4/4 tests ✅
- Data integrity: 8/8 tests ✅

**Known Issues:**
- None identified in production testing

### 🚀 Performance

- Filters render instantly even with 50+ sets/locations
- Horizontal grid layout prevents dropdown overflow
- Individual column scrolling for long filter lists (max-height: 300px)
- Lightweight DOM updates (only renders filtered strips)

### 📋 Dependencies

- No new external dependencies
- Uses existing Papa Parse for CSV operations
- Pure JavaScript implementation

---

## Version 3.0.2 - Drag & Drop Enhancements

### Features
- Enhanced drag-and-drop with visual insertion line
- Timed hold drag (150ms → 50ms for minimized, 0ms for expanded)
- No-negation drag behavior (uses last valid position)

### Bug Fixes
- Fixed drag cancellation when cursor leaves strips
- Fixed insertion line positioning
- Fixed drag-over visual feedback

---

## Version 3.0.1 - Bug Fixes

### Bug Fixes
- Fixed CSV metadata parsing for JSON with commas
- Fixed multi-line cast legend with dynamic height
- Fixed boneyard drag-and-drop using strip IDs instead of indices
- Fixed input sanitization (control character removal)
- Fixed preview exception handling

---

## Version 3.0.0 - Strip UI Overhaul

### Features
- Minimized strip display as default
- Compact horizontal data layout in minimized state
- Double-click to expand/collapse
- Strip-specific drag zones (entire strip vs handle only)

### UI Changes
- Minimized content inside drag handle
- Bold black text for all data fields
- Visual hierarchy improvements

---

## Version 2.7.3 - Input Sanitization & Error Handling

### Features
- Input length limits for all fields
- Control character sanitization
- Preview error handling with retry

---

## Version 2.7.2 - Validation & Auto-Population

### Features
- Auto-populate orphaned cast/location/set assignments
- Warning dialogs before auto-population
- Placeholder naming: Cast 01, Location 01, Set 01

---

**FDX Import System - Production Ready**
- Total implementation: Complete parser pipeline
- Lines added: ~450 lines of robust, documented code
- Tests passed: 53/53 (100%)
- Real-world validation: 3 production FDX files (simple, typical, complex)
