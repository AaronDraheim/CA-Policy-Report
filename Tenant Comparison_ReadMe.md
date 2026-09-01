# Microsoft Entra Multi-Tenant Conditional Access Assessment

A read-only PowerShell assessment tool that collects Microsoft Entra Conditional Access policies from multiple tenants, compares policy effects and configurations, and generates a consolidated HTML report plus a lossless JSON export.

The tool uses delegated Microsoft Graph authentication. Tenants are processed in the same top-to-bottom order entered in the tenant input window, and an interactive sign-in is requested for each tenant.

## Features

- Prompts for multiple tenants using the format `Friendly Name | Tenant ID`.
- Processes tenants and presents sign-in prompts in the entered order.
- Uses delegated Microsoft Graph authentication for each tenant.
- Verifies that the connected tenant matches the requested tenant ID.
- Continues processing remaining tenants if one tenant collection fails.
- Retrieves all Conditional Access policies by following Microsoft Graph pagination.
- Queries Microsoft Graph v1.0 and beta, then retains the collection with the larger policy count.
- Deduplicates policies by policy ID without deduplicating display names.
- Groups policies by readable effects such as Block access, Require MFA, Require compliant device, and Require password change.
- Consolidates multiple policies that share the same effect.
- Displays each policy friendly name and policy ID in the comparison matrix.
- Identifies equivalent configurations, state differences, configuration differences, missing effects, multiple matches, and collection failures.
- Creates collapsible tenant policy inventories with raw policy JSON.
- Produces one consolidated HTML report and one consolidated JSON export.

## Prerequisites

- Windows PowerShell 5.1 or PowerShell 7 on Windows.
- Microsoft Graph PowerShell SDK.
- Windows Forms support.
- A work or school account with access to every target tenant.
- An appropriate Microsoft Entra role in every target tenant.
- Administrator consent for required delegated permissions when applicable.

## Required PowerShell Module

```powershell
Install-Module Microsoft.Graph.Authentication -Scope CurrentUser
```

The script uses `Invoke-MgGraphRequest` for policy retrieval, so the Microsoft Graph authentication module is the only declared module dependency.

## Delegated Microsoft Graph Permissions

- `Policy.Read.All`
- `Directory.Read.All`
- `Application.Read.All`
- `RoleManagement.Read.Directory`
- `Agreement.Read.All`

`Policy.Read.All` is the least-privileged permission for listing Conditional Access policies. The remaining scopes support directory-object and policy-context resolution if the script is extended.

## Supported Microsoft Entra Roles

- Global Reader
- Conditional Access Administrator
- Security Reader
- Security Administrator
- Global Secure Access Administrator for standard readable properties

## Tenant Input Format

Enter one tenant per line:

```text
Friendly Name | Tenant ID
```

Example:

```text
Production | 11111111-1111-1111-1111-111111111111
Development | 22222222-2222-2222-2222-222222222222
Customer Lab | 33333333-3333-3333-3333-333333333333
```

Tenants are processed from top to bottom. Interactive sign-in prompts appear in the same order.

## Usage

1. Download or clone the repository.
2. Open PowerShell.
3. Change to the repository directory.
4. Run the script:

```powershell
.\ConditionalAccess-MultiTenantAssessment.ps1
```

5. Enter the friendly name and tenant ID for each target tenant.
6. Select **Run Assessment**.
7. Complete the interactive sign-in for each tenant in the displayed order.
8. Select the output folder.
9. Review the HTML report that opens automatically.

## Authentication Flow

For each tenant, the script:

1. Disconnects the previous Microsoft Graph session.
2. Connects using the requested tenant ID and delegated scopes.
3. Uses process-scoped authentication context.
4. Confirms that the connected tenant ID matches the requested tenant ID.
5. Collects Conditional Access policy data.
6. Records the tenant result and continues to the next tenant.

A collection failure for one tenant does not stop the remaining tenant assessments.

## Output Files

- `ConditionalAccess_MultiTenant_Assessment_yyyyMMdd_HHmmss.html`
- `ConditionalAccess_MultiTenant_Raw_yyyyMMdd_HHmmss.json`

### HTML Report

- Requested tenant count
- Successfully collected tenant count
- Policy count by tenant
- Cross-tenant policy consistency matrix
- Human-readable policy effect and target
- Policy state
- Policy friendly name and ID
- Comparison result
- Collapsible tenant inventories
- Collapsible raw policy JSON

### JSON Export

- Generation timestamp
- Tenant friendly name and ID
- Signed-in account
- Collection status and error
- Complete Conditional Access policy objects
- Normalized policy comparison data

Treat the JSON and HTML output as sensitive tenant configuration data.

## Comparison Logic

Policies are grouped by a human-readable effect key built from:

- Grant control
- Targeted identity type
- Targeted resource type
- User-risk condition
- Sign-in-risk condition

Configuration consistency is evaluated using a normalized signature containing users, groups, roles, applications, user actions, client app types, platforms, locations, risk levels, grant controls, authentication strength, and session controls.

