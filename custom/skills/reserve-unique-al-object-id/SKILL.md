---
name: reserve-unique-al-object-id
description: 'Reserve a unique AL object ID using AL Object ID Ninja MCP before creating AL objects. Use when: adding new table, page, codeunit, report, xmlport, query, enum, extension object, or permission set; preventing ID collisions; enforcing no hardcoded IDs.'
argument-hint: 'Object type and target file path (example: table c:/repo/src/MyTable.al)'
user-invocable: true
---

# Reserve Unique AL Object ID

Reserves a unique object ID using AL Object ID Ninja MCP and applies it to the new AL object declaration.

## Outcome

Produce a valid, collision-safe object declaration where the object ID is assigned by AL Object ID Ninja MCP, never guessed or hardcoded.

## When to Use

- Creating any new AL object.
- Replacing placeholder IDs in newly scaffolded objects.
- Ensuring team-safe ID allocation in shared ranges.

## Required Inputs

- Object type:
  table, codeunit, page, report, xmlport, query, enum, enumextension, pageextension, tableextension, reportextension, permissionset, permissionsetextension
- Target file path for the AL object.

## Procedure

1. Validate object intent.
- Confirm the object type is supported.
- Confirm target path points to the correct AL app.

2. Reserve ID using MCP.
- Call vjeko-al-objid_getNextObjectId with:
  - objectType: requested object type
  - targetFilePath: full target file path
- If range selection is required, ask user for rangeName and retry.

3. Apply reserved ID.
- Insert the reserved ID into object declaration header.
- Ensure declaration object type matches requested type.
- Ensure object name and file intent stay aligned.

4. Validate result.
- ID appears exactly once in header.
- No placeholder or guessed ID remains.
- Header parses as a valid AL object declaration.

5. Handle edge cases.
- If target file already exists with different ID: ask whether to keep existing object or create new file.
- If reservation fails: surface MCP error and request corrected object type/path or range.
- If object is deleted later: release ID with vjeko-al-objid_unassignObjectId.

6. Confirm completion.
- Report reserved ID, object type, and target path.
- Confirm ID source was AL Object ID Ninja MCP.

## Decision Logic

- Missing object type or path: request missing input before any code change.
- Multiple ranges available: require explicit range selection.
- Existing conflicting declaration: stop and ask whether to replace or abort.

## Quality Criteria

Skill is complete only when all are true:
- ID came from vjeko-al-objid_getNextObjectId.
- No hardcoded fallback ID was used.
- Declaration uses the reserved ID and correct object type.
- User receives explicit confirmation of reserved ID and scope.

## Examples

Input:
- Object type: table
- Target path: c:/dev/al/MyApp/src/CustomerLedgerCache.Table.al

Result:
- Reserved ID from MCP: 50123
- Header begins with: table 50123 "Customer Ledger Cache"

Input:
- Object type: pageextension
- Target path: c:/dev/al/MyApp/src/SalesOrderExt.PageExt.al

Result:
- Reserved ID from MCP: 70102
- Header begins with: pageextension 70102 "Sales Order Ext"
