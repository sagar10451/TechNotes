# AWS Account Management and Security

## AWS Organizations

Manage multiple AWS accounts centrally

### Features
- Global service
- Consolidated billing (one bill for all accounts)
- Organize accounts in OUs (Organizational Units)
- Apply SCPs (Service Control Policies) to restrict permissions
- Automate account creation
- Enforce tagging for billing

### Management Account and Member Account
- **Management Account:** Main account, no SCP applies to management account
- Quick member account creation via API or invitation
- An AWS account can only belong to one organization at a time

### Organizational Structure
- Root OU = management account
- Can create OUs within OUs (nested OUs)

### SCP (Service Control Policies)
IAM policy attached to OUs or accounts to restrict users and roles

## IAM Conditions

### Key Condition Types

**1. Network/Location:**
- `aws:SourceIp`: Restrict by IP/CIDR
- `aws:RequestedRegion`: Restrict by AWS region

**2. Tag-Based:**
- `ec2:ResourceTag`: Restrict action based on resource tags
- `aws:PrincipalTag`: Restrict based on user/role tags

**3. Security/Org:**
- `aws:MultiFactorAuthPresent`: Enforce required MFA for sensitive actions
- `aws:PrincipalOrgID`: Allow only accounts in specific org

**4. S3 ARN Distinction:**
- Bucket level: `/bucket`
- Object level: `/bucket/*`

## IAM Policy Evaluation Logic

### IAM Permission Boundaries
- Only for users and roles, not for groups
- Max permission a user/role can get
- Example:
  - Boundary = S3*, CloudWatch*, EC2*
  - IAM permission: iam:CreateUser => Invalid

### Evaluation Order
1. Explicit deny = immediate deny
2. SCP (must be allowed by SCP)
3. Resource-based policy = resource must allow
4. Identity-based policy: user/role policy must allow
5. Permission boundary

### Final Decision
1. Allow only if there is no explicit deny and allowed by all policies
2. Explicit deny = overrides allow
3. Action neither allowed nor denied = denied

## AWS IAM Identity Centre

Single sign-on service for:
- Multiple AWS accounts
- Business applications (Salesforce, Office 365, etc.)

### Benefits
- One login for all AWS accounts
- Centralized user management
- Integrates with Active Directory, Okta, Azure AD

### ABAC (Attribute-Based Access Control)
- Instead of creating separate policies for each user, you use tags/attributes
- Example: Allow access to resources tagged `Department=Finance`

### Permission Sets
IAM policies in IAM Identity Center

## AWS Directory Service

### 3 Types

**1. AWS Managed Microsoft Directory**
- Way to create Microsoft AD on AWS
- Two-way trust with on-prem
- Supports MFA

**2. AD Connector**
- Just forwards auth requests to on-prem AD
- No user data stored in AWS
- Supports MFA
- When you want to use your existing on-prem AD, use AD Connector

**3. Simple AD**
- Doesn't support MFA
- Cannot integrate with on-prem AD
- Used for very simple tasks

### How to Integrate IAM Identity Centre with Active Directory?
1. AWS Managed Microsoft AD
2. External Identity Provider (SAML)

## AWS Control Tower

Automated setup for multi-account AWS environment with best practices

### What It Does
- Auto-creates AWS Organizations structure
- Sets up OUs (Security, Sandbox, etc.)
- Applies guardrails (SCPs + AWS Config rules)
- Enables centralized logging (CloudTrail, Config)
- Account factory for quick account creation
- Compliance dashboard

### Guardrails
- **Preventive (SCPs):** Block actions before they occur
- **Detective (Config):** Detect violations and remediate them if configured with AWS Config

### Use Case
Quickly set up secure, compliant multi-account environment without manual configuration

### Built On
AWS Organizations + SCPs + Config + CloudTrail + IAM Identity Center
