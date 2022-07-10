# reserve
Demo app to showcase schedule-based court reservation

## Scenario Requirements

- Court can only be reserved 2 month prior earliest
- Reservation is open from 00:00 JST
- Court reservation is done on a web form (requires cookies)

## Infrastructure

```mermaid
flowchart LR
    subgraph Google
    google_form[Google Form]
    google_form -.-> google_sheet[Google Sheet]
    end

    user((User)) -- reserve --> google_form

    subgraph Reservation Site
    form[(Form)]
    end

    subgraph CircleCI

    circleci_job_cookie[job: refresh cookie] --> circleci_job_reserve[job: reserve]
    circleci_job_lookup[job: todos] --> circleci_job_reserve
    circleci_job_cookie -- grab cookie --> form
    circleci_job_lookup -- look up impending reservations --> google_sheet
    circleci_job_reserve -- reserve --> form
    end
```

## How it works

1. Google Form captures all upcoming reservations
2. Workflow runs on CircleCI, at 23:00+ JST every day:
   1. Job grabs cookie from site, and save to workspace
   2. Another job looks up all reservations to make this 00:00 hour
   3. Final job waits until 00:00 to run reservations
