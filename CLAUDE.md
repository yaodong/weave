# Weave

This file provides guidance to AI agents when working with code in this repository.

## What is Weave

Weave is an application that helps users navigate everyday interpersonal relationships through AI-assisted journaling. Users reflect on their interactions, relationships, and social dynamics with AI support to gain insights and improve their connections with others.

## Technology Stack

Weave is built with Rails 8.1 and uses:
- Ruby 3.4.7
- SQLite3 as the database
- Bun for JavaScript bundling and dependency management
- Tailwind CSS 4.x for styling
- Hotwire (Turbo + Stimulus) for reactive frontend
- Solid Cache, Solid Queue, and Solid Cable for Rails infrastructure

## Development Commands

### Server & Development
```bash
# Start development server with live reloading (runs all services)
bin/dev

# Start Rails server only
bin/rails server

# Rails console
bin/rails console
```

The `bin/dev` command runs all services concurrently via Procfile.dev:
- Rails server on port 3000 with debugging enabled
- JavaScript build watcher using Bun
- CSS build watcher using Tailwind

### Database
```bash
# Setup database (run after cloning)
bin/rails db:setup

# Run migrations
bin/rails db:migrate

# Reset database
bin/rails db:reset

# Generate a new migration
bin/rails generate migration MigrationName
```

This project uses SQLite3 for all data storage. Database schemas are organized across separate files:
- `db/cache_schema.rb` - Solid Cache schema
- `db/queue_schema.rb` - Solid Queue schema
- `db/cable_schema.rb` - Solid Cable schema

### Testing

```bash
# Run all tests
bin/rails test

# Run a specific test file
bin/rails test test/models/example_test.rb

# Run a specific test by line number
bin/rails test test/models/example_test.rb:10

# Run system tests
bin/rails test:system
```

Tests run in parallel by default using all available CPU cores (configured in test/test_helper.rb).

### Code Quality
```bash
# Run RuboCop linter
bundle exec rubocop

# Auto-fix RuboCop violations
bundle exec rubocop -a

# Run security audit
bundle exec bundler-audit check --update

# Run Brakeman security scanner
bundle exec brakeman
```

This project follows the rubocop-rails-omakase style guide for code formatting and conventions.

### Generators

Always prefer to use Rails generators rather than creating files directly.

```bash
# Generate a new controller
bin/rails generate controller ControllerName action1 action2

# Generate a new model
bin/rails generate model ModelName field1:type field2:type

# Generate a new Stimulus controller
bin/rails generate stimulus ControllerName
```

## Architecture

### Application Structure

- **Application module name:** `Weave` (config/application.rb:9)
- **Autoloading:** The `lib/` directory is autoloaded (excludes assets, tasks)
- **Rails version:** 8.1 with modern defaults

### Frontend Architecture

**JavaScript bundling:**
- Custom Bun-based setup (configuration in bun.config.js)
- Entry point: `app/javascript/application.js`
- Stimulus controllers location: `app/javascript/controllers/`
- Auto-registers controllers via `app/javascript/controllers/index.js`
- Build output: `app/assets/builds/` with source maps

**CSS compilation:**
- Tailwind CSS 4.x compiled via Tailwind CLI
- Source: `app/assets/stylesheets/application.tailwind.css`
- Build output: `app/assets/builds/application.css`

**Asset pipeline:**
- Uses Propshaft (NOT Sprockets)

### Backend Architecture

**Solid Stack:**
This application uses database-backed adapters instead of Redis:
- Solid Cache for caching
- Solid Queue for background jobs
- Solid Cable for ActionCable/WebSockets

**Image processing:**
- Uses the `image_processing` gem with libvips

### Deployment

**Docker:**
- Production-ready Dockerfile included
- Uses Thruster as web server (port 80)
- Multi-stage build with Bun and Ruby
- Configured for Kamal deployment

**Kamal:**
- Deployment configuration in `.kamal/` directory

## Development Notes

### Working with JavaScript

Stimulus controllers follow the standard Hotwire pattern:
- Controllers in `app/javascript/controllers/`
- Automatically registered in `index.js`
- Use `data-controller`, `data-action`, `data-target` attributes in views

### Working with Views

- Main layout: `app/views/layouts/application.html.erb`
- Uses Turbo for navigation
- Configured as PWA-ready (manifest commented out in routes)
- Application name: "Weave"

### Database Considerations

- SQLite3 is used for all environments
- Separate schema files for Solid Cache/Queue/Cable
- Fixtures loaded alphabetically in tests

### Adding Dependencies

**Ruby gems:**
```bash
bundle add gem_name
bundle install
```

**JavaScript packages:**
```bash
bun add package-name
```

### Running in Production

The Dockerfile builds a production-ready container:
```bash
docker build -t weave .
docker run -d -p 80:80 -e RAILS_MASTER_KEY=<your-key> --name weave weave
```
