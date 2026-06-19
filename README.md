The Terraform module is used by the ITGix AWS Landing Zone - https://itgix.com/itgix-landing-zone/

# AWS CIS Alarms Terraform Module

This module deploys CloudWatch log metric filters and metric alarms based on CIS-style CloudTrail controls, including built-in SNS email notifications.

Part of the [ITGix AWS Landing Zone](https://itgix.com/itgix-landing-zone/).

## Resources Created

- CloudWatch Log Metric Filters for CIS-style security controls
- CloudWatch Metric Alarms for each enabled control
- SNS topic for alarm notifications
- SNS email subscriptions from the provided recipient list
- Optional random name prefix resource

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| `create` | Whether to create CloudWatch log metric filters and metric alarms | `bool` | `true` | no |
| `use_random_name_prefix` | Whether to prefix resource names with a random prefix | `bool` | `false` | no |
| `name_prefix` | Name prefix for alarms and SNS topic (ignored when random prefix is enabled) | `string` | `""` | no |
| `control_overrides` | Map of per-control overrides (for thresholds, periods, actions, etc.) | `any` | `{}` | no |
| `disabled_controls` | List of control IDs to disable | `list(string)` | `[]` | no |
| `namespace` | CloudWatch metric namespace for generated metrics and alarms | `string` | `"CISBenchmark"` | no |
| `log_group_name` | CloudWatch log group name that receives CloudTrail logs | `string` | `""` | no |
| `alert_emails` | Email addresses subscribed to the module-managed SNS topic | `list(string)` | `["aws-landing-zones@itgix.com"]` | no |
| `alarm_actions` | Optional explicit alarm action ARNs; when empty, module SNS topic ARN is used | `list(string)` | `[]` | no |
| `actions_enabled` | Whether alarm actions are executed during state changes | `bool` | `true` | no |
| `tags` | Tags applied to resources | `map(string)` | `{}` | no |
| `ok_actions` | Optional CloudWatch OK action ARNs | `list(string)` | `[]` | no |
| `insufficient_data_actions` | Optional CloudWatch insufficient data action ARNs | `list(string)` | `[]` | no |

## Outputs

| Name | Description |
|------|-------------|
| `cloudwatch_metric_alarm_arns` | Map of ARNs for created CloudWatch metric alarms by control name |
| `cloudwatch_metric_alarm_ids` | Map of IDs for created CloudWatch metric alarms by control name |

## Usage Example

```hcl
module "cis_alarms" {
	source = "path/to/tf-module-aws-cis-alarms"

	log_group_name = "/aws/cloudtrail/itgix-landing-zones"
	name_prefix    = "itgix-"

	alert_emails = [
		"aws-landing-zones@itgix.com",
		"security@example.com"
	]

	disabled_controls = [
		"NoMFAConsoleSignin",
		"ConsoleSigninFailures"
	]

	tags = {
		Environment = "security"
		ManagedBy   = "terraform"
	}
}
```