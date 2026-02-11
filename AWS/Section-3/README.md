# AWS IAM (Identity and Access Management)

## Root Account
- Root account = Only for setup, don't use for daily operations
- Root user must have MFA enabled
- For IAM users, MFA is optional

## Users and Groups
- Users can be organized into groups
- Policies can be attached to users, groups, or roles
- **Inline policy** = Policy attached directly to a single user

## Policies
Policies define permissions and contain **Statements** with the following components:
- **Statement ID** = Unique identifier for the statement
- **Effect** = Allow or Deny
- **Principal** = Who the policy applies to
- **Action** = What actions are allowed/denied
- **Resource** = Which resources the policy applies to
- **Condition** = Optional conditions for when the policy applies

## AWS Access Methods
Three ways to sign in to AWS:
1. **Console UI** = Web-based interface
2. **AWS CLI** = Command-line interface
3. **AWS SDK** = Software development kit

## AWS CloudShell
- Available when already logged in to AWS
- Terminal is already authenticated
- 1 GB persistent home directory
- Upload and download files capability

## Roles
- Give permissions to AWS entities by attaching policies
- Used for temporary access and cross-service permissions

## Security Tools

### 1. AWS Credentials Report
- Account-level report
- Shows status of various credentials

### 2. IAM Access Advisor
- Shows who has what permissions
- Displays when permissions were last used
- Helps revoke unused permissions to follow least privilege principle

## Billing Access
- IAM users, even administrators, don't have access to billing information by default
- Must be manually enabled by root account

## AWS Budgets

### Budget Types
1. **Zero Spend Budget** = Alert when any spending occurs
2. **Monthly Cost Budget** = Track monthly spending

### Notifications
You get notifications at:
- 85% of budget
- 100% of budget
- Forecasted to reach 100%
