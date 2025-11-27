Cybr - Introduction to AWS IAM Enumeration
[https://cybr.com/hands-on-labs/lab/introduction-to-aws-iam-enumeration/](https://cybr.com/hands-on-labs/lab/introduction-to-aws-iam-enumeration/)
# Manual Way
## Initial keys
```
Access key: AK**********IXU2
Sectret key: Yp*************************TTZ
Username:introduction-to-aws-iam-enumeration-1764162720681-Joel
```
## 1. config the account
```
└─$ aws sts get-caller-identity --profile iam
{
    "UserId": "AIDAZAA2SOBEC35BHZ7CT",
    "Account": "618531024968",
    "Arn": "arn:aws:iam::618531024968:user/introduction-to-aws-iam-enumeration-1764162720681-Joel"
}

```
## 2. list-users
```
└─$ aws iam list-users --profile iam         
{
    "Users": [
        {
            "Path": "/",
            "UserName": "introduction-to-aws-iam-enumeration-1764162720681-Chris",
            "UserId": "AIDAZAA2SOBEGE2ZDTFHQ",
            "Arn": "arn:aws:iam::618531024968:user/introduction-to-aws-iam-enumeration-1764162720681-Chris",
            "CreateDate": "2025-11-26T13:12:07+00:00"
        },
        {
            "Path": "/",
            "UserName": "introduction-to-aws-iam-enumeration-1764162720681-Joel",
            "UserId": "AIDAZAA2SOBEC35BHZ7CT",
            "Arn": "arn:aws:iam::618531024968:user/introduction-to-aws-iam-enumeration-1764162720681-Joel",
            "CreateDate": "2025-11-26T13:12:05+00:00"
        },
        {
            "Path": "/",
            "UserName": "introduction-to-aws-iam-enumeration-1764162720681-Mary",
            "UserId": "AIDAZAA2SOBEAPCC2XH3D",
            "Arn": "arn:aws:iam::618531024968:user/introduction-to-aws-iam-enumeration-1764162720681-Mary",
            "CreateDate": "2025-11-26T13:12:07+00:00"
        },
        {
            "Path": "/",
            "UserName": "introduction-to-aws-iam-enumeration-1764162720681-Mike",
            "UserId": "AIDAZAA2SOBEMWHKRCCGF",
            "Arn": "arn:aws:iam::618531024968:user/introduction-to-aws-iam-enumeration-1764162720681-Mike",
            "CreateDate": "2025-11-26T13:12:05+00:00"
        }
    ]
}

```
## 3. list-user-policies
```
└─$ aws iam list-user-policies --user-name introduction-to-aws-iam-enumeration-1764162720681-Joel --profile iam
{
    "PolicyNames": [
        "AllowEnumerateRoles"
    ]
}
   
   
```
## 4. get the policy
```
└─$ aws iam get-user-policy --policy-name AllowEnumerateRoles --user-name introduction-to-aws-iam-enumeration-1764162720681-Joel --profile iam
{
    "UserName": "introduction-to-aws-iam-enumeration-1764162720681-Joel",
    "PolicyName": "AllowEnumerateRoles",
    "PolicyDocument": {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": [
                    "iam:GetRole",
                    "iam:GetRolePolicy",
                    "iam:ListRoles",
                    "iam:ListRolePolicies"
                ],
                "Resource": "*",
                "Effect": "Allow"
            }
        ]
    }
}

```
## 5. groups
```
└─$ aws iam list-groups --profile iam                                                                                                         
{
    "Groups": [
        {
            "Path": "/",
            "GroupName": "introduction-to-aws-iam-enumeration-1764162720681-Developers",
            "GroupId": "AGPAZAA2SOBEGKNHJBEW4",
            "Arn": "arn:aws:iam::618531024968:group/introduction-to-aws-iam-enumeration-1764162720681-Developers",
            "CreateDate": "2025-11-26T13:12:05+00:00"
        },
        {
            "Path": "/",
            "GroupName": "introduction-to-aws-iam-enumeration-1764162720681-Infrastructure",
            "GroupId": "AGPAZAA2SOBEP2EHIARSA",
            "Arn": "arn:aws:iam::618531024968:group/introduction-to-aws-iam-enumeration-1764162720681-Infrastructure",
            "CreateDate": "2025-11-26T13:12:05+00:00"
        }
    ]
}
                                                                                                                                                                                            

└─$ aws iam list-groups-for-user --user-name introduction-to-aws-iam-enumeration-1764162720681-Joel --profile iam
{
    "Groups": [
        {
            "Path": "/",
            "GroupName": "introduction-to-aws-iam-enumeration-1764162720681-Developers",
            "GroupId": "AGPAZAA2SOBEGKNHJBEW4",
            "Arn": "arn:aws:iam::618531024968:group/introduction-to-aws-iam-enumeration-1764162720681-Developers",
            "CreateDate": "2025-11-26T13:12:05+00:00"
        }
    ]
}

```
```
└─$ aws iam list-group-policies --group-name introduction-to-aws-iam-enumeration-1764162720681-Developers --profile iam                       
{
    "PolicyNames": [
        "introduction-to-aws-iam-enumeration-1764162720681-devs-policy"
    ]
}

```
```
└─$ aws iam get-group-policy --policy-name introduction-to-aws-iam-enumeration-1764162720681-devs-policy --group-name introduction-to-aws-iam-enumeration-1764162720681-Developers --profile iam
{
    "GroupName": "introduction-to-aws-iam-enumeration-1764162720681-Developers",
    "PolicyName": "introduction-to-aws-iam-enumeration-1764162720681-devs-policy",
    "PolicyDocument": {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": [
                    "iam:ListAccessKeys"
                ],
                "Resource": [
                    "arn:aws:iam::618531024968:user/introduction-to-aws-iam-enumeration-1764162720681-Joel",
                    "arn:aws:iam::618531024968:user/introduction-to-aws-iam-enumeration-1764162720681-Mike"
                ],
                "Effect": "Allow"
            },
            {
                "Action": [
                    "iam:ListGroupPolicies",
                    "iam:ListAttachedPolicies",
                    "iam:ListPolicyVersions",
                    "iam:ListUserPolicies",
                    "iam:ListAttachedUserPolicies",
                    "iam:ListUsers",
                    "iam:ListGroups",
                    "iam:ListGroupsForUser",
                    "iam:GetPolicy",
                    "iam:GetPolicyVersion",
                    "iam:GetUser",
                    "iam:GetUserPolicy",
                    "iam:GetGroupPolicy",
                    "iam:GetGroup",
                    "iam:ListAttachedGroupPolicies"
                ],
                "Resource": "*",
                "Effect": "Allow"
            }
        ]
    }
}

```
## 6. roles
```
└─$ aws iam list-roles --profile iam                                                                                   
{
    "Roles": [
        {
            "Path": "/aws-reserved/sso.amazonaws.com/",
            "RoleName": "AWSReservedSSO_LabAdministrationAccess_a650738f0a686b4f",
            "RoleId": "AROAZAA2SOBEDS4TXA7LZ",
            "Arn": "arn:aws:iam::618531024968:role/aws-reserved/sso.amazonaws.com/AWSReservedSSO_LabAdministrationAccess_a650738f0a686b4f",
            "CreateDate": "2024-07-31T17:47:46+00:00",
            "AssumeRolePolicyDocument": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Allow",
                        "Principal": {
                            "Federated": "arn:aws:iam::618531024968:saml-provider/AWSSSO_959c69b4d7db49c0_DO_NOT_DELETE"
                        },
                        "Action": [
                            "sts:AssumeRoleWithSAML",
                            "sts:TagSession"
                        ],
                        "Condition": {

......  
```
It shows up so many roles, need to nerrow the roles down to Support role, with the —query command
```
└─$ aws iam list-roles --query "Roles[?RoleName=='SupportRole']" --profile iam
[
    {
        "Path": "/",
        "RoleName": "SupportRole",
        "RoleId": "AROAZAA2SOBEDJTKZGIVK",
        "Arn": "arn:aws:iam::618531024968:role/SupportRole",
        "CreateDate": "2025-11-26T13:12:22+00:00",
        "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Principal": {
                        "AWS": "arn:aws:iam::618531024968:root"
                    },
                    "Action": "sts:AssumeRole",
                    "Condition": {
                        "ArnEquals": {
                            "aws:PrincipalArn": "arn:aws:iam::618531024968:user/introduction-to-aws-iam-enumeration-1764162720681-Mary"
                        }
                    }
                }
            ]
        },
        "Description": "Assumable role for internal support",
        "MaxSessionDuration": 3600
    }
]

```
## 7. list and get the role policy
```
└─$ aws iam list-role-policies --role-name SupportRole --profile iam          
{
    "PolicyNames": [
        "AllowS3FullAccessForRole"
    ]
}




└─$ aws iam get-role-policy --policy-name AllowS3FullAccessForRole --role-name SupportRole --profile iam
{
    "RoleName": "SupportRole",
    "PolicyName": "AllowS3FullAccessForRole",
    "PolicyDocument": {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": [
                    "s3:*"
                ],
                "Resource": "*",
                "Effect": "Allow",
                "Sid": "AllowS3FullAccess"
            }
        ]
    }
}
```
this role have the full S3 access.







