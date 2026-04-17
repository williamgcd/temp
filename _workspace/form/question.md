# form-question

Individual field or question within a form.

## Purpose

Describe one input: its type, prompt, validation, and visibility rules.

## Responsibilities

- Support core types: `text`, `long_text`, `number`, `select`, `multi_select`, `date`, `checkbox`, `scale`, `file`, `signature`.
- Hold validation rules (required, min/max, regex).
- Hold show-if rules referencing earlier answers.

## Data model

- `form_question`: `id`, `form_id`, `order`, `type`, `prompt`, `help_text?`, `options_json?`, `validation_json?`, `show_if_json?`, `required`, `key`.

`key` is a stable field name used on responses and analytics.

## Public API

- `form_question.add(form_id, ...)` / `form_question.update(id, patch)` / `form_question.reorder(form_id, ids[])`
- `form_question.validate(question_id, value)` — server-side validation helper.

## Events emitted

- `form_question.added`, `form_question.updated`, `form_question.removed`.

## Depends on

- `_workspace/form`.

## Consumed by

- `_workspace/form-response`, form UI renderer.
