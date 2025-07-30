# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Common Development Commands

### Testing
- `php artisan test` or `./vendor/bin/phpunit` - Run PHPUnit tests
- PHPUnit configuration is in `phpunit.xml` with coverage settings

### Code Quality
- `./vendor/bin/phpstan analyse` - Run static analysis (level 5)
- PHPStan configuration is in `phpstan.neon.dist`

### Artisan Commands
- `php artisan` - List all available Artisan commands
- This project has numerous custom commands in `app/Ship/Console/Commands/`

## Architecture Overview

This is a **Lumen-based API** using a **Container-based architecture** similar to Porto/Apiato pattern:

### Core Structure
- **app/Ship/** - Contains shared abstracts, services, and infrastructure
- **app/Containers/v1/** - Feature containers (Authentication, Catalog, Order, User, etc.)
- **app/Containers/v2/** - Version 2 containers for API evolution
- **app/Containers/cns/** - CNS (possibly third-party) integration

### Container Pattern
Each container follows this structure:
- **Actions/** - Business logic handlers
- **Controllers/** - HTTP controllers
- **DTO/** - Data Transfer Objects (using Spatie DTO)
- **Models/** - Eloquent models
- **Resources/** - API resources for response formatting
- **Routes/** - Container-specific routes
- **Enums/** - Container-specific enums (using BenSampo Laravel Enum)
- **ModelFilters/** - Query filters (using EloquentFilter)

### Key Architectural Components

#### Route Loading
- Routes are auto-loaded via `App\Ship\Loaders\RouteLoader`
- Each container's routes are prefixed with kebab-case container name
- API versioning through container folders (v1, v2)

#### Base Classes
- `App\Ship\Abstracts\Eloquent\Model` - Base model with standardized date formatting
- `App\Ship\Controllers\Controller` - Base controller with $pageSize = 20

#### Major Features
- **Gaming/E-commerce Platform**: Products, catalogs, purchases, wallets
- **Authentication**: OAuth with Passport, OTP verification
- **Payment**: QR payments, multiple payment types, coupons
- **Gamification**: Missions, levels, rewards, referrals
- **Third-party Integrations**: DTAC, UniPin, CodaShop publishers

## Database & Models

### Key Models
- User management with levels, wallets, referrals
- Product catalog with variants, attributes, images
- Order system with purchases, attributes, redemption codes
- Coupon system with rules, templates, bulk generation
- Mission/gamification system with gifts and rewards

### Database Features
- Extensive migrations in `database/migrations/`
- Uses Spatie Laravel Permission for roles/permissions
- Audit logging with Laravel Auditing
- Soft deletes and timestamps on most models

## Dependencies & Services

### Major Dependencies
- **Laravel Lumen 8** - Micro-framework
- **Passport** - OAuth2 authentication
- **Spatie packages** - Permissions, slugs, DTOs, schedule monitoring
- **Publisher integrations** - UniPin, CodaShop services
- **Queueing** - Laravel queues for async processing
- **External APIs** - DTAC services, payment gateways

### Services in app/Ship/Services/
- **Dtac/** - DTAC telecom integration
- **Publisher/** - Game publisher integrations
- **Google/RecaptchaService** - reCAPTCHA validation
- **Tencent/CosService** - Cloud storage

## Development Notes

### Code Style
- Uses PHP 7.3+ | 8.0+ syntax
- Follows Laravel/Lumen conventions
- Heavy use of Enums for constants
- DTO pattern for data validation and transfer

### Key Patterns
- **Action classes** handle specific business operations
- **Resource classes** format API responses consistently  
- **Filter classes** handle complex query filtering
- **Event/Listener** pattern for decoupled business logic

### Testing
- PHPUnit configured with feature and unit test suites
- Test database: `dtac_test`
- Extensive coverage exclusions defined in phpunit.xml
