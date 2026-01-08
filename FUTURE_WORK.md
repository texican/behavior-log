# Future Work: Configuration Externalization (Behavior Log)

This document outlines planned improvements to make the behavior-log project fully configurable and clone-friendly for other users.

## Current State (2026-01-08)

✅ **Completed:**
- `.clasp.json` added to `.gitignore`
- `.clasp.json.example` template created
- Google Apps Script IDs protected from version control
- Never committed sensitive Script IDs to git history
- Uses placeholder for `DEFAULT_SPREADSHEET_ID` (good practice)

⚠️ **Needs Investigation:**
- Potential hardcoded personal data (to be audited)
- Behavior categories or impact types that should be configurable
- Personal location names or context
- Default values that should be configurable

---

## Priority 1: Audit for Hardcoded Personal Data

### Actions Required

1. **Audit behavior-log submodule** for hardcoded personal data:
   - [ ] Check `code.gs` for hardcoded names, locations, or personal identifiers
   - [ ] Check `index.html` for hardcoded dropdown options
   - [ ] Review form fields for personal defaults
   - [ ] Identify any validation logic tied to specific personal values

2. **Check for similar patterns as chess-tracker:**
   - Hardcoded user names or identifiers
   - Location-specific logic or venue names
   - Personal context or categories
   - Default values that vary by user

3. **Document findings:**
   - List all hardcoded personal data with file paths and line numbers
   - Prioritize by sensitivity (personal info vs. preferences)
   - Identify which values should be configurable

### Potential Issues to Check

- **Behavior categories:** Are there hardcoded behavior types or categories?
- **Impact types:** Are energy/wealth impact values hardcoded?
- **Personal locations:** Any location names or context?
- **Time periods:** Hardcoded time ranges or frequencies?
- **Default values:** Any personal preferences in defaults?

---

## Priority 2: Configuration Externalization Plan

### Proposed Solution (If Hardcoded Data Found)

**Use Google Apps Script Properties Service** (same pattern as chess-tracker):

1. **Server-side changes** (code.gs):
   ```javascript
   /**
    * Get configuration from Script Properties
    * @returns {Object} Configuration object
    */
   function getConfig() {
     const props = PropertiesService.getScriptProperties();

     return {
       // Example - adjust based on audit findings
       behaviorCategories: (props.getProperty('BEHAVIOR_CATEGORIES') || 'Work,Exercise,Social').split(',').map(c => c.trim()),
       impactTypes: (props.getProperty('IMPACT_TYPES') || 'Energy,Wealth,Health').split(',').map(i => i.trim()),
       defaultUser: props.getProperty('DEFAULT_USER') || '',
       customField1: props.getProperty('CUSTOM_FIELD_1') || ''
     };
   }
   ```

2. **Client-side changes** (index.html):
   ```javascript
   // On page load, fetch configuration
   google.script.run
     .withSuccessHandler(function(config) {
       populateBehaviorCategories(config.behaviorCategories);
       populateImpactTypes(config.impactTypes);
       // etc.
     })
     .getConfig();

   function populateBehaviorCategories(categories) {
     const select = document.getElementById('behavior-category');
     categories.forEach(category => {
       select.add(new Option(category, category));
     });
   }
   ```

### Configuration Setup Instructions

Users would set Script Properties via:
1. Google Apps Script Editor → Project Settings → Script Properties
2. Add properties as needed based on audit results

---

## Priority 3: Enhanced Documentation

### Files to Create/Update

1. **behavior-log/SETUP.md** (new)
   - Step-by-step first-time setup guide
   - How to configure Script Properties
   - Screenshots of Script Properties interface
   - Example configurations for different use cases

2. **behavior-log/README.md** (update)
   - Add "Configuration" section
   - Document all Script Properties
   - Explain `.clasp.json` setup
   - Link to SETUP.md

### Script Properties Reference Table (Example)

| Property Key | Type | Default | Example | Description |
|---|---|---|---|---|
| `SPREADSHEET_ID` | String | (auto-create) | `1abc...xyz` | Target Google Sheet ID |
| `BEHAVIOR_CATEGORIES` | Comma-separated | TBD | `Work,Exercise,Social` | Available behavior categories |
| `IMPACT_TYPES` | Comma-separated | TBD | `Energy,Wealth,Health` | Impact type options |
| `DEFAULT_USER` | String | (empty) | `John Doe` | Default user name if applicable |

