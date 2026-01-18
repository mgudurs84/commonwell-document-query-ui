# Replit Prompt: CommonWell DocumentReference Query UI

## Overview

Build a React application that provides a user-friendly interface to query CommonWell Health Alliance FHIR R4 DocumentReference API. This is an internal testing/development tool for the CVS IAS Platform team to test document queries with various parameter combinations.

---

## Tech Stack Requirements

- **Framework**: React 18+ with TypeScript
- **UI Library**: Tailwind CSS + shadcn/ui components
- **HTTP Client**: Axios or Fetch API
- **State Management**: React useState/useReducer (no Redux needed)
- **Form Handling**: React Hook Form (optional but recommended)
- **Date Picker**: Use a date-time picker component for date fields

---

## Application Layout

### Header
- Title: "CommonWell Document Query Tool"
- Subtitle: "CVS IAS Platform - E2E Testing"
- Environment selector dropdown: Integration | Production

### Main Content (Two-Column Layout on Desktop)
- **Left Panel (40%)**: Query Builder Form
- **Right Panel (60%)**: Results Display

### Footer
- Show last query timestamp
- Show response time in milliseconds

---

## Query Builder Form (Left Panel)

### Section 1: Authentication

```
┌─────────────────────────────────────────────────────────┐
│ AUTHENTICATION                                          │
├─────────────────────────────────────────────────────────┤
│ Environment:  [Dropdown: Integration ▼ | Production]    │
│                                                         │
│ JWT Token: *                                            │
│ [Large textarea - 6 rows]                               │
│ [Paste your JWT token here...]                          │
│                                                         │
│ [ ] Show Token (toggle to reveal/hide)                  │
│ [Validate Token] button - decodes and shows expiry      │
└─────────────────────────────────────────────────────────┘
```

**JWT Token Field:**
- Required field
- Large textarea (6 rows minimum)
- Masked by default (show dots/asterisks)
- "Show Token" checkbox to toggle visibility
- "Validate Token" button that decodes the JWT and displays:
  - Expiration time (exp claim)
  - Subject ID
  - Organization ID
  - Purpose of Use
  - Warning if token is expired or expiring within 5 minutes

### Section 2: Patient Identifier (Required)

```
┌─────────────────────────────────────────────────────────┐
│ PATIENT IDENTIFIER (Required)                           │
├─────────────────────────────────────────────────────────┤
│ Assigning Authority ID (AAID): *                        │
│ [Text input: 2.16.840.1.113883.3.101.1]                │
│ (OID format - e.g., 2.16.840.1.113883.3.101.1)         │
│                                                         │
│ Patient ID: *                                           │
│ [Text input: patient123]                                │
│                                                         │
│ Combined Identifier Preview:                            │
│ [Read-only: 2.16.840.1.113883.3.101.1|patient123]      │
└─────────────────────────────────────────────────────────┘
```

**Validation:**
- AAID: Required, should match OID pattern (numbers and dots)
- Patient ID: Required, alphanumeric with hyphens allowed
- Show live preview of combined identifier in format: `{AAID}|{PatientID}`

### Section 3: Document Status Filter

```
┌─────────────────────────────────────────────────────────┐
│ DOCUMENT STATUS                                         │
├─────────────────────────────────────────────────────────┤
│ Status Filter:                                          │
│ ○ All (no filter)                                       │
│ ● Current (recommended)                                 │
│ ○ Superseded                                            │
│ ○ Entered in Error                                      │
└─────────────────────────────────────────────────────────┘
```

**Options:**
- Radio buttons
- Default selection: "Current"
- "All" means no status parameter is sent

### Section 4: Date Filters (Document Creation Date)

```
┌─────────────────────────────────────────────────────────┐
│ DOCUMENT CREATION DATE (when indexed in CommonWell)     │
├─────────────────────────────────────────────────────────┤
│ [ ] Enable Date Filter                                  │
│                                                         │
│ Date From (ge - greater than or equal):                 │
│ [DateTime Picker: 2025-12-15T08:00:00Z]                │
│                                                         │
│ Date To (le - less than or equal):                      │
│ [DateTime Picker: 2026-01-17T23:59:59Z]                │
│                                                         │
│ Quick Select:                                           │
│ [Last 24 Hours] [Last 7 Days] [Last 30 Days]           │
│ [Last 90 Days] [Last Year] [Custom]                    │
└─────────────────────────────────────────────────────────┘
```

