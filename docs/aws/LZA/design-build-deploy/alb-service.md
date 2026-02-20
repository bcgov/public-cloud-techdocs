# Making internal ALB publicly reachable using tags

Last updated: **{{ git_revision_date_localized }}**

This guide explains how to publish an internal Application Load Balancer (ALB) by adding a small set of AWS resource tags. It’s intended for workload teams who want to expose an application endpoint publicly.

## What you need to know

- You deploy an internal ALB in your workload account (for example, `abc123-dev`).
- Our platform publishes it externally when you apply the correct tags.
- You can publish:
  - a wildcard endpoint (everything under your subdomain), or
  - specific hostnames (one or more “named” apps), using `PublicHost`.

## Required tags

### `Public` (required)

Controls whether an ALB should be published.

- **Key:** `Public`
- **Value:** `True`

If `Public` is not `True`, the ALB will not be made public.

### `PublicHost` (optional)

Controls which hostnames your ALB should serve.

- **Key:** `PublicHost`
- **Value:** one or more host labels, separated by spaces

If `PublicHost` is not provided, empty, or removed, the ALB becomes a wildcard mapping.

## Wildcard vs specific hostnames

### Option A — wildcard (default)

If you set only:

- `Public=True`
- and no `PublicHost`

…your ALB becomes the default for:

- `*.YOUR-ACCOUNT.stratus.cloud.gov.bc.ca`

Example:

- `*.abc123-dev.stratus.cloud.gov.bc.ca`

That means it can serve:

- `app1.abc123-dev.stratus.cloud.gov.bc.ca`
- `anything.abc123-dev.stratus.cloud.gov.bc.ca`

### Option B — specific hostname(s) using `PublicHost`

If you set:

- `Public=True`
- `PublicHost="app1"` (or multiple labels)

…your ALB serves only those specific hostnames.

Example:

- `PublicHost="app1 app2"`

Results in:

- `app1.abc123-dev.stratus.cloud.gov.bc.ca`
- `app2.abc123-dev.stratus.cloud.gov.bc.ca`

This option is how you support multiple internal ALBs in the same account and route traffic to the right one based on hostname.

## How precedence works

If you have:

- one ALB published as wildcard, and
- another ALB published with specific `PublicHost` labels

Then the platform ensures:

- specific hostnames take precedence over the wildcard.

Example:

- Wildcard ALB covers: `*.abc123-dev...`
- Host-specific ALB covers: `app1.abc123-dev...`

Traffic to:

- `app1.abc123-dev...` → goes to the `PublicHost` ALB
- `anything-else.abc123-dev...` → goes to the wildcard ALB

## Supporting multiple ALBs with `PublicHost`

You can publish multiple ALBs in the same workload account, as long as:

- each ALB has unique `PublicHost` labels
- only one ALB acts as the wildcard (no `PublicHost`)

### Valid example (multi-ALB)

ALB #1 (default / wildcard):

- `Public=True`
- (no `PublicHost`)

ALB #2:

- `Public=True`
- `PublicHost="app1"`

ALB #3:

- `Public=True`
- `PublicHost="app2"`

Result:

- `app1.abc123-dev...` routes to ALB #2
- `app2.abc123-dev...` routes to ALB #3
- everything else routes to ALB #1

## Conflict rules (what we reject)

### `PublicHost` label conflicts

A `PublicHost` label can only be owned by one ALB per account.

Example conflict:

- ALB #1 has `PublicHost="app1 app2"`
- ALB #2 tries `PublicHost="app2"`

Result:

- ALB #2 is not published for `app2` (first one wins)

### Multiple wildcard ALBs

Only one wildcard mapping is allowed per account.

If one ALB is already the wildcard (`Public=True` + no `PublicHost`), then another ALB cannot also be wildcard.

## `PublicHost` value rules (validation)

`PublicHost` must be a list of labels, not full domains.

**Allowed:**

- `PublicHost="app1"`
- `PublicHost="app1 app2"`

**Not allowed:**

- `PublicHost="app1.abc123-dev.stratus.cloud.gov.bc.ca"` (contains dots)
- `PublicHost="*.app1"` (contains `*`)
- `PublicHost="_app1"` (invalid characters)
- `PublicHost="PayRoll!"` (invalid characters)

## Publishing lifecycle behaviors

### Publishing an ALB

To publish:

1. Add tag: `Public=True`
2. Optionally add: `PublicHost="label1 label2"`

The platform will create the public DNS mapping for you.

### Unpublishing an ALB

To unpublish:

- remove the `Public` tag

Notes:

- Removing `PublicHost` does **not** unpublish the ALB. It only changes which hostnames are mapped. If `Public=True` and you remove `PublicHost`, the ALB is treated as a wildcard mapping (subject to the wildcard rules described above).
- If `Public` is not `True`, the ALB is treated as not public and no public DNS mapping is created.
