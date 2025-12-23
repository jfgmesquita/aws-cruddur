# Step 0 — Billing and Architecture


## Conceptual Diagram 

This diagram provides a high-level architectural overview of the application. It is designed to ensure all stakeholders, including those in non-technical roles, can understand the system's core structure, focusing on the 'what' and 'why' rather than the 'how' (technical implementation details).

![Conceptual Diagram Screenshot](../assets/conceptual-diagram.png)
Diagram made with Lucidchart.

## AWS CLI

Installing the AWS CLI on my local machine:
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update
```

After creating Access Keys for the AWS CLI:
```
aws configure
```

Testing it:
```
aws sts get-caller-identity
```

## Budgets

As I want to spend the least money possible and try to use the most of the AWS Free Tier services, I created a Cost Budget with two thresholds (alerts): one at $0.01 and another at $0.50.

Setting the AWS Account ID environment variable for convenience:
```
export AWS_ACCOUNT_ID="<Account ID>"
```

Budget:
```
{
    "BudgetLimit": {
        "Amount": "1.0",
        "Unit": "USD"
    },
    "BudgetName": "MyBudget",
    "BudgetType": "COST",
    "CostTypes": {
        "IncludeCredit": true,
        "IncludeDiscount": true,
        "IncludeOtherSubscription": true,
        "IncludeRecurring": true,
        "IncludeRefund": true,
        "IncludeSubscription": true,
        "IncludeSupport": true,
        "IncludeTax": true,
        "IncludeUpfront": true,
        "UseBlended": false
    },
    "TimeUnit": "MONTHLY"
}
```

Budget Alerts:
```
[
    {
        "Notification": {
            "ComparisonOperator": "GREATER_THAN",
            "NotificationType": "ACTUAL",
            "Threshold": 1,
            "ThresholdType": "PERCENTAGE"
        },
        "Subscribers": [
            {
                "Address": "email@cruddur.com",
                "SubscriptionType": "EMAIL"
            }
        ]
    },
    {
        "Notification": {
            "ComparisonOperator": "GREATER_THAN",
            "NotificationType": "ACTUAL",
            "Threshold": 50,
            "ThresholdType": "PERCENTAGE"
        },
        "Subscribers": [
                      {
                "Address": "email@cruddur.com",
                "SubscriptionType": "EMAIL"
            }
        ]
    }
]
```

Creating the Budget:
```
aws budgets create-budget \
    --account-id $AWS_ACCOUNT_ID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```

![Budget Screenshot](../assets/budget-screenshot.png)