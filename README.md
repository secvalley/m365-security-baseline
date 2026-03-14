# M365 Security Baseline

[![MIT License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/secvalley/m365-security-baseline?style=social)](https://github.com/secvalley/m365-security-baseline/stargazers)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/secvalley/m365-security-baseline/pulls)

A practical, no-nonsense security hardening baseline for Microsoft 365 tenants. Each control is something you can actually go and do today.

Built and maintained by [SecValley](https://secvalley.com) - because "we'll get to it later" is not a security strategy.

---

## How to Use This

Work through each section and tick off what you've implemented. Controls are tagged by severity:

- **Critical** - Fix this now. Seriously.
- **High** - Should be done within a sprint or two.
- **Medium** - Important, but won't ruin your weekend if it waits a bit.
- **Low** - Nice hardening. Good hygiene.

Not every control will apply to every organization. Read the "why" before flipping a switch, especially in large tenants where a misconfigured transport rule can ruin a lot of people's mornings.

---

## Table of Contents

- [Exchange Online](#exchange-online)
- [SharePoint and OneDrive](#sharepoint-and-onedrive)
- [Microsoft Teams](#microsoft-teams)
- [Tenant-Wide Settings](#tenant-wide-settings)
- [Going Further](#going-further)
- [Related Projects](#related-projects)
- [License](#license)

---

## Exchange Online

- [ ] **[Critical]** Enable multi-factor authentication for all mailbox users
  Credential stuffing against mailboxes is still one of the most common initial access vectors. MFA should be enforced through Conditional Access, not per-user MFA settings, so you get proper reporting and policy flexibility.
  [Microsoft Docs - Conditional Access and MFA](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-mfa-howitworks)

- [ ] **[Critical]** Disable legacy authentication protocols
  Protocols like POP3, IMAP, and SMTP AUTH do not support modern authentication. Attackers love them because MFA doesn't apply. Block them via authentication policies or Conditional Access.
  [Microsoft Docs - Disable Basic Authentication](https://learn.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online)

- [ ] **[Critical]** Configure anti-phishing policies with mailbox intelligence
  Turn on mailbox intelligence, spoof intelligence, and impersonation protection in Microsoft Defender for Office 365. Set actions to quarantine rather than just adding a safety tip.
  [Microsoft Docs - Anti-phishing Policies](https://learn.microsoft.com/en-us/defender-office-365/anti-phishing-policies-about)

- [ ] **[High]** Enable audit logging for all mailboxes
  Mailbox audit logging is on by default since 2019, but verify it hasn't been disabled. You need these logs for incident response. Check that owner, delegate, and admin actions are all being captured.
  [Microsoft Docs - Mailbox Auditing](https://learn.microsoft.com/en-us/purview/audit-mailboxes)

- [ ] **[High]** Configure DKIM and DMARC for all accepted domains
  SPF alone is not enough. Enable DKIM signing for every domain, then publish a DMARC record. Start with `p=none` and monitor, then move to `p=quarantine` or `p=reject` once you're confident.
  [Microsoft Docs - DKIM](https://learn.microsoft.com/en-us/defender-office-365/email-authentication-dkim-configure)

- [ ] **[High]** Block auto-forwarding rules to external domains
  Attackers frequently set up mail forwarding rules to exfiltrate data after compromising a mailbox. Use a mail flow rule to block external auto-forwarding, or set it via the outbound anti-spam policy.
  [Microsoft Docs - Outbound Spam Filtering](https://learn.microsoft.com/en-us/defender-office-365/outbound-spam-policies-configure)

- [ ] **[High]** Enable Safe Attachments and Safe Links (Defender for Office 365)
  Safe Attachments detonates files in a sandbox before delivery. Safe Links rewrites URLs and checks them at click time. Both should be enabled org-wide with policies covering all recipients.
  [Microsoft Docs - Safe Attachments](https://learn.microsoft.com/en-us/defender-office-365/safe-attachments-about)

- [ ] **[Medium]** Restrict who can create and manage mail flow (transport) rules
  Transport rules can silently redirect, copy, or delete mail. Limit the Exchange admin role and regularly audit existing transport rules for anything unexpected.
  [Microsoft Docs - Mail Flow Rules](https://learn.microsoft.com/en-us/exchange/security-and-compliance/mail-flow-rules/mail-flow-rules)

- [ ] **[Medium]** Disable SMTP AUTH submission globally, enable per-mailbox only where needed
  SMTP AUTH is the last remaining basic auth vector for many tenants. Disable it at the org level and only enable it for specific service accounts that genuinely need it (like printers or LOB apps).
  [Microsoft Docs - Enable or Disable SMTP AUTH](https://learn.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/authenticated-client-smtp-submission)

- [ ] **[Low]** Configure external sender tagging
  Mark emails from external senders with a visual indicator in Outlook. It's a small thing, but it helps users spot phishing and impersonation attempts, especially when the display name matches an internal contact.
  [Microsoft Docs - External Email Tagging](https://learn.microsoft.com/en-us/exchange/mail-flow-best-practices/remote-domains/remote-domains)

---

## SharePoint and OneDrive

- [ ] **[Critical]** Restrict external sharing to authenticated guests only
  The default sharing settings in many tenants are far too permissive. At minimum, disable anonymous "Anyone" links. Require guests to authenticate, and consider limiting sharing to guests in specific domains.
  [Microsoft Docs - Sharing Settings](https://learn.microsoft.com/en-us/sharepoint/turn-external-sharing-on-or-off)

- [ ] **[Critical]** Block downloads from unmanaged devices
  Use Conditional Access app-enforced restrictions to prevent users on personal or unmanaged devices from downloading files from SharePoint and OneDrive. Allow browser-only access if needed.
  [Microsoft Docs - Control Access from Unmanaged Devices](https://learn.microsoft.com/en-us/sharepoint/control-access-from-unmanaged-devices)

- [ ] **[High]** Enable sensitivity labels for documents and sites
  Sensitivity labels let you classify and protect content based on its sensitivity. Apply labels to documents automatically or manually, and use them to enforce encryption, watermarking, and access controls.
  [Microsoft Docs - Sensitivity Labels](https://learn.microsoft.com/en-us/purview/sensitivity-labels)

- [ ] **[High]** Set default sharing link type to "Specific People"
  When users click "Share," the default link type matters a lot. Set it to "Specific People" (direct share) rather than "Anyone with the link" or "People in your organization." Fewer accidental over-shares this way.
  [Microsoft Docs - Default Sharing Link Type](https://learn.microsoft.com/en-us/sharepoint/change-default-sharing-link)

- [ ] **[High]** Configure expiration and permissions for guest access
  Guest access should not be indefinite. Set expiration policies for guest accounts and review guest access regularly. Require guests to re-authenticate periodically.
  [Microsoft Docs - Guest Expiration](https://learn.microsoft.com/en-us/entra/external-id/external-identities-overview)

- [ ] **[High]** Enable versioning on document libraries
  Versioning provides a recovery path when files are corrupted, overwritten, or hit by ransomware. Enable it on all document libraries and set a reasonable version limit (50-100 major versions is usually fine).
  [Microsoft Docs - Versioning](https://learn.microsoft.com/en-us/sharepoint/governance/versioning-policy)

- [ ] **[Medium]** Restrict site creation to specific users or groups
  By default, all users can create SharePoint sites and Microsoft 365 groups. This leads to sprawl and ungoverned data stores. Limit site creation to IT or designated users and establish a request process.
  [Microsoft Docs - Manage Site Creation](https://learn.microsoft.com/en-us/sharepoint/manage-site-creation)

- [ ] **[Medium]** Disable legacy SharePoint authentication workflows
  Some older SharePoint authentication methods bypass modern auth controls. Review and disable any custom authentication providers and ensure all access flows through Entra ID with Conditional Access enforced.
  [Microsoft Docs - SharePoint Authentication](https://learn.microsoft.com/en-us/sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts)

- [ ] **[Medium]** Review and restrict SharePoint app-only access
  App-only permissions (via Azure AD app registrations or SharePoint Add-ins) can have broad access to site content without user context. Audit app registrations that have Sites.Read.All or Sites.FullControl.All permissions.
  [Microsoft Docs - App-Only Access](https://learn.microsoft.com/en-us/sharepoint/dev/solution-guidance/security-apponly-azuread)

- [ ] **[Low]** Configure idle session timeout for SharePoint
  If a user walks away from their browser while viewing sensitive documents, you want the session to expire. Set idle session sign-out policies, especially for unmanaged device scenarios.
  [Microsoft Docs - Idle Session Timeout](https://learn.microsoft.com/en-us/sharepoint/sign-out-inactive-users)

---

## Microsoft Teams

- [ ] **[Critical]** Restrict external access to approved domains only
  By default, Teams allows federation with all external tenants. Lock this down to only the partner domains you actually work with, or disable it entirely and use guest access instead.
  [Microsoft Docs - External Access](https://learn.microsoft.com/en-us/microsoftteams/manage-external-access)

- [ ] **[Critical]** Control guest access permissions in Teams
  If guest access is enabled, make sure guests can't create or delete channels, add or remove apps, or share files without oversight. Review guest permissions in Teams admin center and apply the principle of least privilege.
  [Microsoft Docs - Guest Access](https://learn.microsoft.com/en-us/microsoftteams/guest-access)

- [ ] **[High]** Restrict which apps users can install
  The Teams app store is open by default. Users can install third-party apps that may access organizational data. Block all third-party apps by default and allow only vetted apps through an approval process.
  [Microsoft Docs - App Permission Policies](https://learn.microsoft.com/en-us/microsoftteams/app-permissions)

- [ ] **[High]** Configure meeting policies to prevent anonymous join
  Anonymous users joining meetings is a data leak risk. Disable anonymous join for meetings, or at minimum require organizer approval in the lobby. Also consider disabling dial-in bypass for the lobby.
  [Microsoft Docs - Meeting Policies](https://learn.microsoft.com/en-us/microsoftteams/meeting-policies-participants-and-guests)

- [ ] **[High]** Disable email integration for channels if not needed
  Each Teams channel can have an email address for inbound mail. If nobody is using this feature, disable it. It's another ingress point for phishing or spam content that bypasses Exchange transport rules.
  [Microsoft Docs - Email Integration](https://learn.microsoft.com/en-us/microsoftteams/enable-features-office-365)

- [ ] **[High]** Review and restrict who can create teams and private channels
  Uncontrolled team creation leads to data sprawl and shadow IT. Use Microsoft 365 group creation restrictions to limit who can spin up new teams, and have a process for private channel requests.
  [Microsoft Docs - Team Creation](https://learn.microsoft.com/en-us/microsoftteams/manage-teams-in-modern-portal)

- [ ] **[Medium]** Enforce meeting recording storage and access controls
  Meeting recordings land in OneDrive or SharePoint. Make sure the storage location is governed, recordings inherit site permissions, and retention policies apply so recordings don't live forever.
  [Microsoft Docs - Meeting Recording](https://learn.microsoft.com/en-us/microsoftteams/meeting-recording)

- [ ] **[Medium]** Configure data loss prevention policies for Teams chat
  DLP policies can detect and block sensitive data (credit card numbers, personal IDs, health records) from being shared in Teams chats and channels. Extend your existing DLP policies to cover Teams.
  [Microsoft Docs - DLP for Teams](https://learn.microsoft.com/en-us/purview/dlp-microsoft-teams)

- [ ] **[Medium]** Restrict file sharing in Teams chats to managed domains
  By default, users can share files in 1:1 and group chats. If combined with external access, files could end up in the wrong hands. Review your file sharing policies and restrict where needed.
  [Microsoft Docs - Messaging Policies](https://learn.microsoft.com/en-us/microsoftteams/messaging-policies-in-teams)

- [ ] **[Low]** Disable third-party cloud storage integration
  Teams can integrate with Dropbox, Google Drive, Box, and other cloud storage providers. If your organization standardizes on OneDrive/SharePoint, disable the other providers to prevent data from leaking into unmanaged storage.
  [Microsoft Docs - Cloud Storage Settings](https://learn.microsoft.com/en-us/microsoftteams/enable-features-office-365)

---

## Tenant-Wide Settings

- [ ] **[Critical]** Enable Security Defaults or implement a Conditional Access baseline
  If you don't have Entra ID P1/P2, enable Security Defaults at minimum. If you do, build a Conditional Access baseline that enforces MFA, blocks legacy auth, requires compliant devices for sensitive apps, and defines named locations.
  [Microsoft Docs - Security Defaults](https://learn.microsoft.com/en-us/entra/fundamentals/security-defaults)

- [ ] **[Critical]** Enable Unified Audit Log and set retention to maximum
  The Unified Audit Log is your single most important forensic data source in M365. Verify it's enabled, increase retention (default is 180 days, E5 gets 365 days), and consider exporting logs to a SIEM for longer retention.
  [Microsoft Docs - Audit Log](https://learn.microsoft.com/en-us/purview/audit-solutions-overview)

- [ ] **[Critical]** Restrict Global Administrator count and enforce PIM
  You should have no more than 2-4 Global Admins, and ideally they should use Privileged Identity Management (PIM) for just-in-time activation. Break-glass accounts are an exception, but those should be monitored closely.
  [Microsoft Docs - PIM](https://learn.microsoft.com/en-us/entra/id-governance/privileged-identity-management/pim-configure)

- [ ] **[High]** Disable user consent to third-party applications
  By default, users can grant third-party apps access to organizational data. This is a major OAuth phishing vector. Disable user consent and require admin approval for all app registrations.
  [Microsoft Docs - User Consent](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/configure-user-consent)

- [ ] **[High]** Enable Microsoft Secure Score monitoring
  Secure Score gives you a measurable baseline and improvement roadmap. Review it regularly, assign improvement actions to owners, and track progress over time. It's not perfect, but it's a solid starting point.
  [Microsoft Docs - Secure Score](https://learn.microsoft.com/en-us/defender/microsoft-365-defender/microsoft-secure-score)

- [ ] **[High]** Configure alerts for high-risk sign-ins and risky users
  Set up alert policies in the Microsoft 365 Defender portal for impossible travel, sign-ins from anonymous IP addresses, leaked credentials, and other risk detections. Don't just enable them, make sure someone is actually watching.
  [Microsoft Docs - Risk Detections](https://learn.microsoft.com/en-us/entra/id-protection/concept-identity-protection-risks)

- [ ] **[High]** Enforce named locations and block sign-ins from high-risk countries
  If your organization only operates in certain geographies, there is no reason to allow sign-ins from everywhere. Define named locations and create a Conditional Access policy to block or require extra verification for unexpected regions.
  [Microsoft Docs - Named Locations](https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-assignment-network)

- [ ] **[Medium]** Configure data retention and deletion policies
  Define retention policies for Exchange, SharePoint, OneDrive, and Teams. Data should not live forever, and you need clear policies for both retention (for compliance) and deletion (for minimizing exposure).
  [Microsoft Docs - Retention Policies](https://learn.microsoft.com/en-us/purview/retention)

- [ ] **[Medium]** Review and clean up stale guest accounts quarterly
  Guest accounts accumulate over time and are often forgotten. Set up a recurring access review in Entra ID Governance, or at minimum run a quarterly PowerShell script to find and remove guests who haven't signed in recently.
  [Microsoft Docs - Access Reviews](https://learn.microsoft.com/en-us/entra/id-governance/access-reviews-overview)

- [ ] **[Medium]** Enable customer lockbox
  Customer Lockbox gives you approval control when Microsoft support engineers need to access your tenant data during a service request. It's an E5 feature, but if you have the license, turn it on.
  [Microsoft Docs - Customer Lockbox](https://learn.microsoft.com/en-us/purview/customer-lockbox-requests)

- [ ] **[Low]** Disable self-service license assignments and trials
  Users can sign up for trial licenses and self-service purchases by default. This creates unmanaged workloads and data stores that IT doesn't know about. Disable self-service purchasing via PowerShell.
  [Microsoft Docs - Self-Service Purchase](https://learn.microsoft.com/en-us/microsoft-365/commerce/subscriptions/manage-self-service-purchases-admins)

---

## Going Further

This baseline covers the essentials. For continuous, automated M365 security assessment with 400+ controls, attack path analysis, and compliance mapping, check out [SecValley CSPM](https://secvalley.com/cspm.html).

---

## Related Projects

- [cloud-security-checklist](https://github.com/secvalley/cloud-security-checklist) - Broad cloud security checklist covering AWS, Azure, and GCP
- [entra-id-security-checklist](https://github.com/secvalley/entra-id-security-checklist) - Dedicated Entra ID (Azure AD) hardening guide

---

## Contributing

Found a control that's missing, outdated, or just plain wrong? Open a PR or file an issue. Security is a moving target, and this document benefits from community input.

Please keep contributions practical - every control should be something an admin can actually implement, not a theoretical best practice nobody follows.

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.

---

Maintained by [SecValley](https://secvalley.com)
