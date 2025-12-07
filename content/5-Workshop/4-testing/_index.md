---
title: "Testing the Model"
date: "`date +'%Y-%m-%d'`"
weight: 4
chapter: false
pre: "<b>4. </b>"
---

## Test Fraud Detection API

### Get API Endpoint

```bash
API_URL=$(terraform output -raw predict_endpoint_url)
echo "API Endpoint: $API_URL"
```

### Manual Testing with cURL

**Test 1: Normal Transaction**
```bash
curl -X POST $API_URL \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 50.00,
    "user_id": "user_123",
    "payee_id": "merchant_456",
    "timestamp": "2024-01-15T10:30:00Z"
  }'
```

Expected response:
```json
{
  "xgb_score": 0.15,
  "ae_score": 0.08,
  "ensemble_score": 0.136,
  "is_fraud": false,
  "confidence": 0.136,
  "timestamp": "2024-01-15T10:30:05Z"
}
```

**Test 2: Suspicious Transaction**
```bash
curl -X POST $API_URL \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 9999.99,
    "user_id": "user_999",
    "payee_id": "unknown_payee",
    "timestamp": "2024-01-15T03:00:00Z"
  }'
```

Expected response:
```json
{
  "xgb_score": 0.92,
  "ae_score": 0.85,
  "ensemble_score": 0.906,
  "is_fraud": true,
  "confidence": 0.906,
  "timestamp": "2024-01-15T03:00:05Z"
}
```

## Automated Testing Script

Create a Python test script to generate multiple test cases:

### Create test_fraud_api.py

```python
import requests
import json
from datetime import datetime, timedelta
import random
import sys

# Get API URL from command line or environment
API_URL = sys.argv[1] if len(sys.argv) > 1 else input("Enter API URL: ")

def generate_test_transaction(fraud_probability=0.3):
    """Generate random transaction data"""
    
    # Random timestamp within last 24 hours
    timestamp = datetime.utcnow() - timedelta(hours=random.randint(0, 24))
    
    # Determine if this should be fraudulent
    is_fraud_test = random.random() < fraud_probability
    
    if is_fraud_test:
        # Fraudulent transaction characteristics
        amount = random.uniform(5000, 10000)
        user_id = f"user_{random.randint(900, 999)}"
        payee_id = f"unknown_{random.randint(1, 100)}"
    else:
        # Normal transaction characteristics
        amount = random.uniform(10, 500)
        user_id = f"user_{random.randint(100, 500)}"
        payee_id = f"merchant_{random.randint(1, 50)}"
    
    return {
        "amount": round(amount, 2),
        "user_id": user_id,
        "payee_id": payee_id,
        "timestamp": timestamp.isoformat() + "Z"
    }

def test_transaction(transaction_data):
    """Send transaction to API and get prediction"""
    try:
        response = requests.post(
            API_URL,
            headers={"Content-Type": "application/json"},
            json=transaction_data,
            timeout=30
        )
        
        if response.status_code == 200:
            return response.json()
        else:
            print(f"Error: {response.status_code} - {response.text}")
            return None
    except Exception as e:
        print(f"Request failed: {str(e)}")
        return None

def run_tests(num_tests=10):
    """Run multiple test transactions"""
    print(f"\n{'='*80}")
    print(f"Testing Fraud Detection API: {API_URL}")
    print(f"{'='*80}\n")
    
    results = {
        "total": 0,
        "fraud_detected": 0,
        "normal_detected": 0,
        "errors": 0
    }
    
    for i in range(num_tests):
        print(f"\n--- Test {i+1}/{num_tests} ---")
        
        # Generate test data
        transaction = generate_test_transaction()
        print(f"Transaction: ${transaction['amount']:.2f} | "
              f"User: {transaction['user_id']} | "
              f"Payee: {transaction['payee_id']}")
        
        # Send to API
        prediction = test_transaction(transaction)
        
        if prediction:
            results["total"] += 1
            
            # Display results
            print(f"XGBoost Score: {prediction['xgb_score']:.4f}")
            print(f"Autoencoder Score: {prediction['ae_score']:.4f}")
            print(f"Ensemble Score: {prediction['ensemble_score']:.4f}")
            print(f"Fraud Detected: {'YES ⚠️' if prediction['is_fraud'] else 'NO ✓'}")
            
            if prediction['is_fraud']:
                results["fraud_detected"] += 1
            else:
                results["normal_detected"] += 1
        else:
            results["errors"] += 1
    
    # Summary
    print(f"\n{'='*80}")
    print("TEST SUMMARY")
    print(f"{'='*80}")
    print(f"Total Tests: {results['total']}")
    print(f"Fraud Detected: {results['fraud_detected']}")
    print(f"Normal Transactions: {results['normal_detected']}")
    print(f"Errors: {results['errors']}")
    print(f"{'='*80}\n")

if __name__ == "__main__":
    # Run 20 test transactions
    run_tests(num_tests=20)
```

### Run the Test Script

```bash
# Make sure you have requests library
pip3 install requests

# Run tests
python3 test_fraud_api.py $API_URL
```

## Test Kinesis Stream (Optional)

Send data to Kinesis for batch processing:

```bash
STREAM_NAME=$(terraform output -raw kinesis_stream_name)

# Send test record
aws kinesis put-record \
  --stream-name $STREAM_NAME \
  --partition-key "test-1" \
  --data '{
    "transaction_data": {
      "amount": 150.00,
      "user_id": "user_789",
      "payee_id": "merchant_123",
      "timestamp": "2024-01-15T12:00:00Z"
    }
  }'
```

## Verify Results in S3

Check if predictions are saved:

```bash
RESULT_BUCKET=$(terraform output -raw result_bucket_name)

# List recent predictions
aws s3 ls s3://$RESULT_BUCKET/predictions/ --recursive | tail -10

# Download and view a prediction
aws s3 cp s3://$RESULT_BUCKET/predictions/2024/01/15/[file-id].json - | jq .
```

## Check Lambda Logs

View API handler logs:

```bash
# Get function name
FUNCTION_NAME=$(terraform output -raw api_handler_function_name)

# Tail logs
aws logs tail /aws/lambda/$FUNCTION_NAME --follow
```

## Performance Testing

Test API latency:

```bash
# Simple latency test
time curl -X POST $API_URL \
  -H "Content-Type: application/json" \
  -d '{"amount": 100, "user_id": "user_1", "payee_id": "merchant_1", "timestamp": "2024-01-15T12:00:00Z"}'
```

Expected latency: 200-500ms

## Next Steps

API is working! Proceed to [Monitoring with CloudWatch](../5-monitoring/).
