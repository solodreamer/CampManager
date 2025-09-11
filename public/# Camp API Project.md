# AsiaYo Doraemon API Project

## Project Information
- **Project Name**: AsiaYo Doraemon API
- **Framework**: Laravel 8.x
- **PHP Version**: 8.1
- **Development Environment**: Docker (php-fpm-8.1)
- **Current Version**: 1.377.2

## System Architecture
Follows Clean Architecture principles with layered architecture:
FormRequest -> Controller -> DTO -> Service -> Repository -> Model

Response uses Transformers for data transformation.

### Directory Structure
src/
├── app/
│   ├── Asiayo/           # Core business logic
│   │   ├── Controllers/  # Controller layer
│   │   ├── DTO/         # Data Transfer Objects
│   │   ├── Service/     # Service layer
│   │   └── Repository/  # Data access layer
│   ├── Http/            # HTTP related
│   └── Providers/       # Service providers
├── config/              # Configuration files
├── database/            # Database migrations and seeds
├── routes/              # Route definitions
├── tests/               # Test files
└── vendor/              # Third-party packages
```

## Code Standards

### Coding Standards
- **Architecture Principles**: Clean Code, SOLID principles
- **Code Style**: PSR-2 standard
- **PHP Syntax Check**: All PHP files must pass syntax check (`php -l`)
- **Code Sniffer**: Use PHPCS to check PSR-2 standard compliance
- **Code Comments**: Minimize the use of comments in code. Write self-documenting code with clear variable names, method names, and class structures. Only add comments when absolutely necessary for complex business logic or external integrations.

### Code Check Scope
- src/app/ - Application core
- src/tests/ - Test files
- src/config/ - Configuration files
- src/routes/ - Route files
- **Excluded**: src/app/Asiayo/Controllers/Document directory

### PHP-CS-Fixer Configuration
Configuration file: .php-cs-fixer.dist.php

Main rules:
- @PSR2 Base standard
- cast_spaces No spaces in type casting
- concat_space Spaces in string concatenation
- array_syntax Use short array syntax
- ordered_imports Sort imports
- no_useless_else Remove useless else

### Code Check Commands
bash
# PHP-CS-Fixer format check
docker run --rm -v $(pwd):/app -w /app php-fpm-8.1 php-cs-fixer fix --using-cache no --config .php-cs-fixer.dist.php --dry-run

# PHP syntax check
docker run --rm -v $(pwd):/app -w /app php-fpm-8.1 php -l -d display_errors=0 [file_path]

# CodeSniffer check (PSR-2)
docker run --rm -v $(pwd):/app -w /app php-fpm-8.1 phpcs --standard=PSR2 -p src/app/ src/tests/ src/config/ src/routes/

### Naming Conventions
- Classes use PascalCase
- Methods and variables use camelCase
- Constants use UPPER_CASE
- Files and directories use snake_case

## Testing

### Testing Framework
- **Main Tool**: PHPUnit 9.6.4
- **Parallel Testing**: Paratest 6.11.1
- **Runtime Environment**: Local environment uses homebrew php 8.1

### Test Types
1. **Feature Tests** (`tests/Controllers/`) - Feature tests
2. **Unit Tests** (`tests/Unit/`) - Unit tests  
3. **Service Tests** (`tests/Services/`) - Service layer tests

### Test Execution
bash
# Parallel test execution
./src/vendor/bin/paratest -p8 --configuration ./src/phpunit.xml

# Execute in Docker environment
docker run --rm -v $(pwd):/app -w /app php-fpm-8.1 ./src/vendor/bin/paratest -p8 --configuration ./src/phpunit.xml

### Test Notes
- Test environment does not have Redis installed, please Mock Redis-related tests
- Test database uses SQLite (`sqlite_test`)
- Memory limit set to 512M

### Test Coverage
Included directories:
- ./app - Application core

Excluded directories:
- ./app/Http/Middleware - Middleware
- ./app/Http/Controllers - Controllers
- ./app/AsiaYo/DTO - Data Transfer Objects
- ./app/AsiaYo/Enum - Enumerations
- ./app/Providers - Service providers

## API Documentation

### Documentation Standards
- **Language Requirement**: All API documentation MUST be written in English
- **Documentation Completeness**: Every public API endpoint must have comprehensive documentation
- **Consistency**: Follow consistent documentation patterns across all APIs

### OpenAPI (Swagger)
- **Tools**: zircote/swagger-php 3.0
- **Documentation Generation**: Use Swagger-php annotations to auto-generate API documentation
- **Standard**: OpenAPI 3.0 specification

### Annotation Examples
php
/**
 * @OA\Post(
 *     path="/api/example",
 *     summary="Example API",
 *     tags={"Example"},
 *     @OA\RequestBody(
 *         required=true,
 *         @OA\JsonContent(ref="#/components/schemas/ExampleRequest")
 *     ),
 *     @OA\Response(
 *         response=200,
 *         description="Success",
 *         @OA\JsonContent(ref="#/components/schemas/ExampleResponse")
 *     )
 * )
 */

## Development Environment Setup

### Docker Environment
- **Base Image**: php-fpm-8.1
- **Working Directory**: /app
- **Execution Method**: docker run --rm -v $(pwd):/app -w /app php-fpm-8.1 [command]

### Main Dependencies
- **Framework**: Laravel 8.x
- **Database**: Laravel ORM (Eloquent)
- **Cache**: Redis (development environment needs Mock)
- **File Storage**: AWS S3 (league/flysystem-aws-s3-v3)
- **Image Processing**: Intervention Image
- **PDF Generation**: mPDF
- **Search**: Algolia
- **Monitoring**: Sentry

### Common Commands
bash
# Format code
docker run --rm -v $(pwd):/app -w /app php-fpm-8.1 php-cs-fixer fix --config .php-cs-fixer.dist.php

# Execute tests
docker run --rm -v $(pwd):/app -w /app php-fpm-8.1 ./src/vendor/bin/paratest -p8 --configuration ./src/phpunit.xml

# Code style check
docker run --rm -v $(pwd):/app -w /app php-fpm-8.1 phpcs --standard=PSR2 -p src/app/ src/tests/ src/config/ src/routes/

## Error Debugging

### Error Log Location
When errors occur during program execution, you can check log files at the following location:
- **Location**: {your_project_root}/doraemon/src/storage/logs/
- **File Format**: stackTrace-YYYY-MM-DD.log
- **Content**: Contains complete stack trace, request parameters, response content and other detailed error information

### Viewing Error Logs
bash
# View today's latest errors
tail -50 {your_project_root}/doraemon/src/storage/logs/stackTrace-$(date +%Y-%m-%d).log

# Real-time monitoring of error logs
tail -f {your_project_root}/doraemon/src/storage/logs/stackTrace-$(date +%Y-%m-%d).log

# Search for specific errors
grep -n "ErrorClassName" {your_project_root}/doraemon/src/storage/logs/stackTrace-$(date +%Y-%m-%d).log

### Route-related Issues
If you encounter route-related errors (such as Method Not Allowed), remember to clear route cache:
bash
cd {your_project_root}/doraemon/src
docker run --rm -v $(pwd):/app -w /app php-fpm-8.1 php artisan route:clear

### Code Review
- Extract Pull Requests through GitHub CLI and perform code review in local project

## Important Notes
1. Must pass code standard checks before submission (PHP syntax, PSR-2, PHP-CS-Fixer)
2. New features require corresponding tests
3. API changes require updating Swagger documentation
4. Follow existing code style and architecture patterns
5. Redis-related functions need appropriate Mock in test environment
6. When program execution errors occur, please first check src/storage/logs/stackTrace-*.log error logs

```