# Documentation Workflow for Claude Code

This guide explains how to create and maintain documentation for Backstack.io using Mintlify, Storybook, and the docs submodule.

## Prerequisites

- Access to the backstack-io-v2 repository
- Node.js and npm installed
- Claude Code or another AI coding assistant

## Docs Structure

The `docs/` directory is a **separate git submodule** that points to https://github.com/jkelly-backstack/docs.git.

Key files and directories:
- `docs.json` - Navigation configuration for Mintlify
- `*.mdx` - Root-level documentation pages (index, quickstart, etc.)
- `essentials/` - Customization and writing guides
- `ai-tools/` - AI tool integration documentation
- `api-reference/` - API documentation
- `images/` - Image assets
- `logo/` - Logo files

## Creating Documentation

### 1. Running Storybook (for screenshots and component development)

Storybook is recommended for capturing consistent screenshots and developing UI components:

```bash
cd interfaces/web
npm run storybook
```

This starts Storybook on http://localhost:6006/. Browse to the component story you need, then capture screenshots.

**Note:** Storybook was fixed in BS2-227 by converting `.storybook/main.ts` to `.storybook/main.cjs` to resolve ESM compatibility issues.

### 2. Creating MDX Files

Create a new `.mdx` file in the appropriate location. MDX files require YAML frontmatter:

```mdx
---
title: "Page Title"
description: "Brief description for SEO and navigation"
icon: "icon-name"  # Optional - Font Awesome icon name
---

## Your Content Here

Write your documentation using Markdown with MDX extensions.
```

**Frontmatter fields:**
- `title` (required): Page title shown in navigation and h1
- `description` (required): Brief summary for SEO and preview
- `icon` (optional): Font Awesome icon name (e.g., "folder", "wrench", "users")

**Location guidelines:**
- Root level: Main/featured pages (quickstart, workspaces, etc.)
- `essentials/`: Platform guides and how-tos
- `ai-tools/`: AI tool integration guides
- `api-reference/`: API documentation

### 3. Mintlify Components

Mintlify provides custom MDX components. Common ones:

#### Cards
```mdx
<Card title="Card Title" icon="icon-name" href="/path">
  Card description text
</Card>

<CardGroup cols={2}>
  <Card title="First" icon="icon1">Content</Card>
  <Card title="Second" icon="icon2">Content</Card>
</CardGroup>
```

#### Callouts
```mdx
<Tip>Helpful tip for users</Tip>
<Note>Important information</Note>
<Warning>Warning about potential issues</Warning>
```

#### Accordions
```mdx
<AccordionGroup>
  <Accordion icon="icon-name" title="Section Title">
    Content inside accordion
  </Accordion>
</AccordionGroup>
```

#### Code Blocks
```mdx
```bash
npm install package-name
\```

```typescript
const example: string = "code";
\```
```

See `docs/quickstart.mdx` and `docs/workspaces.mdx` for examples.

### 4. Adding Images

If you need images:

1. Save optimized images to `docs/images/[category]/`
2. Reference using relative paths:
   ```mdx
   ![Alt text](/images/category/filename.png)
   ```

**Best practice:** Use images sparingly. Text-based documentation is easier to maintain.

### 5. Screenshot Best Practices

**Liberal use encouraged:** Add screenshots liberally throughout documentation. Err on the side of too many rather than too few - visual aids significantly improve comprehension and reduce support burden.

#### When to Add Screenshots

Add screenshots for:
- **Every major step** in a workflow or procedure
- **Complex UI sections** - capture multiple angles or states if needed
- **Before/after states** - show the impact of actions
- **Complete flows** - document entire sequences, not just individual actions
- **Feature highlights** - showcase key capabilities and outcomes
- **Error states** - help users recognize and resolve issues

#### Screenshot Density Guidelines

- **Minimum**: At least one screenshot per major feature section
- **Workflows**: One screenshot per significant step
- **Tutorials**: Screenshot after each action that changes the UI
- **Reference**: Screenshots of all major UI screens/components

