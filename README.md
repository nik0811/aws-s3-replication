# aws-s3-replication

```
#!/bin/bash

old_us3bucket=""
old_eus3bucket=""
new_us3bucket=""
new_eus3bucket=""
old_aws_access_key_id=""
old_aws_secret_access_key=""
new_aws_access_key_id=""
new_aws_secret_access_key=""

#To check the difference between US bucket in both the region
command -v aws > /dev/null
if [[ $? != 0 ]]; then
	echo "make sure to install AWSCLI"
	exit 0
else
	avidownloads=$(AWS_ACCESS_KEY_ID=$old_aws_access_key_id AWS_SECRET_ACCESS_KEY=$old_aws_secret_access_key aws s3 ls s3://$old_us3bucket/ --recursive --summarize | grep "Total Objects")
	avidownloadseu=$(AWS_ACCESS_KEY_ID=$old_aws_access_key_id AWS_SECRET_ACCESS_KEY=$old_aws_secret_access_key aws s3 ls s3://$old_eus3bucket/ --recursive --summarize | grep "Total Objects")
	prodavidownloadsus=$(AWS_ACCESS_KEY_ID=$new_aws_access_key_id AWS_SECRET_ACCESS_KEY=$new_aws_secret_access_key aws s3 ls s3://$new_us3bucket/ --recursive --summarize | grep "Total Objects")
      	prodavidownloadseu=$(AWS_ACCESS_KEY_ID=$new_aws_access_key_id AWS_SECRET_ACCESS_KEY=$new_aws_secret_access_key aws s3 ls s3://$new_eus3bucket/ --recursive --summarize | grep "Total Objects:")
	echo "OLD_AVIDOWNLOADS_US: $avidownloads"
	echo "NEW_AVIDOWNLOADS_US: $prodavidownloadsus"
	echo "OLD_AVIDOWNLOADS_EU: $avidownloadseu"
	echo "NEW_AVIDOWNLOADS_EU: $prodavidownloadseu"
	AWS_ACCESS_KEY_ID=$old_aws_access_key_id AWS_SECRET_ACCESS_KEY=$old_aws_secret_access_key aws s3 ls s3://$old_us3bucket --recursive | awk '{$1=$2=$3=""; print $0}' | sed 's/^[ \t]*//' | sort > old_bucket_us.txt
	AWS_ACCESS_KEY_ID=$new_aws_access_key_id AWS_SECRET_ACCESS_KEY=$new_aws_secret_access_key aws s3 ls s3://$new_us3bucket --recursive | awk '{$1=$2=$3=""; print $0}' | sed 's/^[ \t]*//' | sort > new_bucket_us.txt
        AWS_ACCESS_KEY_ID=$old_aws_access_key_id AWS_SECRET_ACCESS_KEY=$old_aws_secret_access_key aws s3 ls s3://$old_eus3bucket --recursive | awk '{$1=$2=$3=""; print $0}' | sed 's/^[ \t]*//' | sort > old_bucket_eu.txt
	AWS_ACCESS_KEY_ID=$new_aws_access_key_id AWS_SECRET_ACCESS_KEY=$new_aws_secret_access_key aws s3 ls s3://$new_eus3bucket --recursive | awk '{$1=$2=$3=""; print $0}' | sed 's/^[ \t]*//' | sort > new_bucket_eu.txt
	diff -y -w old_bucket_us.txt new_bucket_us.txt | tee output_us_diff.txt
	diff -y -w old_bucket_eu.txt new_bucket_eu.txt | tee output_eu_diff.txt
	rm -rf old_bucket_us.txt old_bucket_eu.txt new_bucket_eu.txt new_bucket_us.txt
	exit 1
fi





