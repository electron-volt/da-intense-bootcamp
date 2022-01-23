# Test

save\_var PROD\_ALB\_URL $(\
aws cloudformation describe-stacks\
\--stack-name prod-cluster\
\--query "Stacks\[0].Outputs\[?OutputKey == 'ExternalUrl'].OutputValue"\
\--output text\
) curl $PROD\_ALB\_URL/hello/bob



or&#x20;

echo $PROD\_ALB\_URL
