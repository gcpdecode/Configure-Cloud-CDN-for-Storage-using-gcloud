```


# --- COLOR DEFINITIONS ---
RED='\033[0;31m'
GREEN='\033[0;32m'
BLUE='\033[0;34m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

clear
printf "${BLUE}==========================================${NC}\n"
printf "${YELLOW}   CloudoArc CDN Configuration Starting...  ${NC}\n"
printf "${BLUE}==========================================${NC}\n"

# 1. SETUP VARIABLES
export BUCKET_NAME=YOUR_BUCKET_NAME
export BACKEND_BUCKET=static-backend-bucket
export URL_MAP=cdn-map
export PROXY=cdn-http-proxy
export FORWARDING_RULE=cdn-http-rule

# 2. CREATE BACKEND
printf "\n${YELLOW}[1/4]${NC} Creating Backend Bucket with CDN..."
gcloud compute backend-buckets create $BACKEND_BUCKET --gcs-bucket-name=$BUCKET_NAME --enable-cdn --quiet
printf " ${GREEN}DONE!${NC}\n"

# 3. CREATE URL MAP
printf "${YELLOW}[2/4]${NC} Creating URL Map..."
gcloud compute url-maps create $URL_MAP --default-backend-bucket=$BACKEND_BUCKET --quiet
printf " ${GREEN}DONE!${NC}\n"

# 4. CREATE PROXY
printf "${YELLOW}[3/4]${NC} Creating HTTP Proxy..."
gcloud compute target-http-proxies create $PROXY --url-map=$URL_MAP --quiet
printf " ${GREEN}DONE!${NC}\n"

# 5. CREATE FORWARDING RULE
printf "${YELLOW}[4/4]${NC} Creating Global Forwarding Rule..."
gcloud compute forwarding-rules create $FORWARDING_RULE --global --target-http-proxy=$PROXY --ports=80 --quiet
printf " ${GREEN}DONE!${NC}\n"

# 6. GET IP ADDRESS
printf "\n${BLUE}------------------------------------------${NC}\n"
export IP_ADDRESS=$(gcloud compute forwarding-rules describe $FORWARDING_RULE --global --format="value(IPAddress)")
printf "${GREEN}SUCCESS!${NC} Your Load Balancer IP is: ${YELLOW}$IP_ADDRESS${NC}\n"

----------------------------------------------

gsutil ls gs://YOUR_BUCKET_NAME/images/

----------------------------------------------
curl -o nature.png http://YOUR_IP_ADDRESS/images/nature.png




```