#### Quality Standards

All screenshots must pass the validation checklist in the "Screenshot Validation Checklist" section below before inclusion.

### 6. Using Storybook for Screenshots

**Recommended approach:** Use Storybook to capture screenshots with controlled, realistic data. This ensures consistency and allows easy updates when UI changes.

#### Benefits of Storybook for Screenshots

- **Controlled environment**: Set exact UI states without test data
- **Custom data injection**: Use realistic examples instead of placeholder text
- **Repeatable**: Easily recapture after UI changes
- **Isolated components**: Focus on specific features without distractions

#### Storybook Screenshot Workflow

1. **Start Storybook**:
   ```bash
   cd interfaces/web
   npm run storybook
   ```
   Opens at http://localhost:6006/

2. **Navigate to component story**: Browse to the specific component and story you need

3. **Inject realistic data**: Use Storybook controls to set up the UI state:
   - Fill form fields with example data
   - Set component props to show desired state
   - Toggle features on/off as needed
   - Use the Controls addon to adjust values

4. **Capture screenshot**:
   - **Manual**: Use browser screenshot tools or OS screenshot utilities
   - **Playwright**: Write a test to automate screenshot capture (see workspace documentation example)
   - Ensure viewport size is consistent across all screenshots

5. **Optimize the image**:
   - Crop to relevant area (remove unnecessary browser chrome)
   - Resize if needed (keep readable, avoid huge files)
   - Save as PNG for UI screenshots
   - Optimize file size if large

6. **Save to docs**:
   ```bash
   # Save to appropriate subdirectory
   mv screenshot.png docs/images/[category]/descriptive-name.png
   ```

#### Example: Workspace Documentation

The workspace documentation demonstrates effective use of Storybook for screenshots:
- Custom workspace data injected via Storybook controls
- Multiple screenshots showing different workspace states
- Consistent viewport and styling across all images
- Realistic example data (workspace names, member counts, etc.)

See `docs/workspaces.mdx` and `docs/images/workspaces/` for reference.

#### Playwright for Automated Screenshots

For repeatable screenshot capture, use Playwright with Storybook:

```typescript
// Example: Capture screenshot of Storybook story
await page.goto('http://localhost:6006/iframe.html?id=workspace--default');
await page.screenshot({ path: 'docs/images/workspaces/workspace-list.png' });
```

This allows you to:
- Automate screenshot capture after UI changes
- Ensure consistent viewport and state
- Batch capture multiple screenshots
- Version control the screenshot scripts

See workspace documentation tests for working examples.

### 8. Updating Navigation

After creating a new page, add it to `docs/docs.json`:

```json
{
  "navigation": {
    "tabs": [
      {
        "tab": "Guides",
        "groups": [
          {
            "group": "Getting started",
            "pages": [
              "index",
              "quickstart",
              "development",
              "workspaces"  // <-- New page added here
            ]
          }
        ]
      }
    ]
  }
}
```

**Navigation structure:**
- `tabs` - Top-level navigation tabs
- `groups` - Sections within a tab
- `pages` - Array of page paths (no file extension, no leading slash)

**Path format:**
- Root files: `"filename"` (e.g., `"workspaces"` for `workspaces.mdx`)
- Nested files: `"directory/filename"` (e.g., `"ai-tools/claude-code"`)

## Git Submodule Workflow

The docs directory is a **separate git repository**. Changes must be committed and pushed within the submodule.

### Committing Documentation Changes

```bash
# Navigate to docs directory
cd docs/

# Check status
git status

# Stage changes
git add workspaces.mdx docs.json

# Commit with descriptive message
git commit -m "docs: add workspace management documentation

Documented workspace creation, management, and best practices.
Includes information on members, tools, and permissions.

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"

# Push to remote
git push origin main
```

### Important Notes

1. **Separate repository:** The docs/ subdirectory has its own git history
2. **Remote URL:** https://github.com/jkelly-backstack/docs.git
3. **Branch:** Commit directly to `main` branch
4. **Auto-deploy:** Changes pushed to docs repo automatically deploy to Mintlify

