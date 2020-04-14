# Jooby/Kotlin + Svelte/Bootstrap app template

A relatively lightweight modern app template using Kotlin/JVM.
For people asking me at conferences what would I recommend from technical perspective.

* Server API using Jooby
    * Postgres is used for DB (runnable using docker-compose)
    * Liquibase migrates the DB
    * Server unit tests use Junit5/Mockk
    * Repository integration tests run in in-memory H2 to avoid any dependencies
* UI is built with Svelte + Rollup with TypeScript support
    * UI tests use Jest and TypeScript (there is no IDE support for Svelte+TS yet)
* E2E tests use Selenide to drive the actual browser backed by H2
* The whole 4-stage build runs using `docker build`, see below

## Noteworthy features

* Efficient Docker usage (cached layers in order of less frequent changes), Gradle downloads dependencies once
* Builds/tests run in Docker in several stages, test results available after build in [Jenkins](Jenkinsfile)
* [Internationalization](i18n) (both client-side and server-side)
* Supports static server-side rendered pages using [Pebble templates](ui/static)
* Selenide/Selenium tests work inside of Docker

No frameworks needed for this:

* 30-line [dependency injection](src/app/AutoCreatingServiceRegistry.kt)
* 100-line [JDBC extensions](src/db/JdbcExtensions.kt) instead of ORM and [transaction management](src/db/ThreadLocalTransaction.kt)  
* Simple client-side [router](ui/routing/Router.ts) used in [root component](ui/App.svelte)

Testing:

* 3 layers of testing: UI (client-side components), server-side (unit + repositories) and E2E using Selenide to drive an actual browser.
* Repository tests rollback to avoid recreation of the DB each time
* E2E tests test login once and then use [fake login](src/auth/FakeLoginForTestingController.kt) to get to needed places quickly

## Running in Docker

`docker-compose up --build`

or to just start the DB:
`docker-compose up -d db`

This will bind to `127.0.0.1:55432` by default

# Development

After clone:

```
npm install
```

Then:

```
npm run watch
# or just npm run build
./gradlew run
```

To run tests:

* `npm test` - for UI components
* `./gradlew test` - for API
* `./gradlew e2eTest` - for in-browser End-to-End tests

## Running from IDE

# UI
## Static/landing pages

Are generated using Pebble templates in `ui/static`

## Dynamic pages

Pages after login use Svelte framework with components in `ui`

## Adding icons

The design uses Feather icon set available at https://feather.netlify.com/.

To add an icon:

1. Download any icon from that repository as svg. For custom ones, use existing ones as a basis for consistency.
2. Add the icon to `public/img/icons`.
3. Generate the sprites using the run configuration in IDEA or `npm run-script generate-sprite`
