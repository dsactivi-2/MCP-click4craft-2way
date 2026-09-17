# Repository instructions

## Project purpose

This repository contains an independent TypeScript/Node.js MCP server for controlled, read-only access to a Neon PostgreSQL database.

The PHP CRM is an external reference system only. Its filter and business behavior may be analyzed and documented, but the PHP CRM must not be copied into this repository, imported at runtime, installed as a dependency, or deployed together with this MCP server.

The documented CRM behavior is used to define equivalent MCP tool inputs and filtering semantics.

## Technology

- TypeScript
- Node.js
- Official MCP TypeScript SDK
- Zod or equivalent strict schema validation
- Neon Serverless Driver or a supported PostgreSQL client
- TypeScript strict mode
- Automated tests
- ESLint and Prettier

## Repository boundaries

The PHP CRM and this MCP server are separate systems.

The PHP CRM may only be used to:

- inspect existing filtering behavior;
- identify business rules;
- document fields, operators, sorting, pagination, and permissions;
- create comparison fixtures.

Do not:

- copy PHP application code into this repository;
- add a Composer or Laravel runtime dependency;
- import PHP classes or services;
- call the PHP CRM at runtime;
- deploy the PHP CRM with this MCP server;
- invent undocumented CRM behavior.

## MCP tools

Expose narrowly scoped, read-only tools based on documented CRM behavior.

Possible tools include:

- `search_contacts`
- `search_companies`
- `search_deals`
- `get_contact`
- `get_company`
- `get_deal`

Only implement tools and fields supported by the documented specification.

Do not expose:

- `execute_sql`
- arbitrary SQL execution;
- unrestricted table browsing;
- shell execution;
- schema modification;
- unrestricted database writes.

## Input validation

Every tool must:

- use a strict input schema;
- allow only documented fields;
- allow only documented operators;
- use allowlists for sort fields and sort direction;
- enforce a server-side maximum page size;
- validate dates, identifiers, enums, and strings;
- reject unknown or unsafe input.

## Database access

Use Neon PostgreSQL through an environment variable such as `DATABASE_URL`.

Never commit:

- database URLs;
- passwords;
- API keys;
- access tokens;
- production credentials.

The initial implementation must use a dedicated read-only PostgreSQL role.

Do not perform migrations, inserts, updates, deletes, or schema changes through the MCP server unless separately approved and explicitly implemented.

Use parameterized SQL for all values. Never concatenate user-provided values into SQL.

## Query behavior

- Select only required columns.
- Do not use `SELECT *`.
- Enforce result limits.
- Prefer cursor-based pagination for large datasets.
- Use only allowlisted sort expressions.
- Preserve documented CRM filter semantics.
- Clearly mark undocumented or uncertain behavior.
- Keep responses structured and compact.

## Transports

Support `stdio` for local MCP clients and development.

For `stdio`:

- write only MCP protocol messages to stdout;
- write logs and diagnostics to stderr.

Support Streamable HTTP for remote clients when implemented.

For remote HTTP:

- use HTTPS outside local development;
- require authentication;
- validate request origins;
- apply rate limiting;
- apply request timeouts;
- maintain audit logs;
- expose only approved tools.

## Security

Treat all tool arguments as untrusted input.

Enforce security in code and database permissions, not only in prompts.

Test:

- SQL injection attempts;
- invalid filters;
- invalid sort fields;
- excessive page sizes;
- unauthorized access;
- tenant and user boundaries;
- secret leakage through errors or logs.

## Documentation

Keep these documents up to date:

- CRM filter specification;
- CRM business rules;
- CRM-to-database field mapping;
- MCP tool catalog;
- local setup;
- client configuration;
- Neon permissions;
- security model;
- test strategy;
- deployment instructions.

Do not put real credentials in documentation.

## Required commands

Once tooling exists, document and maintain scripts for:

- dependency installation;
- development start;
- production build;
- type checking;
- linting;
- full tests;
- single-test execution;
- MCP server startup.

## Change discipline

Before implementing a behavior change:

1. identify the corresponding CRM rule;
2. update the specification if required;
3. add or update tests;
4. implement the smallest safe change;
5. run type checking, linting, and tests;
6. review security and query performance.

Keep this repository independent, read-only by default, and limited to documented CRM search behavior.
