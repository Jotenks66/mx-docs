---
id: es-index-events
title: events
---

[comment]: # (mx-abstract)

This page describes the structure of the `events` index (Elasticsearch), and also depicts a few examples of how to query it.

[comment]: # (mx-context-auto)

## _id

The `_id` field for this index is composed of hex-encoded hash of the transaction or the smart contract result that generated the log plus shard ID and order of event

example:  `{hash}-{shardID}-{order}` (example: `abcd-2-1`).

[comment]: # (mx-context-auto)

## Fields

| Field          | Description                                                                                                                                                                                                                 |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logAddress     | The address field holds the address in bech32 encoding. It can be the address of the smart contract that generated the log or the address of the receiver of the transaction.                                               |
| address        | The address field holds the address in bech32 encoding. It can be the address of the smart contract that generated the event or the address of the receiver of the transaction.                                             | 
| txHash         | The txHash field field for this index is composed of hex-encoded hash of the transaction or the smart contract result that generated the log.                                                                               |
| originalTxHash | The originalTxHash field holds the hex-encoded hash of the initial transaction. When this field is not empty the log is generated by a smart contract result and this field represents the hash of the initial transaction. |
| timestamp      | The timestamp field represents the timestamp of the block in which the log was generated.                                                                                                                                   |
| identifier     | This field represents the identifier of the event.                                                                                                                                                                          |   
| topics         | The topics field holds a list with extra information, hex-encoded. They don't have a specific order because the smart contract is free to log anything that could be helpful.                                               |
| data           | The data field can contain information added by the smart contract that generated the event, hex-encoded.                                                                                                                   |
| order          | The order field represents the index of the event indicating the execution order.                                                                                                                                           |
| txOrder        | The txOrder field represents the execution order of transaction/smart contract who generated this event.                                                                                                                    |
| timestamp      | The timestamp field represents the timestamp of the block in which the event was generated.                                                                                                                                 |
| shardID        | The shardID field represents the shard this events belongs to.                                                                                                                                                              |


## Query examples

### Fetch all the events generated by a transaction

```
curl --request GET \
  --url ${ES_URL}/events/_search \
  --header 'Content-Type: application/json' \
  --data '{
	"query": {
		"match": {
			"txHash":"d6.."
		}
	}
}'
```

### Fetch all the events generated by a transaction and the smart contract results triggered by it

```
curl --request GET \
  --url ${ES_URL}/events/_search \
  --header 'Content-Type: application/json' \
  --data '{
	"query": {
		"bool": {
			"should": [
				{
				     "match": {
                       "_id":"d6.."
                     }
				},
				{
					"match": {
						"originalTxHash": "d6.."
					}
				}
			]
		}
	}
}'
```
