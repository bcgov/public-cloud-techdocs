# GitHub Copilot Instructions - BC Government Public Cloud Technical Documentation

This repository contains technical documentation for BC Government's public cloud services. All content must follow BC Government web content standards for accessibility, plain language, and readability.

## Core Writing Standards

### 1. Use Active Voice
**Always write in active voice (subject-verb-object), not passive voice.**

✅ Good: "Section B requires you to complete the form"  
❌ Bad: "As required under Section B" (passive)

✅ Good: "You can order copies"  
❌ Bad: "Copies can be ordered"

### 2. Use Present Tense
**Write in present tense to keep sentences short and engaging.**

✅ Good: "The system validates your input"  
❌ Bad: "The system will validate your input"

### 3. Write Short Sentences
**Aim for a maximum of 15-20 words per sentence. Break long sentences into shorter ones.**

- Count the words in each sentence
- If a sentence exceeds 20 words, split it into multiple sentences
- Each sentence should cover a single idea or action

✅ Good: "You can deploy applications to Azure. The platform provides automated scaling."  
❌ Bad: "You can deploy applications to Azure and the platform provides automated scaling which helps manage traffic spikes."

### 4. Use Plain Language
**Write at a Grade 8 reading level or lower. Use everyday words.**

Replace complex words with simpler alternatives:
- "approximately" → "about"
- "utilize" → "use"
- "assist" → "help"
- "obtain" → "get"
- "prior to" → "before"
- "in order to" → "to"
- "submit an application" → "apply"
- "establish" → "create" or "set up"

### 5. Use Sentence Case for Headings and Titles
**Only capitalize the first word and proper nouns in headings, titles, and navigation items.**

✅ Good: "Deploy to the Azure landing zone"  
❌ Bad: "Deploy to the Azure Landing Zone"  
❌ Bad: "Deploy To The Azure Landing Zone"

**Exception:** Proper nouns are always capitalized (AWS, Azure, British Columbia, Rocket.Chat, etc.)

✅ Good: "Get started with Azure"  
✅ Good: "AWS security and compliance guardrails"  
✅ Good: "Understanding your AWS bill"

## Grammar and Style

### Action Verbs
Use clear action verbs to give direction:
- "Find a hospital near you"
- "Download the template"
- "Configure your settings"

### Avoid '-ing' Verbs in Instructions
Be clear when using verbs ending in '-ing':

✅ Good: "When you pay for the service"  
❌ Bad: "When paying for the service"

### Remove Unnecessary Words
Cut adjectives and adverbs that don't improve understanding:

✅ Good: "Complete the required information"  
❌ Bad: "Complete all of the required information"

✅ Good: "Surveys assess user needs"  
❌ Bad: "Surveys are used to assess user needs"

### Use Positive Form
Tell users what they can or must do, not what they cannot:

✅ Good: "Save your work regularly"  
❌ Bad: "Don't forget to save your work"

**Exception:** Use negative form for serious consequences:
- "Do not try to locate the source of carbon monoxide. Leave your home immediately."

