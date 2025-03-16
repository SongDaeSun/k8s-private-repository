# k8s-private-repository
직접 만든 docker image를 k8s환경에서 사용하려면  
기존의 docker hub가 아닌 repository가 필요하다.  

docker repository를 설치하고,  
이를 내부의 k8s cluster가 사용할 수 있도록 설정해보자.

# docker 설정
docker repository가 설치될 노드에 다음의 명령어를 주입한다.  
  
docker login을 통해 .docker/config.json 파일 생성
```shell
docker login
```

docker repository container 생성
```
docker run -d -p 5000:5000 --restart always --name registry registry:2
```

생성된 repository에 login 정보 생성
```
docker login localhost:5000
```

# kubernetes 전체 설정
### k8s secret 설정  
[명령어]
```
kubectl create secret generic {secret-name} \
  --from-file=.dockerconfigjson=home/{user}/.docker/config.json \
  --type=kubernetes.io/dockerconfigjson
```

[예시]
```
kubectl create secret generic codeverse-secret \
  --from-file=.dockerconfigjson=~/.docker/config.json \
  --type=kubernetes.io/dockerconfigjson
```

secret 확인
```
kubectl get secret
kubectl describe secret
```

### serviceaccount 설정

serviceaccount 확인
[명령어]
```
kubectl describe serviceaccount default -n {namespace_name}
```
[예시]
```
kubectl describe serviceaccount default -n default
```

Image pull secrtes: 항목이 none으로 되어있으면  
아직 imagePullSecrets에 default secret이 할당되어있지 않은 상태.  

serviceaccount 기본 설정 바꾸기
[명령어]
```
kubectl patch -n {namespace_name} serviceaccount/default -p '{"imagePullSecrets":[{"name": "{image_pull_secret_name}"}]}'
```

[예시]
```
kubectl patch -n default serviceaccount/default -p '{"imagePullSecrets":[{"name": "codeverse-secret"}]}'
```


다시 namespace를 확인해보면  
Image pull secrtes: 항목에 secret이 할당되어있는것을 확인할 수 있음.


# 출처

