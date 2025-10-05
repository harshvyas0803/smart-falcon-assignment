# Hyperledger Fabric Asset Management System

## Problem Statement
A financial institution needs to implement a blockchain-based system to manage and track assets with attributes: `DEALERID`, `MSISDN`, `MPIN`, `BALANCE`, `STATUS`, `TRANSAMOUNT`, `TRANSTYPE`, `REMARKS`.

## Prerequisites
This project requires the Hyperledger Fabric samples repository. Clone the fabric-samples repository to your local machine before proceeding with the setup.

## Level 1: Setup Hyperledger Fabric Test Network ✅

```bash
cd fabric-samples/test-network
./network.sh up createChannel -c mychannel -ca
./network.sh deployCC -c mychannel -ccn basic -ccp ../asset-transfer-basic/chaincode-go -ccl go
```

## Level 2: Smart Contract (Chaincode) ✅

Functions: `CreateAccount`, `ReadAccount`, `UpdateAccount`, `GetAccountHistory`

```bash
cd fabric-samples/test-network
./network.sh deployCC -ccn accountcc -ccp ../asset-transfer-custom/chaincode-go -ccl go
```

## Level 3: REST API + Docker ✅

```bash
cd fabric-samples/asset-transfer-custom/application-go-rest
docker build -t asset-rest:latest .
docker run --rm -p 8080:8080 \
  --network fabric_test \
  -v $(pwd)/../../test-network/organizations:/app/organizations:ro \
  asset-rest:latest
```

## API Usage

### Create Account
```bash
curl -X POST http://localhost:8080/accounts \
  -H "Content-Type: application/json" \
  -d '{
        "key":"acc002",
        "DEALERID":"D002",
        "MSISDN":"8888888888",
        "MPIN":"5678",
        "BALANCE":"5000",
        "STATUS":"ACTIVE",
        "TRANSAMOUNT":"0",
        "TRANSTYPE":"INIT",
        "REMARKS":"Initial asset"
      }'
```
Response: `{"result":"created","key":"acc002"}`

### Get Account
```bash
curl http://localhost:8080/accounts/acc002
```
Response: 
```json
{"DEALERID":"D002","MSISDN":"8888888888","MPIN":"5678","BALANCE":5000,"STATUS":"ACTIVE","TRANSAMOUNT":0,"TRANSTYPE":"INIT","REMARKS":"Initial asset"}
```

### Update Account
```bash
curl -X PUT http://localhost:8080/accounts/acc002 \
  -H "Content-Type: application/json" \
  -d '{"BALANCE":"2000","REMARKS":"Updated balance"}'
```
Response: `{"result":"updated","key":"acc002"}`

### Get Transaction History
```bash
curl http://localhost:8080/accounts/acc002/history
```
Response:
```json
[
  {
    "IsDelete": false,
    "Timestamp": {
      "seconds": 1759169817,
      "nanos": 392398699
    },
    "TxId": "6db0d88ddb642af0a8884e824b7d04bf801f23c528194a2f6814ca779487f8dc",
    "Value": {
      "BALANCE": 2000,
      "DEALERID": "D002",
      "MPIN": "5678",
      "MSISDN": "8888888888",
      "REMARKS": "Updated balance",
      "STATUS": "ACTIVE",
      "TRANSAMOUNT": 0,
      "TRANSTYPE": "INIT"
    }
  },
  {
    "IsDelete": false,
    "Timestamp": {
      "seconds": 1759169790,
      "nanos": 736623449
    },
    "TxId": "81a456fde77e4e27a31071b6324908afe21c786d7624d0e355a42ec66838e3ed",
    "Value": {
      "BALANCE": 5000,
      "DEALERID": "D002",
      "MPIN": "5678",
      "MSISDN": "8888888888",
      "REMARKS": "Initial asset",
      "STATUS": "ACTIVE",
      "TRANSAMOUNT": 0,
      "TRANSTYPE": "INIT"
    }
  }
]
```

## Setup Status
- ✅ Level 1: Fabric test network setup
- ✅ Level 2: Smart contract developed and deployed  
- ✅ Level 3: REST API created, Dockerized, tested