## Writing Style Guide

Follow these conventions for consistency:

### Voice and Tone
- Use second-person ("you") for instructions
- Be concise and clear
- Focus on user goals and outcomes
- **Maintain professional tone**: Keep documentation professional and business-focused

### Professional Standards

**CRITICAL: No Emojis or Emotes in Documentation**

Documentation must maintain a professional tone. Emojis and emotes are prohibited.

❌ **Prohibited:**
- Emojis in text (🚀, ✨, 👍, 💡, etc.)
- Emotes in headings or body text
- Decorative Unicode characters used as emojis
- Emoji-style usage in examples or code comments

✅ **Allowed:**
- Font Awesome icons in frontmatter (`icon: "wrench"`)
- Mintlify component icons (`<Card icon="folder">`)
- SVG icons in UI components
- Standard Markdown formatting (✅ ❌ in checklists is acceptable as semantic indicators)

**Rationale:** Professional documentation prioritizes clarity and accessibility. Emojis can:
- Appear inconsistent across platforms and devices
- Create accessibility issues for screen readers
- Detract from professional presentation
- Age poorly as emoji trends change

Use clear, descriptive language instead of emojis to convey emphasis or importance.

### Structure
- Start with a brief overview
- Include prerequisites if needed
- Use numbered steps for procedures
- Add examples and code snippets
- End with "Next steps" or related links

### Formatting
- Use sentence case for headings
- Keep paragraphs short (2-4 sentences)
- Use lists for multiple items
- Add code blocks with language tags
- Include alt text for images

## Screenshot Validation Checklist

**CRITICAL: Always verify screenshots before including in documentation.**

Before adding any screenshot to documentation, check for:

### Visual Quality
- ✅ No typos or spelling errors visible in UI text
- ✅ No incorrect UI states or placeholder data
- ✅ No internal/debug information visible (console logs, error messages, etc.)
- ✅ Screenshots show realistic, production-ready states
- ✅ Images are clear, properly cropped, and at appropriate resolution
- ✅ No browser developer tools or debug panels visible
- ✅ Consistent browser window size across all screenshots

### Content Accuracy
- ✅ All text in screenshot is accurate and current
- ✅ No error states or "failed to load" messages (unless documenting errors)
- ✅ Example data looks realistic and professional
- ✅ No sensitive information visible (API keys, tokens, real user data)
- ✅ UI elements match current production design

### Documentation Alignment
- ✅ Screenshot illustrates the exact step being documented
- ✅ Visual state matches the documentation narrative
- ✅ Screenshot filename is descriptive and follows naming conventions
- ✅ Alt text accurately describes what the screenshot shows

**If a screenshot contains any errors or issues, it MUST be retaken before being included in documentation.**

## User-Focused Documentation Principles

**CRITICAL: Documentation must focus on platform outcomes and user workflows, NOT technical implementation.**

### Focus on WHAT, Not HOW

Documentation should explain what users can accomplish and how to use the system, not how the system works internally.

**Bad (Technical focus):**
> "The system uses AWS Lambda functions to deploy your NPM packages. Each package is built using CodeBuild and stored in an S3 bucket with ARM64 architecture."

**Good (User outcome focus):**
> "Install NPM packages that run automatically when needed. The platform handles all deployment and scaling for you."

### Technology Disclosure Policy

**NEVER disclose underlying technologies in user-facing documentation:**

❌ **Prohibited:**
- Specific cloud providers (AWS, Azure, GCP)
- Database technologies (PostgreSQL, Redis, DynamoDB)
- Infrastructure services (Lambda, CodeBuild, S3, EC2)
- Programming languages or frameworks used internally
- Architecture patterns or technical implementation details
- Build systems or CI/CD tools

