# Gemini Project Context: babanin.github.io

This file captures the architectural decisions, styling standards, and technical context established for this Hugo-based blog.

## Tech Stack & Theme
- **Framework:** [Hugo](https://gohugo.io/)
- **Theme:** [Archie](https://github.com/athul/archie) (Customized)
- **Primary Font:** Neuton (serif) at 20px for body text.

## Styling Standards
To maintain visual consistency across all posts, adhere to these CSS standards:

### Typography & Code
- **Inline Code (`code`):** Set to `0.85em` to integrate better with the 20px body text.
- **Code Blocks (`pre code`, `.highlight`):** Set to `0.9em` for readability.
- **Syntax Highlighting:** Uses the `trac` style (defined in `config.toml`). 
- **Language Labels:** TypeScript/TS blocks must have the `ts` label (defined in `main.css` and `dark.css` using `[class*="language-ts"]`).

### Images & Figures
- **Scaling:** Images are restricted to `70%` max-width and `350px` max-height to ensure they don't overwhelm the text.
- **Alignment:** Images are centered with a `1em` bottom margin for spacing.

### Layout & Navigation
- **Post Headers:** Use `flexbox` with `justify-content: space-between` and `align-items: baseline` to keep the Title and "Published on [Date]" on the same horizontal line.
- **Tags:** 
    - Always left-aligned below the title.
    - prefixed with `#` via CSS pseudo-elements.
    - `white-space: nowrap` to prevent multi-word tags (e.g., "system design") from splitting.
    - `display: inline-block` with `margin-right: 1em` for clean wrapping on the "All tags" page.
- **Article Lists:** The `/posts` and tag pages use a clean list without bullets, using flexbox to separate titles and dates with a dotted bottom border.

## Technical Context: Ingress-NGINX
The blog contains critical information regarding `ingress-nginx`. Note these verified facts:
- **Global Rate Limiting:** This feature was explicitly removed from the community controller (PR #11851) to reduce complexity.
- **Retirement:** The community-maintained `ingress-nginx` controller is scheduled for **retirement in March 2026**.
- **Migration Path:** The Kubernetes project recommends migrating to the **Gateway API**.

## Maintenance Workflows
- **GitHub Deployments:** Use the `gh` CLI to manage deployments. To delete deployments, they must first be set to `inactive`.
- **Hugo Server:** For local preview, use `hugo server -D`.