**Features:**
- Checkbox to enable/disable this filter section
- Two datetime pickers with timezone support (default UTC)
- Quick select buttons that auto-populate the date range
- "Date From" maps to `date=ge{value}`
- "Date To" maps to `date=le{value}`
- Validation: Date From must be before Date To

### Section 5: Service Period Filters (Clinical Encounter Date)

```
┌─────────────────────────────────────────────────────────┐
│ SERVICE PERIOD (when clinical encounter occurred)       │
├─────────────────────────────────────────────────────────┤
│ [ ] Enable Period Filter                                │
│                                                         │
│ ⓘ Note: This filters by when the clinical service      │
│   happened, NOT when the document was uploaded.         │
│                                                         │
│ Service Start From (ge):                                │
│ [DateTime Picker]                                       │
│                                                         │
│ Service End To (le):                                    │
│ [DateTime Picker]                                       │
└─────────────────────────────────────────────────────────┘
```

**Features:**
- Checkbox to enable/disable
- Info tooltip explaining difference between `date` and `period`
- Maps to `period=ge{value}` and `period=le{value}`

### Section 6: Document Type Filter (LOINC)

```
┌─────────────────────────────────────────────────────────┐
│ DOCUMENT TYPE (LOINC Code)                              │
├─────────────────────────────────────────────────────────┤
│ [ ] Enable Document Type Filter                         │
│                                                         │
│ Select Document Type:                                   │
│ [Multi-select dropdown]                                 │
│ ☑ 34133-9 - Summarization of Episode Note (CCD)        │
│ ☐ 18842-5 - Discharge Summary                          │
│ ☐ 11506-3 - Progress Note                              │
│ ☐ 34117-2 - History and Physical                       │
│ ☐ 11488-4 - Consultation Note                          │
│ ☐ 28570-0 - Procedure Note                             │
│ ☐ 57133-1 - Referral Note                              │
│                                                         │
│ Or enter custom LOINC code:                             │
│ [Text input]                                            │
└─────────────────────────────────────────────────────────┘
```

**Features:**
- Checkbox to enable/disable
- Multi-select dropdown with common LOINC codes pre-populated
- Option to enter custom LOINC code
- Maps to `documenttype={code}`

### Section 7: Content Type Filter (MIME)

```
┌─────────────────────────────────────────────────────────┐
│ CONTENT TYPE (MIME Type)                                │
├─────────────────────────────────────────────────────────┤
│ [ ] Enable Content Type Filter                          │
│                                                         │
│ ⚠️ Note: Only applies to FHIR responders, not IHE/XCA  │
│                                                         │
│ Select Content Types:                                   │
│ [Multi-select dropdown]                                 │
│ ☑ application/xml - C-CDA XML Documents                │
│ ☐ application/pdf - PDF Documents                      │
│ ☐ text/xml - XML (alternative)                         │
│ ☐ text/plain - Plain Text                              │
│ ☐ application/dicom - DICOM Images                     │
│ ☐ image/jpeg - JPEG Images                             │
│ ☐ image/png - PNG Images                               │
│ ☐ image/tiff - TIFF Images                             │
└─────────────────────────────────────────────────────────┘
```

**Features:**
- Checkbox to enable/disable
- Warning note about IHE/XCA limitation
- Multi-select for content types
- Maps to `contenttype={mimetype}`

### Section 8: Author Filter

```
┌─────────────────────────────────────────────────────────┐
│ AUTHOR FILTER                                           │
├─────────────────────────────────────────────────────────┤
│ [ ] Enable Author Filter                                │
│                                                         │
│ Author First Name (Given):                              │
│ [Text input]                                            │
│                                                         │
│ Author Last Name (Family):                              │
│ [Text input]                                            │
└─────────────────────────────────────────────────────────┘
```

### Section 9: Action Buttons

