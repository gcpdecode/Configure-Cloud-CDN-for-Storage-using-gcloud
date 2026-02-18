```

# 1. Setup Variables (Bucket name is already filled from your screenshot)
export BUCKET_NAME=qwiklabs-gcp-03-a688e6ad9373-bucket
export BACKEND_BUCKET=static-backend-bucket
export URL_MAP=cdn-map
export PROXY=cdn-http-proxy
export FORWARDING_RULE=cdn-http-rule

# 2. Create Backend with CDN enabled
gcloud compute backend-buckets create $BACKEND_BUCKET --gcs-bucket-name=$BUCKET_NAME --enable-cdn

# 3. Create URL Map
gcloud compute url-maps create $URL_MAP --default-backend-bucket=$BACKEND_BUCKET

# 4. Create HTTP Proxy
gcloud compute target-http-proxies create $PROXY --url-map=$URL_MAP

# 5. Create Global Forwarding Rule
gcloud compute forwarding-rules create $FORWARDING_RULE --global --target-http-proxy=$PROXY --ports=80

# 6. Automatically get the IP and save it to a variable
export IP_ADDRESS=$(gcloud compute forwarding-rules describe $FORWARDING_RULE --global --format="value(IPAddress)")

# 7. Show the IP and give it a few seconds to warm up
echo "Load Balancer IP: $IP_ADDRESS"
echo "Waiting 30 seconds for propagation..."
sleep 30

# 8. Test the image download (Assuming nature.png exists in your /images/ folder)
curl -o nature.png http://$IP_ADDRESS/images/nature.png
```
