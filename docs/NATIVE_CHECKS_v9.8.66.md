# Windows checks for v9.8.66

The build environment cannot execute the native Windows GUI.

1. Close the old copy, extract this build into a new folder and confirm the
   new EXE's title reads v9.8.66.
2. Check the grey captions under Total current, Current spread, Avg spread,
   Safeguard, GPU connector and Max spread. They should sit between the large
   reading and the card bottom, without touching the border or clipping.
3. Resize through short and tall windows, and switch compact/full mode. Check
   normal DPI settings, including 100%, 125%, 150%, 175% and 200% if available.
4. Check the overview cards in a saved-log report; they use the same renderer.

The caption layout uses the selected native font's measured height. All
v9.8.65 calculations and monitoring behavior are retained.
