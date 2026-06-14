# Sara Tarek Ahmed — CV Publishing Workflow

This repository contains a structured, maintainable CV publishing system built around a single source of truth.

The workflow begins with an editable Excel master dataset, exports that data to JSON, renders a responsive web CV through GitHub Pages, and supports generation of an ATS-friendly Word document.

## Repository contents

```text
.
├── Sara_Tarek_Ahmed_CV_Master_Dataset_with_JSON_Exporter.xlsx
├── SaraExportedDataCV.json
├── Sara_Tarek_Ahmed_ATS_CV.docx
├── Sara_Tarek_Ahmed_CV_ATS_Button_Preview.png
├── Index.html
└── README.md
```

### File roles

| File | Purpose |
|---|---|
| `Sara_Tarek_Ahmed_CV_Master_Dataset_with_JSON_Exporter.xlsx` | Human-editable source of truth for all CV data, privacy settings, output selections, verification statuses, and editorial notes. |
| `SaraExportedDataCV.json` | Machine-readable dataset generated from the Excel workbook and consumed by the website. |
| `Index.html` | Responsive CV webpage. It loads `SaraExportedDataCV.json` at runtime and dynamically renders the content. |
| `Sara_Tarek_Ahmed_ATS_CV.docx` | Current ATS-friendly Word CV generated from the structured data. |
| `Sara_Tarek_Ahmed_CV_ATS_Button_Preview.png` | Preview image showing the webpage and ATS DOCX export control. |
| `README.md` | Project documentation and maintenance workflow. |

## Architecture

```text
Excel master dataset
        │
        │  Office Script export
        ▼
SaraExportedDataCV.json
        │
        ├──────────────► Index.html
        │                 Responsive GitHub Pages CV
        │
        └──────────────► ATS DOCX exporter
                          Professional one-column Word CV
```

The Excel workbook is the authoritative source. The JSON, webpage, and Word document are generated outputs and should not be edited independently unless the change is intentionally temporary.

## Workflow

### 1. Update the master dataset

Open:

```text
Sara_Tarek_Ahmed_CV_Master_Dataset_with_JSON_Exporter.xlsx
```

Edit the relevant structured table rather than manually changing the JSON, HTML, or DOCX.

The workbook separates CV content into sections such as:

- profile and contact details;
- education;
- employment;
- publications;
- intellectual property;
- committee service;
- professional memberships;
- community projects;
- professional development;
- unresolved issues and editorial decisions.

Each record includes stable identifiers, source references, verification status, privacy controls, and output-specific inclusion flags.

### 2. Review privacy and output settings

Before publishing, confirm the following fields for each record:

- `Include Web`
- `Include Employer CV`
- `Include Academic CV`
- `Privacy`
- `Status`

Public web content should only contain information approved for public display.

Private addresses, private contact details, unresolved claims, and incomplete records should remain excluded until reviewed.

### 3. Resolve outstanding issues

Use the workbook's **Issues & Decisions** sheet to document ambiguous dates, wording corrections, missing information, and final decisions.

When an issue is resolved:

1. update the relevant source record;
2. enter the final decision;
3. change the issue status to `Resolved`;
4. change the related record status to `Verified`.

This maintains an auditable history and prevents silent corrections.

### 4. Export the JSON dataset

Use the workbook's **JSON Export** sheet and the configured Microsoft Office Script.

The exporter:

- reads the structured Excel tables;
- applies the configured section mappings;
- preserves the expected JSON schema;
- converts configured empty fields to `null`;
- writes the generated JSON to the workbook output sheet;
- returns the complete JSON string.

Save or replace the exported file as:

```text
SaraExportedDataCV.json
```

Keep the filename unchanged because `Index.html` loads it directly.

### 5. Validate the webpage locally

The webpage requests the JSON file at runtime:

```text
Index.html
SaraExportedDataCV.json
```

Both files must remain in the same directory.

Because browsers can restrict local `fetch()` requests from `file://` pages, test the repository through a local web server.

Using Python:

```bash
python -m http.server 8000
```

Then open:

```text
http://localhost:8000/Index.html
```

Check:

- name, title, location, and contact information;
- section ordering;
- publication links;
- inclusion and privacy filtering;
- desktop and mobile layouts;
- print styling;
- ATS DOCX generation;
- browser console errors.

For automatic loading at the root GitHub Pages URL, using lowercase `index.html` is recommended. If the file remains `Index.html`, link to that exact case-sensitive path.

### 6. Generate the ATS DOCX

Open the webpage and select **Download ATS DOCX**.

The generated document is built directly from the active JSON dataset rather than converted from the visual webpage.

This approach produces a cleaner ATS-oriented document with:

- one-column reading order;
- standard headings;
- ordinary paragraphs and bullet lists;
- no sidebars, text boxes, icons, or decorative tables;
- selectable text;
- conventional fonts;
- visible DOI text and hyperlinks;
- employer-specific inclusion settings;
- contact information in the main document body.

After generation:

1. open the DOCX in Microsoft Word;
2. review page breaks, dates, contact details, and section selection;
3. run spelling and grammar checks;
4. confirm that hyperlinks work;
5. save the approved file as:

