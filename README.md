# Reproducible steps for using Yandex Cloud Message Queue

Querying is done with AWS CLI. 
Yandex Cloud offers two types of queues: standard and FIFO. In standard queues messages are read in random order, while in FIFO messages are read in order they are received. 

## Configuring user
1. Create user with the role "editor" and static access key for this user with Yandex Cloud.
2. In AWS CLI configure user with `aws configure` command. Enter Access Key ID and Access Key given by Yandex Cloud platform and region: ru-central1.

## Creating the queue
For regular queues:
```
aws sqs create-queue --queue-name <queue_name> --endpoint https://message-queue.api.cloud.yandex.net/
```
For FIFO queues FifoQueue and ContentBasedDeduplication flags should also be provided. 

Output:
```
{
  "QueueUrl": https://message-queue.api.cloud.yandex.net/b1g7cq4sd2q09h0e5ugg/dj600000000d6rmv017i/<queue_name>
}
```

## Sending messages
```
aws sqs send-message --message-body <message> --endpoint https://message-queue.api.cloud.yandex.net/ --queue-url <queue_url>
```
For FIFO queues additional parameters of MessageGroupId and MessageDeduplicationId should be provided, otherwise the message will not be sent. 

Output:
```
{
    "MD5OfMessageBody": "88ef8f31ed540f1c4c03d5fdb06a7935",
    "MessageId": "8f2229ec-81c39f95-d541c8f8-cb6e98bf",
    "SequenceNumber" (FIFO only): "6"
}
```

## Receiving messages 
```
aws sqs receive-message --endpoint https://message-queue.api.cloud.yandex.net/ --queue-url <queue_url>
```

Output:
```
{
    "Messages": [
        {
            "MessageId": "eadb8085-d87f0c85-72790bb9-a9992359",
            "ReceiptHandle": "EAEgw9rzyvEwKAE",
            "MD5OfBody": "0fd3dbec9730101bff92acc820befc34",
            "Body": "Test string",
            "Attributes": {
                "ApproximateFirstReceiveTimestamp": "1679757733187",
                "ApproximateReceiveCount": "1",
                "SentTimestamp": "1679757459177"
            }
        }
    ]
}
```
FIFO:
```
{
    "Messages": [
        {
            "MessageId": "f23dd043-26e667db-8d9cf5e4-31ff15e7",
            "ReceiptHandle": "CgExEAUaI2Q2MWIyZTM4LThmMTFlZTAxLTcwMDY1YWRhLWMwN2M2ZThmIKjm-8rxMCgA",
            "MD5OfBody": "68390233272823b7adf13a1db79b2cd7",
            "Body": "Message 1",
            "Attributes": {
                "SequenceNumber": "5",
                "MessageDeduplicationId": "1",
                "MessageGroupId": "1",
                "ApproximateFirstReceiveTimestamp": "1679757865768",
                "ApproximateReceiveCount": "1",
                "SentTimestamp": "1679757831955"
            }
        }
    ]
}
```

## Deleting messages
```
aws sqs delete-message --endpoint https://message-queue.api.cloud.yandex.net/ --queue-url <queue_url> --receipt-handle <receipt_handle>
```
