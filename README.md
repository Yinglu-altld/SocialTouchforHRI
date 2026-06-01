# Social Touch for HRI

This repository contains the initial HTML prototype for the Social Touch for HRI project.

## Preview

Open `index.html` in a browser to view the current prototype.

## Supabase Setup

Run `supabase_setup.sql` in the Supabase SQL Editor before publishing the survey.

The page submits responses to the `survey_responses` table using the public Supabase publishable key. Row Level Security is configured so anonymous visitors can insert responses but cannot read collected data.

## Export Data

In Supabase, open SQL Editor and run:

```sql
select
  participant_id,
  created_at,
  study_version,
  consent_version,
  language,
  age_group,
  country,
  gender,
  selected_intents,
  body_data
from public.survey_responses
order by created_at;
```

To export a long-format table for analysis:

```sql
select
  r.participant_id,
  r.created_at,
  r.age_group,
  r.country,
  r.gender,
  intent.key as intent_id,
  region.key as region_id,
  (region.value #>> '{}')::int as rating
from public.survey_responses r
cross join lateral jsonb_each(r.body_data) as intent(key, value)
cross join lateral jsonb_each(intent.value) as region(key, value)
order by r.participant_id, intent.key, region.key;
```

After running a query, use the Supabase results download button to export CSV.

## Collaboration

Use branches for future changes, then open pull requests for review before merging into `main`.
