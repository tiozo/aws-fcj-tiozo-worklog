---
title: "Testing Model"
date: "`date +'%Y-%m-%d'`"
weight: 4
chapter: false
pre: "<b>4. </b>"
---

## Test Fraud Detection API

### Lấy API Endpoint

```bash
API_URL=$(terraform output -raw predict_endpoint_url)
echo "API Endpoint: $API_URL"
```

### Test Thủ công với cURL

**Test 1: Giao dịch Bình thường**
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

Kết quả mong đợi:
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

**Test 2: Giao dịch Đáng ngờ**
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

Kết quả mong đợi:
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

## Script Test Tự động

Tạo Python script để generate nhiều test cases:

### Tạo file test_fraud_api.py

```python
import requests
import json
from datetime import datetime, timedelta
import random
import sys

# Lấy API URL từ command line hoặc nhập tay
API_URL = sys.argv[1] if len(sys.argv) > 1 else input("Nhập API URL: ")

def generate_test_transaction(fraud_probability=0.3):
    """Tạo dữ liệu giao dịch ngẫu nhiên"""
    
    # Timestamp ngẫu nhiên trong 24 giờ qua
    timestamp = datetime.utcnow() - timedelta(hours=random.randint(0, 24))
    
    # Xác định có phải giao dịch gian lận không
    is_fraud_test = random.random() < fraud_probability
    
    if is_fraud_test:
        # Đặc điểm giao dịch gian lận
        amount = random.uniform(5000, 10000)
        user_id = f"user_{random.randint(900, 999)}"
        payee_id = f"unknown_{random.randint(1, 100)}"
    else:
        # Đặc điểm giao dịch bình thường
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
    """Gửi giao dịch đến API và nhận prediction"""
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
            print(f"Lỗi: {response.status_code} - {response.text}")
            return None
    except Exception as e:
        print(f"Request thất bại: {str(e)}")
        return None

def run_tests(num_tests=10):
    """Chạy nhiều test transactions"""
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
        
        # Tạo test data
        transaction = generate_test_transaction()
        print(f"Giao dịch: ${transaction['amount']:.2f} | "
              f"User: {transaction['user_id']} | "
              f"Payee: {transaction['payee_id']}")
        
        # Gửi đến API
        prediction = test_transaction(transaction)
        
        if prediction:
            results["total"] += 1
            
            # Hiển thị kết quả
            print(f"XGBoost Score: {prediction['xgb_score']:.4f}")
            print(f"Autoencoder Score: {prediction['ae_score']:.4f}")
            print(f"Ensemble Score: {prediction['ensemble_score']:.4f}")
            print(f"Phát hiện Gian lận: {'CÓ ⚠️' if prediction['is_fraud'] else 'KHÔNG ✓'}")
            
            if prediction['is_fraud']:
                results["fraud_detected"] += 1
            else:
                results["normal_detected"] += 1
        else:
            results["errors"] += 1
    
    # Tổng kết
    print(f"\n{'='*80}")
    print("TỔNG KẾT TEST")
    print(f"{'='*80}")
    print(f"Tổng số Tests: {results['total']}")
    print(f"Phát hiện Gian lận: {results['fraud_detected']}")
    print(f"Giao dịch Bình thường: {results['normal_detected']}")
    print(f"Lỗi: {results['errors']}")
    print(f"{'='*80}\n")

if __name__ == "__main__":
    # Chạy 20 test transactions
    run_tests(num_tests=20)
```

### Chạy Test Script

```bash
# Đảm bảo có requests library
pip3 install requests

# Chạy tests
python3 test_fraud_api.py $API_URL
```

## Test Kinesis Stream (Tùy chọn)

Gửi data đến Kinesis cho batch processing:

```bash
STREAM_NAME=$(terraform output -raw kinesis_stream_name)

# Gửi test record
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

## Xác minh Kết quả trong S3

Kiểm tra predictions đã lưu:

```bash
RESULT_BUCKET=$(terraform output -raw result_bucket_name)

# List predictions gần đây
aws s3 ls s3://$RESULT_BUCKET/predictions/ --recursive | tail -10

# Download và xem prediction
aws s3 cp s3://$RESULT_BUCKET/predictions/2024/01/15/[file-id].json - | jq .
```

## Kiểm tra Lambda Logs

Xem API handler logs:

```bash
# Lấy function name
FUNCTION_NAME=$(terraform output -raw api_handler_function_name)

# Tail logs
aws logs tail /aws/lambda/$FUNCTION_NAME --follow
```

## Performance Testing

Test API latency:

```bash
# Test latency đơn giản
time curl -X POST $API_URL \
  -H "Content-Type: application/json" \
  -d '{"amount": 100, "user_id": "user_1", "payee_id": "merchant_1", "timestamp": "2024-01-15T12:00:00Z"}'
```

Latency mong đợi: 200-500ms

## Bước Tiếp theo

API đã hoạt động! Tiến hành [Giám sát với CloudWatch](../5-monitoring/).
