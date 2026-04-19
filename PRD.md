# PRD

Build a production-ready MVP for an AI app called Untangle.

This is a mental clarity tool that takes a user's thought and returns a structured response with emotional validation, logical reframing, and actionable steps.

Tech stack:

- - Frontend: Flutter (mobile-first)
- - Backend: Firebase Cloud Functions (Node.js, TypeScript)
- - Database: Firestore (for rate limiting only in MVP)
- - AI: Groq API
- - Validation: Zod

## CORE FUNCTIONALITY

User inputs:

- thought (string, min 10 chars)

- tone (enum: calm | logical | motivational)

System returns structured JSON:

{

  "feeling_validation": "string",

  "reframe": "string",

  "reality_check": "string",

  "action_steps": ["string", "string", "string"]

}

## BACKEND IMPLEMENTATION

Create Firebase Cloud Function:

**POST /api/reframe**

Steps:

1. Validate input

2. Check rate limit (max 5/day per IP)

3. Call Groq API

4. Parse JSON response

5. Validate using Zod schema

6. Return structured response



**ZOD SCHEMA**

Use this exact schema:

import { z } from "zod";

const ClaritixSchema = z.object({

  feeling_validation: z.string().min(1),

  reframe: z.string().min(1),

  reality_check: z.string().min(1),

  action_steps: z.array(z.string()).length(3)

});



## GROQ AI PROMPT

System prompt:

"You are a mental clarity coach.

Return ONLY valid JSON.

Rules:

- Validate the user's feeling briefly (1–2 sentences)

- Reframe the thought logically

- Provide a grounded reality check

- Give exactly 3 actionable steps

- Keep it concise

- Match tone: calm, logical, or motivational

- No medical advice

- No extra fields"

User prompt template:

Thought: {{thought}}

Tone: {{tone}}

---

## ERROR HANDLING

Return structured error:

{

  data: null,

  error: {

    code: "INVALID_INPUT | PARSE_ERROR | MODEL_ERROR | RATE_LIMIT",

    message: "string"

  }

}

Retry AI call once if JSON parsing fails.

---

## RATE LIMITING

Use Firestore:

collection: usage_logs

structure:

{

  ip: string,

  date: string,

  count: number

}

Limit: 5 requests/day

---

## FRONTEND (FLUTTER)

Build simple UI:

### Screen Layout:

- Text input (multiline)

- Tone selector (segmented control)

- Submit button

- Result section (4 blocks)

- Regenerate button

---

### STATE

String thought

String tone

bool loading

Map result

String error

---

### UX

- Disable submit while loading

- Show loader

- Smooth transition input → output

- Preserve input on regenerate

---

## RESULT DISPLAY

Render sections:

- Feeling validation

- Reframe

- Reality check

- Action steps (3 bullets)

No chat UI. Clean structured layout.

---

## SECURITY

- Sanitize input

- Limit input size

- Reject script injection

- Do not allow prompt override

---

## ACCEPTANCE CRITERIA

- Always returns valid JSON or structured error

- Exactly 3 action steps

- Tone consistency

- No crashes

- Rate limiting works

---

## FILE STRUCTURE

/functions

  /src

    reframe.ts

    schema.ts

    groq.ts

    rateLimit.ts

/flutter_app

  /lib

    main.dart

    screens/home.dart

    widgets/

---

Build this in modular, clean, production-ready code.

Avoid overengineering.
