**Note**: This public repo contains the documentation for the private GitHub repo <https://github.com/gruntwork-io/module-aws-monitoring>.
We publish the documentation publicly so it turns up in online searches, but to see the source code, you must be a Gruntwork customer.
If you're already a Gruntwork customer, the original source for this file is at: <https://github.com/gruntwork-io/module-aws-monitoring/blob/master/modules/alarms/route53-health-check-alarms/README.md>.
If you're not a customer, contact us at <info@gruntwork.io> or <http://www.gruntwork.io> for info on how to get access!

# Route 53 Health Check Module

This module creates a [Route 53 Health Check](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover.html)
that monitors your website on a regular interval and triggers a CloudWatch alarm if your site goes down. This provides
basic *availability level* monitoring for your website (see [monitoring levels](http://www.hello-startup.net/resources/monitoring/)
for more info). Note: this sort of monitoring only works for *public-facing* endpoints.

**NOTE**: Route 53 only sends CloudWatch metrics to `us-east-1`. Therefore, we must create the health check and alarm in
that region. Moreover, the SNS Topic ARNs you pass in must ALSO be in `us-east-1`. Otherwise, you'll get the error
`Invalid region us-XXXX-Y specified. Only us-east-1 is supported.` For more info, see:
https://github.com/hashicorp/terraform/issues/7371.

## How do you use this module?

Check out the [examples/route53-health-check-alarms example](/examples/route53-health-check-alarms).

## How do I get notifications from these alarms?

One of the parameters you pass to this module is a list of [SNS](https://aws.amazon.com/sns/) topic ARNs to notify when
the website goes down. Here is how to configure an SNS topic:

1. Create an SNS topic using the Terraform [aws_sns_topic](https://www.terraform.io/docs/providers/aws/r/sns_topic.html) resource.
2. Pass the topic's ARN to this module (e.g. `alarm_sns_topic_arns = "${aws_sns_topic.my_topic.arn}"`)
3. Login to the [SNS console](https://console.aws.amazon.com/sns/v2/home).
4. Click the "Topics" link in the menu on the left.
5. Find your topic in the list and click the checkbox next to it.
6. Click the "Actions" button and select "Subscribe to Topic".
7. Choose "Email" or "SMS Message" as the protocol, enter your email or phone number, and click "Create Subscription".
8. AWS will email or message you to confirm the subscription. Be sure to confirm it, or you won't receive any
   notifications, and the alarm will report its status as `INSUFFICIENT_DATA`!