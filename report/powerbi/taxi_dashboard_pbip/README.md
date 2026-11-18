# taxi_dashboard (Power BI Project)

Open `taxi_dashboard.pbip` in Power BI Desktop (Windows).

1. First time only: File > Options > Preview features > enable **Power BI Project (.pbip) save option** and **Store reports using enhanced metadata format (PBIR)**; restart.
2. Copy the project folder to C:\taxi_analytics (see report/powerbi/claude_desktop_guide.md, step 1.1).
3. Open the .pbip. If asked, set the **DataPath** parameter (Home > Transform data > Edit parameters) to the full path of data\powerbi\taxi_trips.csv, then Refresh.
4. Check: Trips 6,370 · Total Revenue $117,548 · Net Revenue $102,921 · Avg $18.45 · Card Share 71.4% · Peak Hour 18:00.
5. Sync the slicers: View > Sync slicers (set the five slicers to all pages).
6. File > Save as .pbix if you want a single file.

Not included (add by hand, see dashboard_design.md): Top-N filters, conditional colours / heatmap gradient, navigation buttons, tooltip and drill-through pages.