*Note: Actual properties will be determined after audit phase*

---

## Implementation Phases

### Phase 1: Security (✅ COMPLETED)
- [x] Add `.clasp.json` to `.gitignore`
- [x] Create `.clasp.json.example` template
- [x] Ensure no sensitive data in version control

### Phase 2: Audit & Discovery (FUTURE)
- [ ] Audit code.gs for hardcoded personal data
- [ ] Audit index.html for hardcoded form values
- [ ] Document all findings with file paths and line numbers
- [ ] Prioritize configuration items by importance
- [ ] Design configuration schema based on findings

### Phase 3: Configuration System (FUTURE - IF NEEDED)
- [ ] Implement server-side `getConfig()` function
- [ ] Add configuration validation
- [ ] Document Script Properties setup
- [ ] Create default configuration values

### Phase 4: Behavior Log Refactoring (FUTURE - IF NEEDED)
- [ ] Externalize any identified hardcoded data
- [ ] Update client-side to populate dropdowns dynamically
- [ ] Remove all hardcoded personal references
- [ ] Add configuration fallbacks for missing properties

### Phase 5: Testing & Documentation (FUTURE)
- [ ] Test with different configurations
- [ ] Write SETUP.md guide
- [ ] Update README with configuration instructions
- [ ] Test fresh clone and deployment process
- [ ] Create migration guide for existing users (if needed)

---

## Estimated Effort

*To be determined after audit phase*

Initial estimate (if similar to chess-tracker):

| Phase | Files Modified | Files Created | Est. Lines Changed |
|-------|---|---|---|
| Phase 2: Audit | 0 | 0 | 0 (investigation only) |
| Phase 3: Config System | 1 | 0 | ~30 |
| Phase 4: Refactoring | 2 | 0 | ~50-100 |
| Phase 5: Documentation | 2 | 1 | ~150 |
| **TOTAL** | **5** | **1** | **~230-280** |

*Note: Actual effort depends on audit findings*

---

## Breaking Changes & Migration

### For Existing Users (If Changes Required)

When configuration work is implemented, existing deployments may need to:

1. **Set up Script Properties** (one-time):
   - Open Google Apps Script Editor
   - Go to Project Settings → Script Properties
   - Add required properties based on audit findings

2. **Update `.clasp.json`** (if using clasp):
   - Copy `.clasp.json.example` to `.clasp.json`
   - Replace with your Script ID

3. **No data migration required** - existing spreadsheets will continue to work

### Migration Guide Template

```markdown
# Migration Guide: v1.x to v2.0 (Behavior Log)

## What's Changed
- [To be determined based on audit findings]

## Migration Steps
1. Open your Google Apps Script project
2. Click Project Settings (⚙️ icon)
3. Scroll to Script Properties section
4. Add the following properties:
   - [Properties to be listed after audit]
5. Deploy the updated code
6. Test by submitting a behavior entry
```

---

## Testing Requirements

Before marking this work complete, test:

- [ ] Fresh clone experience (new user)
- [ ] Configuration via Script Properties
- [ ] Form submission with configured values
- [ ] Validation with dynamic configuration
- [ ] Spreadsheet creation and data logging

*Additional tests to be added based on audit findings*

---

## Optional Future Enhancements

### Configuration UI (Admin Panel)
- Web-based configuration interface
- Edit Script Properties through the web app
- No need to access Apps Script Editor

### Configuration Import/Export
- Export configuration as JSON
- Import configuration from JSON
- Share configuration templates

### Templates & Presets
- Pre-configured templates for different use cases
- Quick-start configurations
- Export/import entire setups

---

## References

- Original scoping discussion: 2026-01-08
- Chess-tracker FUTURE_WORK.md (similar patterns)
- Google Apps Script Properties Service: https://developers.google.com/apps-script/reference/properties

---

## Next Steps

1. **Immediate:** Conduct thorough audit of behavior-log codebase
2. **Document:** List all hardcoded personal data found
3. **Decide:** Determine if configuration externalization is needed
4. **Implement:** Follow phases 2-5 if audit reveals issues

---

**Last Updated:** 2026-01-08
**Status:** Security phase complete, audit phase pending