✅ **Allowed:**
- User-facing protocols and standards (OAuth 2.1, MCP, HTTP)
- Technologies users directly interact with (npm packages, git)
- Client-side tools users install (Claude Desktop, VS Code)
- Public APIs and endpoints users configure

### Outcome-Oriented Language

Use language that emphasizes what users achieve, not how the system works:

| ❌ Technical | ✅ User-Focused |
|-------------|-----------------|
| "Lambda functions process requests" | "Your tools run automatically when called" |
| "CodeBuild compiles the package" | "The service installs and prepares your package" |
| "Stored in S3 with ARM64 architecture" | "Deployed and ready to use" |
| "PostgreSQL database stores data" | "Your data is securely stored" |
| "Redis cache improves performance" | "Fast response times for your requests" |

### Guide Workflows, Not Architecture

Structure documentation around user tasks and workflows:

**Bad (Architecture focus):**
> "The MCP server implements OAuth 2.1 discovery via the /.well-known endpoint. Client requests are authenticated using JWT tokens and routed through the API gateway to Lambda functions."

**Good (Workflow focus):**
> "Connect your AI client to Backstack:
> 1. Copy your MCP endpoint URL from the homepage
> 2. Add it to your AI client's configuration
> 3. Authorize access when prompted
> 4. Start using your organization's tools"

### Examples and Best Practices

When writing documentation:

1. **Start with the user's goal**: "Create a workspace to organize your tools"
2. **Describe what happens, not how**: "The service will process your package" (not "Lambda will build it")
3. **Use active voice**: "Install packages from npm" (not "Packages are installed")
4. **Abstract implementation**: "Automatically scales" (not "Auto-scaling groups manage EC2 instances")
5. **Focus on control**: "Configure environment variables" (not "Lambda environment configuration")

### Technical Details Section Exception

If technical implementation details are necessary (e.g., for developers building integrations), place them in a clearly marked "Technical Details" or "For Developers" section separate from user-focused content.

**Example:**
```mdx
## Using Organization Services

[User-focused content here...]

## Technical Details

For developers interested in the implementation:
- OAuth Discovery: `/.well-known/oauth-authorization-server`
- Source Code: `services/mcp-server-service/`
```

Even in technical sections, avoid disclosing infrastructure specifics (AWS services, database choices, etc.) unless absolutely necessary for integration purposes.

## Examples

### Good Documentation Structure

```mdx
---
title: "Feature Name"
description: "Brief description of the feature"
icon: "feature-icon"
---

## Overview

Brief introduction explaining what the feature does and why it's useful.

## Getting Started

### Prerequisites

- Requirement 1
- Requirement 2

### Setup Steps

1. First step with clear instructions
2. Second step
3. Third step

<Tip>Helpful tip related to the steps above</Tip>

## Advanced Usage

More detailed information for power users.

## Best Practices

<CardGroup cols={2}>
  <Card title="Practice 1" icon="icon1">
    Description
  </Card>
  <Card title="Practice 2" icon="icon2">
    Description
  </Card>
</CardGroup>

## Next Steps

<Card title="Related Feature" icon="icon" href="/related">
  Learn about related functionality
</Card>
```

## Reference Files

- **Navigation:** See `docs.json` for structure
- **MDX Examples:** See `quickstart.mdx`, `workspaces.mdx`
- **Mintlify Components:** See `essentials/*.mdx` files
- **Storybook Stories:** See `interfaces/web/src/**/*.stories.ts`

## Troubleshooting

### Storybook won't start
- Ensure you're using `main.cjs` (not `main.ts`) in `.storybook/`
- This was fixed in commit d812c767 (BS2-227)

### Navigation not showing new page
- Check docs.json syntax (valid JSON)
- Verify page path matches file location
- Restart Mintlify preview if running locally

### Images not displaying
- Use relative paths starting with `/images/`
- Verify image files are committed to docs repo
- Check file extensions match (case-sensitive)

## Questions?

Refer to:
- Mintlify documentation: https://mintlify.com/docs
- Existing docs files for examples
- This workflow guide for the submodule git process
