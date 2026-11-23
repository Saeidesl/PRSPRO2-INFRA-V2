# TASK-2.8-FRONTEND-SESSION-PERMISSION-AND-IMAGE-CALLER-TRACE-INCREMENT-85

Date: 2026-11-23
Evidence level: E1 source inspection
Reference repository: PRSPRO2-PRODUCTION
No runtime tests, production writes, migrations or deployment.

## Additional caller/source files
- js/core/router.js SHA 2aa3bec548fc424bc35815a9b53ec7e5f51e9487: permission lookup endpoint caller /auth/me/permissions.
- js/core/permissions.js SHA 8fe1b343e73b505573e4cce5bdaae114928b63f6 and js/core/session.js SHA fc756b3d6a6c416bb6845d9dae643d3b03f9bd0f: inspected bounded file content; no literal endpoint calls in regex sample.
- js/modules/auth.js SHA b089a14a510ea53df70539b8b0cde5c47fd2c803: login/logout/current-user endpoint families.
- js/modules/information-types.js SHA 6c2dee5ba5eb160390d52a9e53bdfd9c12d2349d: information types and column endpoint families.
- js/modules/project-images.js SHA 58ad2388ffd2e5e8267ee45bc435cf441d647181: project and attachment caller families.

## Observations
- Permission/session front-end flow is distributed between API/session/router/permissions modules and auth module; complete token lifecycle and role cache invalidation were not traced.
- Image caller paths overlap with attachment and project modules, so V2 should unify API and storage attachment abstractions rather than create parallel endpoints.
- No confirmed source-level mismatch from sampled endpoints; route permission contract and complete caller coverage remain unfinished.

## V2 requirements
1. Centralized authentication/session state and typed permission query.
2. Server-authoritative authorization; client permission UI only for presentation.
3. Single attachment/image API abstraction and storage lifecycle.
4. Automated session expiry, role-change/cache invalidation and unauthorized request tests.
E1 only; no tests, G-01 blocker remains; no production changes.
