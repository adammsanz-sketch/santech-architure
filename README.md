# AI Agent Architecture

A full-stack AI agent system built with Next.js, Supabase, Google Gemini, and WhatsApp integration.

## Features

- **Chat API**: Google Gemini-powered conversational AI
- **WhatsApp Integration**: Send and receive messages via WhatsApp
- **Database**: Supabase PostgreSQL for data persistence
- **Background Jobs**: Cron jobs for daily summaries and task processing
- **Admin Dashboard**: Monitor users, messages, and sessions

## Setup

### 1. Database Setup

Run the SQL script to create tables:

\`\`\`bash
# The script is located at: scripts/001_initial_schema.sql
# Execute it in your Supabase SQL editor
\`\`\`

### 2. Environment Variables

Add these to your Vercel project:

\`\`\`env
# Supabase
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_anon_key

# AI Model (Google Gemini via Vercel AI Gateway)
AI_MODEL=google/gemini-2.0-flash-exp

# WhatsApp
WHATSAPP_ACCESS_TOKEN=your_whatsapp_token
WHATSAPP_PHONE_NUMBER_ID=your_phone_number_id
WHATSAPP_VERIFY_TOKEN=your_verify_token

# Cron Security
CRON_SECRET=your_random_secret
\`\`\`

### 3. WhatsApp Webhook Setup

1. Go to Meta Developer Console
2. Configure webhook URL: `https://your-domain.com/api/webhook`
3. Set verify token to match `WHATSAPP_VERIFY_TOKEN`
4. Subscribe to `messages` webhook

### 4. Cron Jobs

Cron jobs are configured in `vercel.json`:

- **Daily Summary**: Runs at 9 AM daily (`0 9 * * *`)
- **Task Processing**: Runs every 5 minutes (`*/5 * * * *`)

## API Endpoints

### POST /api/chat
Send a message to the AI agent.

**Request:**
\`\`\`json
{
  "phone": "+60123456789",
  "message": "Hello",
  "name": "John Doe"
}
\`\`\`

**Response:**
\`\`\`json
{
  "success": true,
  "response": "AI response here",
  "session_id": "uuid"
}
\`\`\`

### POST /api/action/send-whatsapp
Send a WhatsApp message.

**Request:**
\`\`\`json
{
  "phone": "+60123456789",
  "message": "Hello from AI"
}
\`\`\`

### POST /api/webhook
Receive WhatsApp messages (configured in Meta Developer Console).

### GET /api/cron/daily-summary
Send daily conversation summaries (protected by CRON_SECRET).

### GET /api/cron/process-tasks
Process pending background tasks (protected by CRON_SECRET).

## Database Schema

- **users**: User information (phone, name)
- **sessions**: Conversation sessions
- **messages**: Chat messages (user, assistant, system)
- **tasks**: Background job queue

## Usage

### Testing Chat Interface

1. Visit the homepage
2. Enter your phone number
3. Start chatting with the AI

### Admin Dashboard

Visit `/admin` to view system statistics and recent activity.

### Queue Background Tasks

\`\`\`typescript
import { queueWhatsAppMessage } from '@/lib/queue/task-queue'

await queueWhatsAppMessage(sessionId, '+60123456789', 'Hello!')
\`\`\`

## Architecture

\`\`\`
┌─────────────┐
│   User      │
└──────┬──────┘
       │
       ▼
┌─────────────────────────────────┐
│   Next.js API Routes            │
│   - /api/chat                   │
│   - /api/webhook                │
│   - /api/action/send-whatsapp   │
└──────┬──────────────────────────┘
       │
       ├──────────────┬──────────────┬──────────────┐
       ▼              ▼              ▼              ▼
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│ Supabase │   │  Gemini  │   │ WhatsApp │   │  Cron    │
│   DB     │   │   AI     │   │   API    │   │  Jobs    │
└──────────┘   └──────────┘   └──────────┘   └──────────┘
\`\`\`

## Deployment

Deploy to Vercel:

\`\`\`bash
vercel deploy
\`\`\`

Make sure to add all environment variables in Vercel project settings.

## License

MIT