```text
Sara_Tarek_Ahmed_ATS_CV.docx
```

### 7. Commit the updated outputs

After reviewing the website and DOCX, commit the changed files:

```bash
git add   Sara_Tarek_Ahmed_CV_Master_Dataset_with_JSON_Exporter.xlsx   SaraExportedDataCV.json   Sara_Tarek_Ahmed_ATS_CV.docx   Index.html   README.md

git commit -m "Update CV data and generated outputs"
git push
```

The preview image only needs to be replaced when the interface or export controls change.

## GitHub Pages deployment

In the GitHub repository:

1. open **Settings**;
2. select **Pages**;
3. choose **Deploy from a branch**;
4. select the publishing branch, usually `main`;
5. select the repository root;
6. save the configuration.

The published site must contain `Index.html` and `SaraExportedDataCV.json` in the same served directory.

After deployment, verify that:

- the JSON request returns HTTP 200;
- the page renders without a data-loading error;
- DOI and email links open correctly;
- the DOCX export works in a modern browser;
- no private data appears in the public page source or generated document.

## Data maintenance rules

### Treat the workbook as the source of truth

Do not make permanent content corrections only in:

- `SaraExportedDataCV.json`;
- `Index.html`;
- `Sara_Tarek_Ahmed_ATS_CV.docx`.

Those changes will be overwritten the next time outputs are regenerated.

### Preserve record IDs

Existing IDs should remain stable even when records are reordered or temporarily excluded.

Examples:

```text
EDU001
EMP003
PUB009
COM002
PD046
```

Use the next sequential ID when adding a new record.

### Preserve raw and cleaned values

Where the workbook contains both raw and cleaned values:

- retain the original source wording;
- place corrected or presentation-ready wording in the clean field;
- record uncertain transformations in `Notes`;
- keep the status as `Needs review` until approved.

### Use ISO dates

Use the most precise supported ISO representation:

```text
YYYY
YYYY-MM
YYYY-MM-DD
```

Examples:

```text
2026
2026-03
2026-03-13
```

Retain the original date in the corresponding raw-date field.

### Do not publish empty placeholders

Profile fields such as summary, expertise, languages, and academic links should only render when they contain approved values.

The website and document generator should omit empty or excluded fields rather than showing blank headings.

## Updating the website design

The website separates data from presentation:

- the JSON file contains CV content;
- Handlebars templates define rendered markup;
- CSS controls the visual design;
- JavaScript prepares, filters, sorts, and formats the data.

Content changes should normally require only:

1. editing the workbook;
2. exporting a new JSON file;
3. replacing `SaraExportedDataCV.json`.

Changes to `Index.html` are only required when modifying layout, styling, rendering logic, or export behaviour.

## Troubleshooting

### The page shows a JSON loading error

Confirm that:

- the filename is exactly `SaraExportedDataCV.json`;
- the JSON file is in the same directory as `Index.html`;
- the JSON is valid;
- the page is being served over HTTP or HTTPS;
- the filename case matches exactly;
- GitHub Pages has finished deploying the latest commit.

### The webpage still shows old information

The browser or GitHub Pages CDN may be serving a cached file.

Try:

- a hard refresh;
- opening the page in a private window;
- confirming the latest JSON is present in the repository;
- checking the JSON response directly in browser developer tools.

### The DOCX button does not work

Check:

- the browser console for JavaScript errors;
- whether the DOCX-generation library loaded successfully;
- whether the JSON completed loading before export;
- whether downloads are blocked by the browser;
- whether the active dataset contains employer-CV records.

### A record is missing from the website

In the workbook, verify:

```text
Include Web = Yes
Privacy = Public
```

Also confirm that the record contains a usable display value and that the section is enabled in the page's rendering logic.

### A record is missing from the ATS DOCX

Verify:

```text
Include Employer CV = Yes
```

Then regenerate the JSON and create a new Word document from the webpage.

## Release checklist

Before publishing a CV update:

- [ ] Master workbook updated
- [ ] Ambiguous records reviewed
- [ ] Privacy settings confirmed
- [ ] Web inclusion settings confirmed
- [ ] Employer-CV inclusion settings confirmed
- [ ] JSON regenerated
- [ ] JSON validation passed
- [ ] Website tested locally
- [ ] GitHub Pages deployment checked
- [ ] ATS DOCX regenerated
- [ ] DOCX opened and reviewed in Word
- [ ] Contact details confirmed
- [ ] Publication links tested
- [ ] Changes committed and pushed

## Recommended versioning

Use clear commit messages:

```text
Update academic appointment
Add 2026 publication
Correct professional development dates
Refresh public CV and ATS document
Update webpage layout
```

For major approved releases, create Git tags:

```bash
git tag -a cv-2026-06 -m "CV release June 2026"
git push origin cv-2026-06
```

This provides a recoverable history of published CV versions.

## Privacy notice

This repository may contain personal and professional information intended for public distribution.

Before making the repository public, review:

- phone numbers;
- email addresses;
- residential addresses;
- identification or registration numbers;
- unpublished research;
- draft achievements;
- unresolved or unverified claims;
- metadata contained in Excel and Word files.

Only publish information that has been explicitly approved for public access.
