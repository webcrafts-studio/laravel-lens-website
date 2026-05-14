# Upgrade to v2.1.0

Lens v2.1.0 adds interactive state scanning, baseline-based CI checks, dashboard localization, and local HTTPS support.

## What's New

- interactive state scans for menus, modals, tabs, validation states, dropdowns, and other UI that appears after user interaction
- visual state recorder in the dashboard
- reusable interaction scripts
- `stateLabel` metadata on issues found during interactive state scans
- state labels in scan history, PDF reports, and scan comparisons
- baseline quality gate for CI
- configurable baseline file path
- localized dashboard UI
- language switcher in the dashboard and state recorder
- bundled translations for English, Polish, Spanish, French, and German
- publishable translation files
- optional HTTPS certificate error ignoring for local self-signed environments

## Upgrade Steps

Update the package:

```bash
composer update webcrafts-studio/lens-for-laravel
```

Run migrations:

```bash
php artisan migrate
```

v2.1.0 adds `state_label` to stored scan issues. This lets Lens preserve which interactive state produced a given violation.

If you published the config before v2.1.0, republish it:

```bash
php artisan vendor:publish --tag="lens-for-laravel-config"
```

Or add the new keys manually:

```php
'locale' => env('LENS_FOR_LARAVEL_LOCALE', app()->getLocale()),

'fallback_locale' => env('LENS_FOR_LARAVEL_FALLBACK_LOCALE', 'en'),

'supported_locales' => [
    'en' => 'English',
    'pl' => 'Polski',
    'es' => 'Español',
    'fr' => 'Français',
    'de' => 'Deutsch',
],

'baseline_path' => env('LENS_FOR_LARAVEL_BASELINE_PATH', storage_path('app/lens-for-laravel/baseline.json')),

'ignore_https_errors' => env('LENS_FOR_LARAVEL_IGNORE_HTTPS_ERRORS', false),
```

## Interactive State Scans

Interactive state scans run axe-core after Lens performs browser actions. This is useful for UI that is not present on initial page load.

Example script:

```text
state: Navigation open
click: [data-menu-button]

state: Form validation
type: input[name="email"] => invalid@example.test
click: button[type="submit"]
wait: 300
```

Supported actions:

| Action | Purpose |
|--------|---------|
| `click` | Click an element without navigating away. |
| `type` | Set an input value and dispatch input/change events. |
| `select` | Set a select value and dispatch input/change events. |
| `check` | Check a checkbox or radio input. |
| `uncheck` | Uncheck a checkbox. |
| `wait` | Wait a number of milliseconds before continuing. |

Interactive scan scripts are validated before execution. Lens limits script size, state count, action count, selector length, value length, and wait duration.

## Baseline Quality Gate

For existing projects, a strict `--threshold=0` gate can fail on accessibility debt that already exists. Baselines let you approve the current state once and fail only when new violations appear.

Create a baseline:

```bash
php artisan lens:audit --crawl --baseline
```

Run a regression-only check:

```bash
php artisan lens:audit --crawl --fail-on-new
```

Use a custom baseline file:

```bash
php artisan lens:audit --crawl --fail-on-new --baseline-file=.github/lens-baseline.json
```

The baseline stores stable fingerprints built from the axe rule, normalized URL path, interactive state label, selector, source file, and source type. Host and scheme are normalized so a local baseline can be compared in CI.

## Dashboard Localization

The dashboard now supports localized UI strings. Configure the default locale:

```text
LENS_FOR_LARAVEL_LOCALE=en
LENS_FOR_LARAVEL_FALLBACK_LOCALE=en
```

Supported bundled locales:

- `en` - English
- `pl` - Polish
- `es` - Spanish
- `fr` - French
- `de` - German

Users can switch language in the dashboard. The selected locale is stored in the session.

Publish translations when you want to customize wording:

```bash
php artisan vendor:publish --tag="lens-for-laravel-translations"
```

## Local HTTPS

For local environments with self-signed HTTPS certificates, enable:

```text
LENS_FOR_LARAVEL_IGNORE_HTTPS_ERRORS=true
```

This is useful for DDEV, Laravel Valet, and similar local setups. The default is `false`, so scans remain strict unless you explicitly opt in.

## API and Payload Changes

Issues can now include:

```json
{
  "stateLabel": "Form validation"
}
```

The field is `null` for regular scans and set for interactive state scans.

The dashboard also adds:

| Method | Path | Purpose |
|--------|------|---------|
| `GET` | `/lens-for-laravel/states/recorder` | Open the visual state recorder. |
| `POST` | `/lens-for-laravel/scan/states` | Run an interactive state scan. |

## Notes

Interactive state scanning improves coverage for interaction-only UI, but it does not replace manual accessibility testing. Keep using keyboard navigation testing, screen reader testing, and manual review for full accessibility work.
