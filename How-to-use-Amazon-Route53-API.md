1. Follow http://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html to create a new user and obtain API keys

2. Save the downloaded API keys to later use with acme.sh

3. In the user profile, click in Permissions, followed by Add Permissions
4. Then click the 3rd icon "Attach existing policies directly"
5. Click "Create Policy" and in the new window choose "Create Your Own Policy"
6. Enter a name to your policy and paste the following

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "route53:GetHostedZone",
                "route53:ListHostedZones",
                "route53:ListHostedZonesByName",
                "route53:GetHostedZoneCount",
                "route53:ChangeResourceRecordSets",
                "route53:ListResourceRecordSets"
            ],
            "Resource": "*"
        }
    ]
}
```

Validate the policy and Click Create.
Apply the new policy to your new user.

You can now use the new API keys with acme.sh

[https://github.com/Neilpang/acme.sh/tree/master/dnsapi#10-use-amazon-route53-domain-api](https://github.com/Neilpang/acme.sh/tree/master/dnsapi#10-use-amazon-route53-domain-api)

### appendix
If you want to use a much more restrictive AWS policy, use the following:
- http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/access-control-managing-permissions.html
```json
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Action": [
            "route53:ListHostedZones"
         ],
         "Resource": "*"
      },
      {
         "Effect": "Allow",
         "Action": [
            "route53:GetHostedZone",
            "route53:ListResourceRecordSets",
            "route53:ChangeResourceRecordSets"
         ],
         "Resource": "arn:aws:route53:::hostedzone/hosted zone id"
      }
   ]
}
```

