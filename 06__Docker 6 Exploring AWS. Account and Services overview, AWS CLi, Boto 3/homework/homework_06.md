# Security Group: MySQL, HTTPS и SSH

ID группы: `sg-022a3b96ebbf60ef7`. Регион: `eu-north-1`.

## 1. Создание Security Group через AWS CLI

```bash
SG_NAME='docker-cloud-morning-2025-bohdan-hw6-sg'

SG_ID="$(aws ec2 create-security-group \
  --group-name "$SG_NAME" \
  --description 'Security Group for MySQL and HTTPS and SSH' \
  --region eu-north-1 \
  --query 'GroupId' \
  --output text)"
```

## 2. MySQL и HTTPS через AWS CLI

```bash
aws ec2 authorize-security-group-ingress \
  --group-id "$SG_ID" \
  --region eu-north-1 \
  --ip-permissions \
    'IpProtocol=tcp,FromPort=3306,ToPort=3306,IpRanges=[{CidrIp=0.0.0.0/0,Description=mysql}]' \
    'IpProtocol=tcp,FromPort=443,ToPort=443,IpRanges=[{CidrIp=0.0.0.0/0,Description=https}]' \
  --query 'Return' \
  --output json \
  --no-cli-pager
```

Полученный результат:

```json
true
```

## 3. SSH через AWS Management Console

Добавлено правило: **Type:** `SSH`, **Protocol:** `TCP`, **Port:** `22`,
**Source:** `My IP`, **Description:** `SSH My IP`. Правило сохранено через **Save rules**.

## 4. Результат проверки через AWS CLI

```bash
aws ec2 describe-security-groups \
  --group-ids sg-022a3b96ebbf60ef7 \
  --region eu-north-1 \
  --query 'SecurityGroups[*].IpPermissions' \
  --output json \
  --no-cli-pager
```

Результат команды (личный IP заменён на `<MY_IP>/32`):

```json
[
  [
    {
      "IpProtocol": "tcp",
      "FromPort": 22,
      "ToPort": 22,
      "UserIdGroupPairs": [],
      "IpRanges": [
        {
          "Description": "SSH My IP",
          "CidrIp": "<MY_IP>/32"
        }
      ],
      "Ipv6Ranges": [],
      "PrefixListIds": []
    },
    {
      "IpProtocol": "tcp",
      "FromPort": 3306,
      "ToPort": 3306,
      "UserIdGroupPairs": [],
      "IpRanges": [
        {
          "Description": "mysql",
          "CidrIp": "0.0.0.0/0"
        }
      ],
      "Ipv6Ranges": [],
      "PrefixListIds": []
    },
    {
      "IpProtocol": "tcp",
      "FromPort": 443,
      "ToPort": 443,
      "UserIdGroupPairs": [],
      "IpRanges": [
        {
          "Description": "https",
          "CidrIp": "0.0.0.0/0"
        }
      ],
      "Ipv6Ranges": [],
      "PrefixListIds": []
    }
  ]
]
```


## 5. Удаление Security Group

```bash
aws ec2 delete-security-group \
  --group-id "$SG_ID" \
  --region eu-north-1 \
  --no-cli-pager
```

Группа удалена вместе с inbound/outbound-правилами. AWS вернул `Return: true`.
