# AWS SQS (Simple Queue Service) Practice Lab

## Objective

Learn how to:

* Create an SQS Queue
* Send Messages to Queue
* Receive Messages from Queue
* Delete Messages after Processing
* Understand Visibility Timeout
* Understand Dead Letter Queue (DLQ)
* Integrate EC2 Application with SQS

---

# What is Amazon SQS?

Amazon SQS is a fully managed message queuing service used to decouple applications.

### Real-Life Example

```text
Customer Places Order
         |
         v
      SQS Queue
         |
         v
 Order Processing Server
         |
         v
  Database Updated
```

If the processing server is down, messages remain safely in the queue.

---

# Key Concepts

| Concept            | Description                          |
| ------------------ | ------------------------------------ |
| Producer           | Sends messages to queue              |
| Queue              | Stores messages                      |
| Consumer           | Reads messages                       |
| Visibility Timeout | Temporarily hides message after read |
| Retention Period   | How long messages stay in queue      |
| Dead Letter Queue  | Stores failed messages               |
| Long Polling       | Waits for messages before responding |

---

# SQS Types

## Standard Queue

### Features

* Unlimited Throughput
* At Least Once Delivery
* Best Effort Ordering

```text
Message 1
Message 3
Message 2
Message 4
```

Order is not guaranteed.

---

## FIFO Queue

### Features

* First In First Out
* Exactly Once Processing
* Ordered Delivery

```text
Message 1
Message 2
Message 3
Message 4
```

Must end with:

```text
.fifo
```

Example:

```text
orders.fifo
```

---

# Architecture

```text
                Producer
                   |
                   v
             +-----------+
             |    SQS    |
             +-----------+
                   |
                   v
              Consumer
```

---

# Lab 1: Create SQS Queue from Console

## Step 1

Navigate:

```text
AWS Console
→ SQS
→ Create Queue
```

### Configuration

```text
Queue Name : my-sqs-queue

Type : Standard

Message Retention :
4 Days

Visibility Timeout :
30 Seconds
```

Click:

```text
Create Queue
```

---

# Lab 2: Send Message

Open Queue

```text
Send and Receive Messages
```

Message:

```text
Hello from AWS SQS
```

Click:

```text
Send Message
```

---

# Lab 3: Receive Message

Click:

```text
Poll for Messages
```

Output:

```text
Hello from AWS SQS
```

---

# Lab 4: Delete Message

After processing:

```text
Delete Message
```

Message permanently removed from queue.

---

# AWS CLI Practice

## Create Queue

```bash
aws sqs create-queue \
--queue-name my-sqs-queue
```

---

## List Queues

```bash
aws sqs list-queues
```

---

## Get Queue URL

```bash
aws sqs get-queue-url \
--queue-name my-sqs-queue
```

Output:

```text
https://sqs.us-east-1.amazonaws.com/123456789012/my-sqs-queue
```

---

## Send Message

```bash
aws sqs send-message \
--queue-url QUEUE_URL \
--message-body "Hello SQS"
```

---

## Receive Message

```bash
aws sqs receive-message \
--queue-url QUEUE_URL
```

---

## Delete Message

```bash
aws sqs delete-message \
--queue-url QUEUE_URL \
--receipt-handle RECEIPT_HANDLE
```

---

# Lab 5: Visibility Timeout

### Scenario

```text
Consumer 1 reads message
```

Message becomes invisible.

```text
Visibility Timeout = 30 Seconds
```

Consumer 2 cannot see it.

If Consumer 1 fails:

```text
30 seconds later
```

Message becomes visible again.

---

# Lab 6: Dead Letter Queue (DLQ)

## Create DLQ

```text
order-dlq
```

Create another queue:

```text
order-queue
```

Configure:

```text
Redrive Policy

Max Receive Count = 3
```

Architecture:

```text
Producer
   |
   v
order-queue
   |
   | Failed 3 Times
   v
order-dlq
```

---

# Practical Scenario 1: E-Commerce Orders

```text
Customer Places Order
        |
        v
     SQS Queue
        |
        v
 Payment Service
        |
        v
 Shipping Service
```

Benefits:

* Decoupled Services
* Reliable Processing
* Scalable Architecture

---

# Practical Scenario 2: Email Processing

```text
Application
      |
      v
   SQS Queue
      |
      v
 Email Worker
      |
      v
 Amazon SES
```

---

# Practical Scenario 3: EC2 Log Processing

```text
Application Server
       |
       v
      SQS
       |
       v
 Log Processing Server
       |
       v
      S3
```

---

# IAM Permissions Required

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "sqs:*"
      ],
      "Resource": "*"
    }
  ]
}
```

---

# Important Points to Remember

### SQS is Pull-Based

Consumer requests messages.

```text
Consumer ---> SQS
```

Not push-based.

---

### Standard Queue

* Unlimited throughput
* At least once delivery
* Possible duplicate messages

---

### FIFO Queue

* Ordered delivery
* Exactly-once processing
* Lower throughput than Standard

---

### Message Size

```text
Maximum = 256 KB
```

For larger payloads:

```text
Store data in S3
Store S3 URL in SQS
```

---

### Retention Period

```text
1 Minute
to
14 Days
```

Default:

```text
4 Days
```

---

### Visibility Timeout

Default:

```text
30 Seconds
```

Range:

```text
0 Seconds
to
12 Hours
```

---

# Interview Questions

### Why SQS?

To decouple applications and process messages asynchronously.

### Difference Between SQS and SNS?

| SQS                        | SNS                  |
| -------------------------- | -------------------- |
| Pull Model                 | Push Model           |
| One Consumer Reads Message | Multiple Subscribers |
| Queue Based                | Pub/Sub Based        |

### Can SQS trigger Lambda?

Yes.

```text
SQS → Lambda → Processing
```

### Does SQS guarantee message order?

* Standard Queue → No
* FIFO Queue → Yes

---

# Practice Assignment

### Task

Create:

```text
1 Standard Queue
1 FIFO Queue
1 Dead Letter Queue
```

Perform:

1. Send 10 messages.
2. Receive messages using AWS CLI.
3. Configure DLQ.
4. Test Visibility Timeout.
5. Integrate SQS with Lambda.
6. Monitor metrics in Amazon CloudWatch.

### Expected Outcome

You should understand:

* Standard vs FIFO Queue
* Message Lifecycle
* Visibility Timeout
* Dead Letter Queues
* SQS + Lambda Integration
* Real-world asynchronous architectures using Amazon Simple Queue Service.
