# Lens for Laravel — Website

Marketing website and documentation for **[Lens for Laravel](https://github.com/webcrafts-studio/lens-for-laravel)**, a plug-and-play WCAG accessibility auditor for Laravel applications.

## About

This is the official website for the Lens for Laravel package. It includes:

- Landing page with feature overview, CLI showcase, and dashboard preview
- Full documentation (installation, configuration, usage, AI providers, etc.)
- Light/dark theme support

The package itself lives in a [separate repository](https://github.com/webcrafts-studio/lens-for-laravel).

## Tech Stack

- **Laravel 12** (PHP 8.2+)
- **Tailwind CSS 4** with Vite
- **SQLite** database
- **Pest 4** for testing

## Local Development

### Requirements

- PHP 8.2+
- Composer
- Node.js 18+
- [Laravel Herd](https://herd.laravel.com) (recommended) or any local server

### Setup

```bash
composer install
cp .env.example .env
php artisan key:generate
php artisan migrate
npm install
npm run build
```

### Running

With Laravel Herd the site is automatically available at `https://lens-for-laravel-website.test`.

Alternatively, use the dev script which starts the server, queue worker, log tail, and Vite in parallel:

```bash
composer run dev
```

### Testing

```bash
php artisan test
```

### Code Formatting

```bash
vendor/bin/pint
```

## License

MIT