### Contractions
Use positive contractions (it's, we're, you're) for readability.

❌ Avoid negative contractions (don't, can't, shouldn't) as they can be misread.

✅ Good: "You are not able to access this feature"  
❌ Bad: "You can't access this feature"

### Pronouns
Use gender-neutral pronouns:
- Use "you" when addressing the reader
- Use "they/them/their" for third person
- Avoid "he/she" constructions

✅ Good: "Applicants can see their results"  
❌ Bad: "The applicant can see his/her results"

## Formatting Standards

### Capitalization Rules

#### Do NOT Use All Caps
Never use ALL CAPS unless it's an abbreviation or acronym (AWS, API, URL).

❌ Bad: "IMPORTANT: Read this section"  
✅ Good: "Important: Read this section"

#### Headings
Use sentence case - capitalize only the first word and proper nouns:

```markdown
# Get started with Azure
## Deploy to the landing zone
### Configure your environment
```

#### Lists
Always capitalize the first word of each list item.

#### Proper Nouns
Always capitalize:
- Service names: Azure, AWS, Rocket.Chat
- Product names: Landing Zone Accelerator, Terraform
- Geographic names: British Columbia, Canada
- Specific entities: Ministry of Finance, BC Government
- Indigenous terms: First Nations, Indigenous, Inuit, Métis

#### Common Nouns
Do NOT capitalize common nouns:
- "the ministry" (unless part of full name: "Ministry of Finance")
- "the government"
- "directors and executive directors"

### Spelling
Use **Canadian spelling**:
- "adviser" (not advisor)
- "defence" (not defense)
- "licence" (noun), "license" (verb)
- "offence" (not offense)
- "practice" (noun), "practise" (verb)

### Paragraphs
- Keep paragraphs to 5 sentences or less
- One topic per paragraph
- Start new paragraph for new topics

## Accessibility Requirements (WCAG 2.0)

### Links
Use descriptive link text that makes sense out of context:

✅ Good: "Read the [deployment guide](link)"  
❌ Bad: "Click [here](link) for the deployment guide"

### Images
- Always provide alt text for images
- Make alt text descriptive and meaningful
- Describe the content and function of the image

### Headings
- Use headings in proper hierarchical order (H1 → H2 → H3)
- Don't skip heading levels
- Make headings descriptive and meaningful

### Color and Contrast
- Don't rely on color alone to convey information
- Ensure sufficient color contrast

### Lists
Use lists to break up content and improve scannability:
- Bulleted lists for unordered items
- Numbered lists for sequential steps
- Keep list items parallel in structure

## Technical Documentation Specific

### Code Blocks
Always specify the language for syntax highlighting:

````markdown
```yaml
key: value
```

```bash
az login
```

```python
def example():
    pass
```
````

### Examples and Instructions
Provide complete, working examples:
- Include all necessary context
- Test code examples before including them
- Add explanatory comments where helpful

### File References
When mentioning files, use proper markdown links:
- `[file.md](path/to/file.md)` for internal links
- Use relative paths for internal documentation

### Admonitions (Call-outs)
Use MkDocs Material admonitions for important information:

```markdown
!!! note "Note"
    This is important context

!!! warning "Warning"
    This could cause issues

!!! tip "Tip"
    Here's a helpful suggestion
```

Available types: note, info, tip, warning, danger, example, quote

## Content Organization

### Structure
Every page should follow this structure:
1. H1 title (sentence case)
2. Last updated timestamp: `Last updated: **{{ git_revision_date_localized }}**`
3. Brief introduction paragraph
4. Horizontal rule (`---`)
5. Main content with H2 sections
6. Related pages section at end

### Headings Hierarchy
- **H1 (`#`)**: Page title only (one per page)
- **H2 (`##`)**: Major sections
- **H3 (`###`)**: Subsections
- **H4 (`####`)**: Only if absolutely necessary
- Avoid H5 and below

## Quality Checklist

Before submitting documentation, verify:

- [ ] All sentences use active voice
- [ ] All sentences are in present tense
- [ ] No sentence exceeds 20 words
- [ ] Headings and titles use sentence case
- [ ] Only first word and proper nouns are capitalized
- [ ] Plain language used (Grade 8 reading level)
- [ ] Complex terms are explained or have examples
- [ ] Canadian spelling used throughout
- [ ] No ALL CAPS (except abbreviations)
- [ ] Links have descriptive text
- [ ] Images have alt text
- [ ] Headings are in proper hierarchy
- [ ] Code blocks have language identifiers
- [ ] Paragraphs are 5 sentences or less
- [ ] Contractions are positive, not negative
- [ ] Gender-neutral pronouns used

## References

- [BC Government Writing Guide - Grammar, Spelling and Tone](https://www2.gov.bc.ca/gov/content/governments/services-for-government/service-experience-digital-delivery/web-content-development-guides/web-style-guide/writing-guide/grammar-spelling-tone)
- [BC Government Writing Guide - Writing Web Content](https://www2.gov.bc.ca/gov/content/governments/services-for-government/service-experience-digital-delivery/web-content-development-guides/web-style-guide/writing-guide/writing-web-content)
- [BC Government Writing Guide - Capitalization](https://www2.gov.bc.ca/gov/content/governments/services-for-government/service-experience-digital-delivery/web-content-development-guides/web-style-guide/writing-guide/capitalization)
- [Web Content Accessibility Guidelines (WCAG) 2.0](https://www.w3.org/WAI/WCAG20/quickref/)

---

**When generating or editing documentation content, always prioritize:**
1. Clarity and simplicity over technical sophistication
2. Active voice over passive voice
3. Short sentences over long, complex ones
4. Plain language over jargon
5. Accessibility for all users
6. Sentence case for all headings and titles