## Comparison Results

### Equivalent effect and configuration

The effect, normalized configuration, and policy state are consistent across collected tenants.

### Same configuration; state differs

The normalized policy configuration is equivalent, but one or more tenants use a different state.

### Same effect; configuration differs

The policies share a high-level effect, but assignments, conditions, controls, authentication strength, or session controls differ.

### Missing in one or more tenants

A comparable policy effect was not identified in one or more successfully collected tenants.

### Multiple distinct policies share this effect

More than one unique policy ID in the same tenant maps to the same effect group. Every distinct policy remains listed by friendly name and policy ID.

### No comparison tenant

Only one tenant was successfully collected, so cross-tenant consistency cannot be established.

## Important Interpretation Notes

The comparison is a static configuration assessment. Equivalent effect labels do not prove identical runtime behavior.

Conditional Access evaluation can also depend on dynamic groups, nested membership, guest configuration, exclusions, application filters, device filters, authentication context, locations, risk, device state, and runtime sign-in properties.

Validate material findings with sign-in logs, Conditional Access insights, and the Conditional Access What If tool before changing production policies.

## Known Limitations

- Point-in-time assessment only.
- Does not evaluate an actual sign-in event.
- Does not expand dynamic or nested group membership.
- Does not normalize tenant-specific object IDs across tenants.
- Policy-effect grouping is heuristic and not a Microsoft Graph runtime evaluation.
- Does not identify emergency-access accounts automatically.
- Licensing is not validated.
- Beta Microsoft Graph APIs can change without v1.0 stability guarantees.
- Does not modify Conditional Access policies.
- Windows Forms requires Windows.

## Security Considerations

- Review all requested delegated scopes before consenting.
- Use the least-privileged account and role appropriate for the assessment.
- Confirm the tenant name before each sign-in.
- Do not publish customer HTML or JSON output to a public repository.
- Store output only in approved locations.
- Redact tenant IDs, accounts, policy IDs, and customer configuration before sharing examples.
- Review differences through formal change control before updating production policies.

## Troubleshooting

### A tenant fails collection

- Confirm the tenant ID.
- Confirm the signed-in account has access to the tenant.
- Confirm `Policy.Read.All` consent.
- Confirm a supported Microsoft Entra role.
- Review the collection error shown in the tenant inventory.

### Fewer policies are returned than expected

- Confirm the signed-in account and tenant ID.
- Confirm `Policy.Read.All` consent.
- Confirm a supported Microsoft Entra role.
- Review both v1.0 and beta behavior.
- Inspect the consolidated raw JSON.

### Conceptually similar policies appear missing

Review differing effect keys, target identity types, target resources, risk conditions, grant controls, states, and tenant-specific identifiers before treating the result as a confirmed gap.

### Windows Forms errors

Run the script on Windows. For cross-platform execution, replace the forms with text or parameter-based input.

## Recommended Validation Workflow

1. Confirm the requested tenant order.
2. Verify the account and tenant during each sign-in.
3. Confirm the policy count for every tenant.
4. Review missing effect groups.
5. Review state differences.
6. Review same-effect configuration differences.
7. Inspect every policy by friendly name and policy ID.
8. Review raw policy JSON.
9. Validate representative scenarios with Conditional Access What If.
10. Review sign-in evidence before implementing changes.
11. Apply changes through formal change control and staged rollout.

## Repository Structure

```text
.
├── ConditionalAccess-MultiTenantAssessment.ps1
├── README.md
└── LICENSE
```

## Contributing

- Preserve read-only behavior by default.
- Use Microsoft Graph instead of deprecated Azure AD modules.
- Request only required permissions.
- Retain unique policy IDs even when display names or effects match.
- Avoid presenting static comparisons as proven runtime behavior.
- Include customer-safe error handling.
- Update this README when permissions, dependencies, comparison logic, or output behavior changes.

## Disclaimer

This sample code is provided for illustration and assessment purposes only and is provided as-is without warranty. It is not supported under a Microsoft standard support program or service.

Test the script and resulting recommendations in a non-production environment. Validate findings against Microsoft documentation, tenant requirements, licensing, sign-in evidence, and formal change-management processes.

## Microsoft Documentation

- [Connect-MgGraph](https://learn.microsoft.com/en-us/powershell/module/microsoft.graph.authentication/connect-mggraph)
- [Microsoft Graph PowerShell authentication commands](https://learn.microsoft.com/en-us/powershell/microsoftgraph/authentication-commands)
- [Microsoft Graph PowerShell documentation](https://learn.microsoft.com/en-us/powershell/microsoftgraph/)
- [List Conditional Access policies](https://learn.microsoft.com/en-us/graph/api/conditionalaccessroot-list-policies)
- [Conditional Access policy resource](https://learn.microsoft.com/en-us/graph/api/resources/conditionalaccesspolicy)
- [Microsoft Graph permissions reference](https://learn.microsoft.com/en-us/graph/permissions-reference)