```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│ [🔍 Execute Query]  [🔄 Reset Form]  [📋 Copy cURL]     │
│                                                         │
│ Generated Query URL:                                    │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ GET /v2/R4/DocumentReference?patient.identifier=    │ │
│ │ 2.16.840.1.113883.3.101.1|patient123&status=current │ │
│ │ &date=ge2025-12-15T08:00:00Z                        │ │
│ └─────────────────────────────────────────────────────┘ │
│ [📋 Copy URL]                                           │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**Features:**
- "Execute Query" - Primary button, makes the API call
- "Reset Form" - Clears all fields to defaults
- "Copy cURL" - Generates and copies full cURL command with headers
- Live preview of generated query URL (updates as form changes)
- "Copy URL" button for the query URL

---

## Results Display (Right Panel)

### Results Header

```
┌─────────────────────────────────────────────────────────┐
│ QUERY RESULTS                                           │
├─────────────────────────────────────────────────────────┤
│ Status: ✅ Success (200 OK)    Documents Found: 47      │
│ Response Time: 2,450 ms        Query Type: Incremental  │
└─────────────────────────────────────────────────────────┘
```

### Results Tabs

```
┌─────────────────────────────────────────────────────────┐
│ [Documents List] [Raw JSON] [Request Details]           │
├─────────────────────────────────────────────────────────┤
```

### Tab 1: Documents List (Default View)

Display documents in a table/card format:

```
┌─────────────────────────────────────────────────────────┐
│ 🔍 Filter results: [Search box]     Sort by: [Dropdown] │
├─────────────────────────────────────────────────────────┤
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 📄 Document #1                                      │ │
│ │ ─────────────────────────────────────────────────── │ │
│ │ Unique ID: eyJEb2NJZCI6IkJpbmFyeS9wYXRp...         │ │
│ │ Type: Clinical Note (clinical-note)                 │ │
│ │ Content Type: application/xml                       │ │
│ │ Status: current                                     │ │
│ │ Source: Oswego Health System                        │ │
│ │ Source Patient ID: T1-22198900                      │ │
│ │                                                     │ │
│ │ [📋 Copy ID] [🔗 Copy Download URL] [📥 Download]   │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ 📄 Document #2                                      │ │
│ │ ...                                                 │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

**Document Card Fields:**
- Document Unique ID (masterIdentifier.value) - truncated with copy button
- Category/Type
- Content Type (MIME)
- Status
- Source Organization
- Source Patient ID
- Download URL (with copy button)
- Actions: Copy ID, Copy URL, Download (attempts to fetch Binary)

### Tab 2: Raw JSON

```
┌─────────────────────────────────────────────────────────┐
│ [📋 Copy JSON] [💾 Download JSON] [Expand All]          │
├─────────────────────────────────────────────────────────┤
│ {                                                       │
│   "resourceType": "Bundle",                             │
│   "type": "searchset",                                  │
│   "total": 47,                                          │
│   "entry": [                                            │
│     {                                                   │
│       "fullUrl": "https://api...",                      │
│       "resource": {                                     │
│         "resourceType": "DocumentReference",            │
│         ...                                             │
│       }                                                 │
│     }                                                   │
│   ]                                                     │
│ }                                                       │
└─────────────────────────────────────────────────────────┘
```

**Features:**
- Syntax-highlighted JSON viewer
- Copy full JSON button
- Download as .json file
- Collapsible/expandable nodes

### Tab 3: Request Details

```
┌─────────────────────────────────────────────────────────┐
│ REQUEST                                                 │
├─────────────────────────────────────────────────────────┤
│ Method: GET                                             │
│ URL: https://api.integration.commonwellalliance...      │
│                                                         │
│ Headers:                                                │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Authorization: Bearer eyJhbGciOiJSUzI1NiI...        │ │
│ │ Content-Type: application/json                      │ │
│ │ Accept: application/fhir+json                       │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ Query Parameters:                                       │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ patient.identifier: 2.16.840.1.113883.3.101.1|...   │ │
│ │ status: current                                     │ │
│ │ date: ge2025-12-15T08:00:00Z                        │ │
│ └─────────────────────────────────────────────────────┘ │
│                                                         │
│ [📋 Copy as cURL]                                       │
├─────────────────────────────────────────────────────────┤
│ RESPONSE                                                │
├─────────────────────────────────────────────────────────┤
│ Status: 200 OK                                          │
│ Time: 2,450 ms                                          │
│                                                         │
│ Response Headers:                                       │
│ ┌─────────────────────────────────────────────────────┐ │
│ │ Content-Type: application/fhir+json                 │ │
│ │ X-Request-Id: abc123...                             │ │
│ └─────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```

