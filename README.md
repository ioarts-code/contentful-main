# IOARTS Contentful

This repository contains a Next.js blog and content site backed by the Contentful GraphQL API. It renders a post index and individual post pages, with support for Contentful draft preview and on-demand revalidation.

## Features

- Next.js App Router pages for the post list and `/posts/[slug]`
- Content fetched from Contentful's GraphQL Content API
- Draft mode for previewing unpublished entries
- Tag-based on-demand revalidation for published post content
- Tailwind CSS styling and shared header and footer components

## Technology

- Next.js 15 and React 18
- TypeScript and Tailwind CSS 4
- Contentful GraphQL API
- pnpm (the repository includes `pnpm-lock.yaml`)

## Getting started

### Requirements

- Node.js supported by Next.js 15
- pnpm
- A Contentful space with API credentials

### Install and run

```bash
pnpm install
pnpm dev
```

Open [http://localhost:3000](http://localhost:3000).

### Environment variables

Create a `.env.local` file in the project root. Keep all Contentful credentials server-side; do not use a `NEXT_PUBLIC_` prefix or commit secrets.

```dotenv
CONTENTFUL_SPACE_ID=your-space-id
CONTENTFUL_ACCESS_TOKEN=your-delivery-api-token
CONTENTFUL_PREVIEW_ACCESS_TOKEN=your-preview-api-token
CONTENTFUL_PREVIEW_SECRET=use-a-long-random-secret
CONTENTFUL_REVALIDATE_SECRET=use-a-different-long-random-secret

# Only needed when importing the content model with `pnpm setup`.
CONTENTFUL_MANAGEMENT_TOKEN=your-content-management-token
```

`CONTENTFUL_SPACE_ID` and `CONTENTFUL_ACCESS_TOKEN` are used for published content. Preview requires the preview token and `CONTENTFUL_PREVIEW_SECRET`; the revalidation endpoint checks `CONTENTFUL_REVALIDATE_SECRET`. The Content Management token is only needed by the setup/import script and should be removed from the environment when it is no longer needed.

## Contentful model

See [`CONTENTFUL_SETUP.md`](CONTENTFUL_SETUP.md) for the existing setup instructions. Before importing or creating a model, verify that its API identifiers and fields match the queries in [`lib/api.ts`](lib/api.ts). There is currently a mismatch: the queries request `titleCollection` and fields such as `image`, `price`, and `description`, while the setup guide and `lib/export.json` describe a `blogPost`/`post` model with fields such as `coverImage`, `date`, and `content`. Align the model or the queries before expecting posts to load.

The `pnpm setup` script imports the content model from `lib/export.json` and requires both `CONTENTFUL_SPACE_ID` and `CONTENTFUL_MANAGEMENT_TOKEN`.

## Available scripts

| Command | Description |
| --- | --- |
| `pnpm dev` | Start the development server |
| `pnpm build` | Create a production build |
| `pnpm start` | Serve the production build |
| `pnpm setup` | Import the Contentful model in `lib/export.json` |

## Routes

- `/` — list posts
- `/posts/[slug]` — a post detail page
- `GET /api/draft?secret=...&slug=...` — enable draft mode and redirect to a post
- `GET /api/disable-draft` — disable draft mode
- `POST /api/revalidate` — revalidate the `posts` cache tag using the `x-vercel-reval-key` header

## Project structure

```text
app/                  App Router pages and API routes
components/           Shared site components
lib/api.ts            Contentful GraphQL queries and data mapping
lib/export.json       Contentful model imported by the setup script
lib/setup.js          Contentful model import script
public/               Static images, icons, and other public assets
styles/               Shared styles
CONTENTFUL_SETUP.md   Contentful setup notes
```

## Deployment

Deploy as a Next.js application on Vercel or another platform that supports Next.js. Add the required Contentful variables to the deployment environment. Configure your Contentful preview and revalidation integrations with the corresponding server-only secrets; never expose management or API tokens in client-side code.
