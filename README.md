# Switch to Yoki API

I created [Yoki](https://github.com/boroivanov/yoki) a supplementary ECS API. It utilizes the same idea as ecs-slack-notifications but it also provides much better functionality and notifications. Please consider using Yoki going forward. Thanks.

# ecs-slack-notifications (DEPRECIATED)
**No further development will be done on this app. See note above.**

Based on the AWS example for handling ECS events https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_cwet_handling.html

# Installation
1. Requires changes from https://github.com/serverless/serverless/pull/4694. Install serverless 1.27.0 or higher.
```bash
npm install -g serverless@^1.27.0
```

2. Install python requirements serverless plugin in the app directory.
```bash
npm install serverless-python-requirements
npm install serverless-dynamodb-autoscaling
```

3. Create a Slack application and copy the token. The app needs the following permission scopes:
- `chat:write`

OR:
- `channels:read`
- `chat:write:bot`

4. Create `.env.yml` in the app directory
```bash
$ cat .env.yml
lambda:
  environment:
    SLACK_API_TOKEN: "xoxa-11111111111-1111111111111-1111111111111-abcd3abcd3abcd3abcd3abcd3abcd3123"
    SLACK_VERIFICATION_TOKEN: "asdf1234asdf"
    SLACK_CHANNEL: "ecs-notifications"
    INCLUDED_CLUSTERS: "all"  # notifications for all clusters
    SERVICE_GROUPS_TABLE: "ecs-slack-ServiceGroups"

```

5. Install the app on aws
```bash
sls deploy
```

6. Create Slack Deploy Slash Command (optional)
Create a slash command `/ecs-deploy` in the Slack app. Set the `Request URL` to the API Gateway created by serverless.
  - Go to API Gateway and select dev-ecs-slack
  - Under stages get the `Invoke URL` from POST method under /deploy

7. Configure service groups (optional)
Create items in dynamodb table `ecs-slack-ServiceGroups` to configure service groups. Example:
```json
{
  "group": "myapp",
  "services": [
    "myapp",
    "myapp-worker-1",
    "myapp-worker-2"
  ]
}
```
Then to trigger a deployment in slack run: `/ecs-deploy <cluster_name> myapp <reference> -g`