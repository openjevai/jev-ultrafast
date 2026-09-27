# OpenJEV Support

This fork adds optional [OpenJEV](https://openjev.sh) support alongside the original
[TypeSafe](https://typesafe.ai) integration. TypeSafe remains the default — anyone
with a `TYPESAFE_API_KEY` sees zero behaviour change.

## What was added

- **`jev_ultrafast/model.py`** — new `provider_config()` function that selects
  between TypeSafe and OpenJEV based on environment variables. The `choose()`
  function now calls it instead of hardcoding the TypeSafe endpoint/key/model.
- **`.env.example`** — documented `OPENJEV_API_KEY`, `JEV_PROVIDER`, and
  `OPENJEV_MODEL` alongside the existing TypeSafe variables.
- **`README.md`** — short note after the project intro crediting TypeSafe first.

## Provider selection rule

1. `JEV_PROVIDER=openjev` → OpenJEV (explicit choice wins).
2. `TYPESAFE_API_KEY` set (and no explicit override) → TypeSafe (unchanged default).
3. Only `OPENJEV_API_KEY` set → OpenJEV.

| | TypeSafe (default) | OpenJEV (optional) |
|---|---|---|
| Endpoint | `https://api.typesafe.ai/v1/systemone` | `https://api.openjev.sh/v1/systemone` |
| Model | `jev-latest` | `openjev` |
| Key env | `TYPESAFE_API_KEY` | `OPENJEV_API_KEY` |
| Retry statuses | 429, 529, 503 | 429, 529, 503 (same `post_json`) |

## How to configure

Set `OPENJEV_API_KEY` (from https://openjev.sh/dashboard) and either leave
`TYPESAFE_API_KEY` unset or set `JEV_PROVIDER=openjev` to force OpenJEV even
when a TypeSafe key is present.

## How it was verified

A live POST request was made to the OpenJEV systemone endpoint with model
`openjev`, state `ping`, and one noul question. It returned HTTP 200.
Additionally, `grep` confirmed no hardcoded `api.typesafe.ai` default remains
in the request path (it is selected dynamically by `provider_config()`).

## Upstream

Original project: https://github.com/browser-use/jev-ultrafast by @browser-use.
