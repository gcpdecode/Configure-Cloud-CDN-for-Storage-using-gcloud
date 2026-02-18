```

# 1. SET YOUR VARIABLES (Fill in your bucket name first!)
export BUCKET_NAME=qwiklabs-gcp-03-a688e6ad9373-bucket
export BACKEND_BUCKET=static-backend-bucket
export URL_MAP=cdn-map
export PROXY=cdn-http-proxy
export FORWARDING_RULE=cdn-http-rule

# 2. CREATE BACKEND BUCKET AND ENABLE CDN
gcloud compute backend-buckets create $BACKEND_BUCKET \
    --gcs-bucket-name=$BUCKET_NAME \
    --enable-cdn

# 3. CREATE URL MAP
gcloud compute url-maps create $URL_MAP \
    --default-backend-bucket=$BACKEND_BUCKET

# 4. CREATE TARGET HTTP PROXY
gcloud compute target-http-proxies create $PROXY \
    --url-map=$URL_MAP

# 5. CREATE GLOBAL FORWARDING RULE
gcloud compute forwarding-rules create $FORWARDING_RULE \
    --global \
    --target-http-proxy=$PROXY \
    --ports=80

# 6. GET YOUR PUBLIC IP ADDRESS
export IP_ADDRESS=$(gcloud compute forwarding-rules describe $FORWARDING_RULE --global --format="value(IPAddress)")
echo "Your Load Balancer IP is: $IP_ADDRESS"

# 7. TEST THE CONNECTION (Wait 2-5 minutes before running this)
curl -I http://$IP_ADDRESS/index.html

```
