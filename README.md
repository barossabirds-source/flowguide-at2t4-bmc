# FlowGuide — AT2 Task 4
## Stage 2 Business Innovation · Business Model Evaluation

---

## File structure

```
flowguide-at2t4/
├── index.html                    ← Student-facing app
├── netlify.toml                  ← Netlify config (functions only, no publish dir)
├── netlify/
│   └── functions/
│       └── flowguide.js          ← API proxy + Supabase logger
├── supabase_setup.sql            ← Run once in Supabase SQL editor
└── SYSTEM_PROMPT.md              ← Human-readable prompt reference
```

---

## Deploy steps

### 1. Supabase
- Open your Supabase project (uxeolwdahwuggeapuurz)
- Go to SQL Editor → paste and run `supabase_setup.sql`
- Confirm `flowguide_sessions` table exists

### 2. GitHub
- Create a new repo (e.g. `flowguide-at2t4`) on barossabirds-source
- Push all files maintaining the structure above

### 3. Netlify
- New site → Import from GitHub → select the repo
- Build settings: leave blank (no build command, no publish directory)
- Environment variables — add all three:
  ```
  ANTHROPIC_API_KEY   = your Anthropic key
  SUPABASE_URL        = https://uxeolwdahwuggeapuurz.supabase.co
  SUPABASE_SERVICE_KEY = your Supabase service role key (not anon key)
  ```
- Deploy

### 4. Test
- Open the deployed URL
- Enter a test name and start a session
- Check Supabase Table Editor → flowguide_sessions for logged rows

---

## Teacher dashboard

The `flowguide_sessions` table gives you:

| Column | Use |
|---|---|
| `student_id` | Who had the session |
| `phase` | Which phase each exchange happened in |
| `evaluation_flag` | true = evaluative response, false = descriptive |
| `phase_complete` | true when a phase gate was met |
| `student_message` | What the student wrote |
| `ai_response` | What FlowGuide replied |
| `created_at` | Timestamp |

Query to see evaluation flag rate per student:
```sql
select
  student_id,
  count(*) as total_exchanges,
  sum(case when evaluation_flag then 1 else 0 end) as evaluative_count,
  round(100.0 * sum(case when evaluation_flag then 1 else 0 end) / count(*), 0) as eval_pct
from flowguide_sessions
group by student_id
order by eval_pct desc;
```

---

## Evidence collection (trial)

For the pre/post trial:
1. Administer pre-test (paper or Google Form) before the session
2. Students complete one FlowGuide session (~20 min)
3. Administer identical post-test immediately after
4. Score each response 0–2 per question (total /10)
5. Mean gain of ≥2 points across the class = meaningful evidence threshold
6. Keep one anonymised pre/post pair at each gain level as qualitative evidence
