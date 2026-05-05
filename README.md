# ExecOS

Autonomous email + calendar assistant built with Next.js, Clerk, PostgreSQL, Gmail API, Google Calendar API, and Gemini.

ExecOS processes unread emails, extracts action items, drafts replies, and creates calendar events with minimal manual work.

## Highlights

- AI email analysis with structured output
- Auto-drafted Gmail replies
- Calendar event extraction and creation
- Task creation from email action items
- Agent run monitoring dashboard
- OAuth token encryption at rest (AES-256-GCM)
- Manual run + cron-based run support

## Screenshots

Add your product screenshots here.

### Dashboard

<img width="2178" height="1107" alt="image" src="https://github.com/user-attachments/assets/7c071efe-d5f9-47fc-ba16-0bd88fb20ef3" />


### Monitoring

<img width="2166" height="1101" alt="image" src="https://github.com/user-attachments/assets/63143de6-7a6f-4a57-9969-4fd056d3db54" />


### Settings / Integrations

<img width="2170" height="1104" alt="image" src="https://github.com/user-attachments/assets/5842f5c1-23bb-4e03-97d6-788cc5d6f8cb" />


## Tech Stack

- Next.js 16 (App Router)
- TypeScript
- Clerk Authentication
- PostgreSQL + Drizzle ORM
- Google APIs (Gmail + Calendar)
- Vercel (deployment + cron)
- Gemini via `@ai-sdk/google`

## Project Structure

```text
app/
  (auth)/
  (main)/
    dashboard/
    monitoring/
    settings/
  api/
    agents/run/
    auth/google/

components/
db/
lib/
  agent.ts
  agents/
    gmail.ts
    calendar.ts
    process-email.ts
```

## Environment Variables

Create `.env.local`:

```env
# Clerk
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_FALLBACK_REDIRECT_URL=/dashboard
NEXT_PUBLIC_CLERK_SIGN_IN_FALLBACK_REDIRECT_URL=/dashboard

# Database
DATABASE_URL=

# Google OAuth
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
NEXT_PUBLIC_APP_URL=http://localhost:3000

# Encryption (64-char hex string for AES-256-GCM key)
ENCRYPTION_KEY=

# Gemini API key (used by @ai-sdk/google)
GOOGLE_GENERATIVE_AI_API_KEY=

# Cron auth secret
CRON_SECRET=
```

## Local Setup

1. Install dependencies:

```bash
bun install
```

2. Push DB schema:

```bash
npx drizzle-kit push
```

3. Start dev server:

```bash
bun run dev
```

4. Build locally:

```bash
bun run build
```

## How Agent Flow Works

1. Fetch unread emails from Gmail
2. Fetch upcoming calendar events
3. Analyze each email with Gemini
4. Create tasks from action items
5. Draft reply if required
6. Create calendar events if required
7. Mark processed emails as read
8. Log full run output to monitoring

## API Route

- `POST /api/agents/run`
  - Manual authenticated trigger from app
  - Also supports cron-authenticated execution
- `GET /api/agents/run`
  - Cron-authenticated execution

## Cron Configuration

Current Vercel cron config:

```json
{
  "crons": [
    {
      "path": "/api/agents/run",
      "schedule": "0 9 * * *"
    }
  ]
}
```

Note: Vercel Hobby supports once-daily cron schedules.

## Deploy (Vercel)

1. Import repo into Vercel
2. Add all environment variables in Vercel project settings
3. Deploy
4. Validate cron route once:

```bash
curl -H "Authorization: Bearer <CRON_SECRET>" https://<your-domain>/api/agents/run
```

## Security Notes

- Rotate any API keys/secrets if they were exposed publicly
- Keep `.env.local` out of version control
- Use separate secrets for auth and cron (`BETTER_AUTH_SECRET` is not a replacement for `CRON_SECRET`)

## Roadmap

- Multi-provider model fallback
- Per-user agent schedules
- Better deduplication for calendar event creation
- Human-in-the-loop approval mode for drafts/events

## License

MIT

