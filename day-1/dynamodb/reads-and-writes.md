---
description: This section's a little heavy.
---

# Reads and Writes

## Read consistency&#x20;

When we retrieve data from our table, we perform a read. DynamoDB supports _eventually consistent_ and _strongly consistent_ reads:

### **Eventually Consistent Reads**

When you read data from a DynamoDB table, the response might not reflect the results of a recently completed write operation. The response might include some stale data. If you repeat your read request after a short time, the response should return the latest data.

DynamoDB uses eventually consistent reads, unless you specify otherwise.

### **Strongly Consistent Reads**

When you request a strongly consistent read, DynamoDB returns a response with the most up-to-date data, reflecting the updates from all prior write operations that were successful. However, this consistency comes with some disadvantages:

* A strongly consistent read might not be available if there is a network delay or outage. In this case, DynamoDB may return a server error (HTTP 500).
* Strongly consistent reads may have higher latency than eventually consistent reads.
* Strongly consistent reads are not supported on global secondary indexes. **** (More about these later)&#x20;
* Strongly consistent reads use more throughput capacity than eventually consistent reads.&#x20;

## Read and write capacity

We will look at read and write capacity modes first. When we created our table, we went with the default option: provisioned capacity (which is free-tier eligible). You can check your table's settings in the console under Additional settings. For the Dogs table, your capacity mode is Provisioned, with 1 read capacity unit and 1 write capacity unit provisioned.&#x20;

### Provisioned capacity mode

If you choose provisioned mode, you specify the number of reads and writes per second that you require for your application. You can use auto scaling to adjust your table’s provisioned capacity automatically in response to traffic changes. This helps you govern your DynamoDB use to stay at or below a defined request rate in order to obtain cost predictability.

Provisioned mode is a good option if any of the following are true:

* You have predictable application traffic.
* You run applications whose traffic is consistent or ramps gradually.
* You can forecast capacity requirements to control costs.

Bottom line: you have to specify limits for capacity, but then you know that there is an upper limit to how big your bill can get.&#x20;

### On-demand capacity mode

The alternative to provisioned capacity is on-demand capacity: is a flexible billing option capable of serving thousands of requests per second without capacity planning. It offers pay-per-request pricing for read and write requests so that you pay only for what you use.

When you choose on-demand mode, DynamoDB instantly accommodates your workloads as they ramp up or down to any previously reached traffic level. If a workload’s traffic level hits a new peak, DynamoDB adapts rapidly to accommodate the workload.

On-demand mode is a good option if any of the following are true:

* You create new tables with unknown workloads.
* You have unpredictable application traffic.
* You prefer the ease of paying for only what you use.

Bottom line: you do not have to know how much capacity you need. You will be charged based on the number of requests. This might save some money because you only pay for what you use, but then again if there is nothing stopping the requests coming in, then there is nothing stopping your bills from growing big..&#x20;

### RRU, WRU, RCU, WCU?

Here is where it gets a little trickier.

* For on-demand mode, you will be charged based on **read request units** and **write request units** or RRU and WRU.&#x20;
* For provisioned mode, you specify how many **read capacity units** and **write capacity units**, or RCUs and WCUs your table has.&#x20;

For example our Dogs table has 1 RCU and 1 WCU provisioned. Next we will look closer at what 1 RCU or WCU buys us.&#x20;

#### RCU

For an item that is up to 4 KB in size, one RCU is

* one strongly consistent read per second OR
* two eventually consistent reads per second.&#x20;

If the item size is over 4 KB, then additional RCU's are required.&#x20;

#### WCU

For an item up to 1 KB in size, one WCU is&#x20;

* one write per second&#x20;

If the item is bigger than 1 KB, then additional WCU's are required.

All the other juicy details are in [https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html)

## TL;DR

For predictable workloads (or free tier) use provisioned capacity (default). For variable workloads choose on-demand.&#x20;

Eventually consistent reads are the default. They might in some cases return stale data.&#x20;

Strongly consistent reads return the most up-to-date data but they use more read capacity units.&#x20;

Bigger items consume more units.&#x20;

There are also transactional reads and writes, but we won't cover those here.&#x20;
