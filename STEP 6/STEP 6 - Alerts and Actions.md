It is the APM app that integrates with Kibana to give alerts and then do further actions on it. APM can visualize comprehensive performance information on deployed services, can identify and analyze errors, and monitor host-level and agent-specific metrics like JVM and Go runtime metrics.
1. Choose APM under Observability on left navigation menu.
2. Choose Alerts -> Error Count -> Create Threshold Alert
3. Based on criteria needed we shall fill details for alert. 
      Check every - 1 Second
      IS ABOVE - 0 errors
      FOR THE LAST - 1 minute

4. Then move onto add connector. Choose Slack. We shall need SLACK Webhook so make a SLACK. 
5. Login to your slack account as administrator.
6. Then go to https://my.slack.com/services/new/incoming-webhook
7. Now select a default channel for the integration.
8. Click Add Incoming Webhook Integration.
9. Copy the generated webhook URL so you can paste it into your Slack connector form.
10. Fill in the Connector Name and URL obtained and hit Save and Test.