---

## Error Handling

### Error Display

```
┌─────────────────────────────────────────────────────────┐
│ ❌ ERROR                                                │
├─────────────────────────────────────────────────────────┤
│ Status: 401 Unauthorized                                │
│                                                         │
│ Error Message:                                          │
│ JWT token has expired. Please generate a new token.     │
│                                                         │
│ Response Body:                                          │
│ {                                                       │
│   "error": "unauthorized",                              │
│   "message": "Token expired"                            │
│ }                                                       │
│                                                         │
│ Troubleshooting:                                        │
│ • Check if your JWT token is valid and not expired      │
│ • Verify the token was generated for the correct env    │
│ • Ensure the Organization ID in token matches request   │
└─────────────────────────────────────────────────────────┘
```

**Error States to Handle:**
- 400 Bad Request - Invalid parameters
- 401 Unauthorized - Invalid/expired JWT
- 403 Forbidden - Insufficient permissions
- 404 Not Found - Patient not found
- 408 Request Timeout - Query took too long
- 500 Internal Server Error
- Network errors (no connectivity)

---

## Configuration Constants

```typescript
// Environment URLs
const ENVIRONMENTS = {
  integration: 'https://api.integration.commonwellalliance.lkopera.com',
  production: 'https://api.commonwellalliance.lkopera.com'
};

// Base path
const DOCUMENT_REFERENCE_PATH = '/v2/R4/DocumentReference';

// Document Type Options (LOINC codes)
const DOCUMENT_TYPES = [
  { code: '34133-9', display: 'Summarization of Episode Note (CCD)' },
  { code: '18842-5', display: 'Discharge Summary' },
  { code: '11506-3', display: 'Progress Note' },
  { code: '34117-2', display: 'History and Physical' },
  { code: '11488-4', display: 'Consultation Note' },
  { code: '28570-0', display: 'Procedure Note' },
  { code: '57133-1', display: 'Referral Note' },
  { code: '57016-8', display: 'Privacy Policy Acknowledgement' }
];

// Content Type Options (MIME types)
const CONTENT_TYPES = [
  { value: 'application/xml', display: 'C-CDA XML Documents' },
  { value: 'application/pdf', display: 'PDF Documents' },
  { value: 'text/xml', display: 'XML (alternative)' },
  { value: 'text/plain', display: 'Plain Text' },
  { value: 'application/dicom', display: 'DICOM Images' },
  { value: 'image/jpeg', display: 'JPEG Images' },
  { value: 'image/png', display: 'PNG Images' },
  { value: 'image/tiff', display: 'TIFF Images' }
];

// Document Status Options
const DOCUMENT_STATUSES = [
  { value: '', display: 'All (no filter)' },
  { value: 'current', display: 'Current (recommended)' },
  { value: 'superseded', display: 'Superseded' },
  { value: 'entered-in-error', display: 'Entered in Error' }
];

// Quick Date Filters
const DATE_PRESETS = [
  { label: 'Last 24 Hours', days: 1 },
  { label: 'Last 7 Days', days: 7 },
  { label: 'Last 30 Days', days: 30 },
  { label: 'Last 90 Days', days: 90 },
  { label: 'Last Year', days: 365 }
];
```

---

## Query Builder Logic

