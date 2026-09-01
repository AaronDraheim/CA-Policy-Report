# Microsoft Entra Conditional Access Assessment

A PowerShell-based assessment utility that inventories Microsoft Entra Conditional Access policies and produces an interactive HTML report plus a lossless JSON export.

The report supports configuration review, duplicate-effect analysis, policy-quality observations, and customer-facing assessment discussions. It does not modify Conditional Access policies.

## Features
- Retrieves the complete Conditional Access policy collection through Microsoft Graph pagination. - Compares Microsoft Graph v1.0 and beta policy counts and uses the larger returned collection. - Preserves policies with duplicate display names by retaining each policy ID. - Resolves user, group, application, named-location, directory-role, and Terms of Use identifiers where permitted. - Displays assignments, exclusions, conditions, grant controls, authentication strength, enabled session controls, and raw JSON. - Generates an executive summary, grouped policy-quality findings, advisory coverage observations, and duplicate-effect analysis. - Creates timestamped HTML and JSON output files.

## Prerequisites
- Windows PowerShell 5.1 or PowerShell 7 on Windows. - Microsoft Graph PowerShell SDK. - A work or school account in the target Microsoft Entra tenant. - An appropriate Microsoft Entra role for reading Conditional Access policies. - Administrator consent for delegated permissions when required.

### Required modules

```powershell
Install-Module Microsoft.Graph.Authentication -Scope CurrentUser
Install-Module Microsoft.Graph.Identity.SignIns -Scope CurrentUser
Install-Module Microsoft.Graph.DirectoryObjects -Scope CurrentUser
```

### Delegated permissions
- `Policy.Read.All` - `Directory.Read.All` - `Application.Read.All` - `RoleManagement.Read.Directory` - `Agreement.Read.All`

`Policy.Read.All` is the least-privileged Microsoft Graph permission for listing Conditional Access policies. Additional delegated permissions are used to resolve friendly names for directory objects, service principals, role definitions, and Terms of Use agreements.

### Supported roles
- Global Reader - Conditional Access Administrator - Security Reader - Security Administrator - Global Secure Access Administrator for standard readable properties

## Usage
1. Download or clone the repository. 2. Open PowerShell and change to the repository directory. 3. Run the script:

```powershell
.\ConditionalAccessAssessment.ps1
```
4. Complete the Microsoft Graph interactive sign-in. 5. Select an output folder. 6. Review the HTML report that opens automatically.

## Output
- `ConditionalAccessAssessment_yyyyMMdd_HHmmss.html` — interactive assessment report. - `ConditionalAccessPolicies_Raw_yyyyMMdd_HHmmss.json` — lossless Microsoft Graph policy export.

### Report sections
- Executive summary - Policy-quality findings - Potential coverage observations - Duplicate policy names - Potential duplicate effects - Detailed collapsible policy inventory - Raw policy JSON

## Interpretation

### Policy-quality findings

Findings identify recognizable patterns such as disabled policies, report-only policies, broad assignments without exclusions, legacy client-app conditions, and MFA controls without authentication strength.

### Potential duplicate effects

Duplicate-effect results are based on static configuration comparison. They do not prove that two policies always apply to the same sign-in. Conditional Access evaluation can be affected by group membership, authentication context, device state, risk state, location, exclusions, and runtime sign-in properties.

### Potential coverage observations

Coverage observations are advisory. A missing recognizable policy pattern does not prove that the tenant lacks a control. Validate observations against sign-in logs, role assignments, authentication-method registration, emergency-access design, licensing, group membership, workload requirements, and business requirements.

## Known limitations
- Point-in-time configuration assessment only. - Does not evaluate a specific sign-in event. - Does not replace Conditional Access What If evaluation. - Dynamic and nested group membership is not fully evaluated. - Emergency-access accounts cannot be identified reliably from policy configuration alone. - Licensing is not automatically validated. - Beta Microsoft Graph data can change without v1.0 stability guarantees. - Object-resolution failures fall back to the original object ID. - The script does not make policy changes.

## Security considerations
- Review requested permissions before consenting. - Use the least-privileged account and role appropriate for the assessment. - Treat generated HTML and JSON as sensitive tenant configuration data. - Store outputs only in approved locations. - Do not publish customer output files to a public repository. - Review sign-in evidence before enabling or changing policies.

## Troubleshooting

### Only some policies are returned
- Confirm the tenant ID and signed-in account. - Confirm `Policy.Read.All` consent. - Confirm the account has a supported Microsoft Entra role. - Compare the v1.0 and beta counts shown in the console. - Review the raw JSON export.

### Friendly names are not resolved
- Confirm the additional delegated permissions. - Confirm administrator consent. - Confirm the user can read the corresponding directory object.

### Windows Forms errors

The folder picker requires Windows Forms. Run the script on Windows. For non-Windows environments, replace the folder picker with a text-based output-path parameter.

## Recommended validation workflow
1. Run the assessment and retain the raw JSON. 2. Confirm the Microsoft Graph tenant ID and policy count. 3. Review disabled and report-only policies. 4. Review duplicate-name and duplicate-effect results. 5. Validate exclusions, including emergency-access and workload identities. 6. Review sign-in logs and Conditional Access insights. 7. Use What If evaluation for representative scenarios. 8. Apply changes through formal change control and staged rollout.

## Contributing
- Preserve read-only behavior by default. - Use Microsoft Graph instead of deprecated Azure AD modules. - Request only required permissions. - Avoid presenting static observations as proven runtime gaps. - Include clear error handling and customer-safe output. - Update this README when dependencies, permissions, or report behavior change.

## Disclaimer

This sample is provided for illustration and assessment purposes only and is provided as-is without warranty. Test changes in a non-production environment and validate recommendations against Microsoft documentation, tenant requirements, licensing, and change-management processes.

## Microsoft Documentation
- [Connect-MgGraph](https://learn.microsoft.com/en-us/powershell/module/microsoft.graph.authentication/connect-mggraph) - [Microsoft Graph PowerShell authentication commands](https://learn.microsoft.com/en-us/powershell/microsoftgraph/authentication-commands) - [List Conditional Access policies](https://learn.microsoft.com/en-us/graph/api/conditionalaccessroot-list-policies) - [Get-MgIdentityConditionalAccessPolicy](https://learn.microsoft.com/en-us/powershell/module/microsoft.graph.identity.signins/get-mgidentityconditionalaccesspolicy) - [Microsoft Graph permissions reference](https://learn.microsoft.com/en-us/graph/permissions-reference) - [Conditional Access What If evaluation](https://learn.microsoft.com/en-us/graph/api/conditionalaccessroot-evaluate)

