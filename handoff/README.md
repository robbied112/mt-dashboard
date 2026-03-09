# Build Scripts & Templates Handoff from Cowork

These are the WORKING production files from the OneDrive folder.
Claude Code should use these as the source for the mt-crm repo.

## scripts/
- `build_dashboard.py` (2,426 lines) - Executive dashboard build script
- `build_rep_dashboard.py` (2,150 lines) - Rep dashboard build script

Both scripts need:
1. Add argparse for --data, --template, --output flags
2. Replace hardcoded file paths with the --data directory
3. Keep ALL existing logic intact (VIP_WINE_MAP, MULTI_WAREHOUSE_GROUPS,
   consolidation functions, QB matching, inventory tracker date detection, etc.)

## templates/
- `executive_dashboard.html` - Active executive template WITH Account CRM section,
  Firebase, AccountStore, and account panel (added at bottom of file)
- `rep_dashboard_template.html` - Active rep template WITH My Action Items tab
  and all CRM/Firebase code