```typescript
interface QueryParams {
  'patient.identifier': string;  // Required: {AAID}|{PatientID}
  status?: 'current' | 'superseded' | 'entered-in-error';
  date?: string[];  // Can have multiple: ge{date} and le{date}
  period?: string[];  // Can have multiple: ge{date} and le{date}
  contenttype?: string;
  documenttype?: string;
  'author.given'?: string;
  'author.family'?: string;
}

function buildQueryUrl(params: QueryParams, environment: string): string {
  const baseUrl = ENVIRONMENTS[environment];
  const url = new URL(`${baseUrl}${DOCUMENT_REFERENCE_PATH}`);
  
  // Add patient.identifier (required)
  url.searchParams.append('patient.identifier', params['patient.identifier']);
  
  // Add optional parameters
  if (params.status) {
    url.searchParams.append('status', params.status);
  }
  
  // Date filters (can have multiple)
  if (params.date) {
    params.date.forEach(d => url.searchParams.append('date', d));
  }
  
  // Period filters (can have multiple)
  if (params.period) {
    params.period.forEach(p => url.searchParams.append('period', p));
  }
  
  // Other optional filters
  if (params.contenttype) {
    url.searchParams.append('contenttype', params.contenttype);
  }
  if (params.documenttype) {
    url.searchParams.append('documenttype', params.documenttype);
  }
  if (params['author.given']) {
    url.searchParams.append('author.given', params['author.given']);
  }
  if (params['author.family']) {
    url.searchParams.append('author.family', params['author.family']);
  }
  
  return url.toString();
}
```

---

## API Call Implementation

```typescript
async function executeQuery(queryUrl: string, jwtToken: string): Promise<QueryResult> {
  const startTime = performance.now();
  
  try {
    const response = await fetch(queryUrl, {
      method: 'GET',
      headers: {
        'Authorization': `Bearer ${jwtToken}`,
        'Accept': 'application/fhir+json',
        'Content-Type': 'application/json'
      }
    });
    
    const endTime = performance.now();
    const responseTime = Math.round(endTime - startTime);
    
    const data = await response.json();
    
    return {
      success: response.ok,
      statusCode: response.status,
      statusText: response.statusText,
      responseTime,
      data,
      headers: Object.fromEntries(response.headers.entries())
    };
  } catch (error) {
    return {
      success: false,
      statusCode: 0,
      statusText: 'Network Error',
      responseTime: 0,
      error: error.message
    };
  }
}
```

---

## Local Storage Features

Save and restore:
- Last used environment
- Last used AAID (but NOT patient ID for privacy)
- Preferred date range preset
- Commonly used document type selections
- Query history (last 10 queries) - store URL pattern only, no tokens

---

## Additional Features (Nice to Have)

1. **Query History Sidebar**
   - Show last 10 queries (URL only, no tokens)
   - Click to re-run with current token
   - Clear history button

2. **Export Results**
   - Export documents list as CSV
   - Export raw JSON
   - Export as formatted report

3. **Diff View**
   - Compare results of two queries
   - Highlight new/removed documents

4. **Dark Mode Toggle**
   - Support dark/light themes

5. **Keyboard Shortcuts**
   - Ctrl+Enter: Execute query
   - Ctrl+K: Focus search/filter
   - Ctrl+Shift+C: Copy cURL

---

## Sample Test Data for Development

Use these sample values during development:

```typescript
const SAMPLE_DATA = {
  aaid: '2.16.840.1.113883.3.2611.9.99.101.1',
  patientId: 'patient-test-001',
  sampleJwt: 'eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...' // placeholder
};
```

---

## Responsive Design Requirements

- **Desktop (1200px+)**: Two-column layout as described
- **Tablet (768px-1199px)**: Stacked layout, form on top, results below
- **Mobile (< 768px)**: Single column, collapsible sections

---

## Accessibility Requirements

- All form inputs must have proper labels
- Error messages must be associated with inputs
- Color contrast must meet WCAG AA standards
- Keyboard navigation support
- Screen reader friendly

---

## Security Notes

- JWT tokens should never be logged to console in production
- Implement token masking by default
- Clear token from memory when component unmounts
- No token persistence in localStorage (security risk)
- Warn user if token is about to expire

---

This prompt provides complete specifications for building the CommonWell Document Query UI tool. The application should be intuitive for developers and QA engineers to test various query parameter combinations against the CommonWell FHIR API.
