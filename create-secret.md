kubectl create secret generic codeverse-secret \
  --from-file=.dockerconfigjson=/home/song/.docker/config.json \
  --type=kubernetes.io/dockerconfigjson


kubectl get secret
kubectl describe secret



----  

[명령어]
kubectl describe serviceaccount default -n <namespace_name>

[예쩨]
kubectl describe serviceaccount default -n default

Image pull secrtes: 항목이 none으로 되어있으면 
아직 imagePullSecrets에 default secret이 할당되어있지 않은 상태.
----


[명령어]
kubectl patch -n <namespace_name> serviceaccount/default -p '{"imagePullSecrets":[{"name": "<image_pull_secret_name>"}]}'
 
[예제]
kubectl patch -n default serviceaccount/default -p '{"imagePullSecrets":[{"name": "codeverse-secret"}]}'


----

2.3. 확인
다시 namespace를 확인해보면
Image pull secrtes: 항목에 secret이 할당되어있는것을 확인할 수 있음.
이후 pod생성 시 imagePullSecrets 옵션이 없어도
Private Registry에서 image를 pull 해올 수 있음.
