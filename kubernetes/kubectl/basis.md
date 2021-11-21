# Kubernetes

## Reference

* [kubectl](https://kubernetes.io/ja/docs/reference/kubectl/overview/)

## Create Resource

リソースは `kubectl create` コマンドを利用して生成する。

```
$ kubectl create -f <manifest>
[Example]
$ kubectl create -f pod.yaml
```

## Get Resource

リソースの情報は `kubectl get` コマンドを利用して取得する。

```
$ kubectl get <kind>
[Example]
$ kubectl get pods
```

特定の名前のリソースを取得するには `<resourceName>` を指定する。

```
$ kubectl get <kind> <resourceName>
[Example]
$ kubectl get pods sample-pod
```

リソースの状態を監視するには `--watch` を指定する。

```
$ kubectl get pods --watch
```

## Get Resource Detail

リソースの詳細情報は `kubectl describe` コマンドを利用して取得する。

```
$ kubectl describe <kind> <resourceName>
[Example]
$ kubectl describe pods sample-pod
```

## Execute Command in a Running Container

Pod内の特定のコンテナ上でコマンドを実行するには `kubectl exec` コマンドを利用する。

```
[Example]
# env command
$ kubectl exec -ti sample-pod -- env

# login container
$ kubectl exec -ti sample-pod -- /bin/sh
```

## Print Log for a Running Container

Pod内のコンテナのログを出力するには `kubectl logs` コマンドを利用する。

```
$ kubectl logs <podName>
[Example]
# print logs for a single container
$ kubectl logs sample-pod
# print logs for a specified container
$ kubectl logs sample-pod -c sample-container
```

## Copy File and Directory to and from Container

コンテナとローカルマシン間でファイルをコピーするには `kubectl cp` コマンドを利用する。

```
[Copy from Container]
$ kubectl cp <podName>:<containerFilePath>
 <localFilePath>
[Example]
$ kubectl cp sample-pod:etc/hostname ./hostname

[Copy to Container]
$ kubectl cp  <localFilePath> <podName>:<containerFilePath> 
[Example]
$ kubectl cp ./hostname sample-pod:/tmp/newfile
```

## Debug Pod

Podが起動しないとき、以下の方法でデバッグする。
* アプリケーション側に問題があるとき `kubectl logs` コマンドを利用する。
* Kubernetesの設定やリソースの設定に問題があるとき `kubectl describe` コマンドを利用する。
* コンテナ内の環境やアプリケーションに問題があるとき `kubectl run` コマンドを利用し、ENTRYPOINTを上書きする。

```
$ kubectl run --image=<image> --restart=Never --rm -ti <podName> --command -- /bin/sh
[Example]
$ kubectl run --image=nginx:1.16 --restart=Never --rm -ti sample-pod --command -- /bin/sh
```


## Update Resource

リソースは `kubectl apply` コマンドを利用して更新する。

```
$ kubectl apply -f <manifest>
```

## Edit Resource

デプロイ済みのリソースは `kubectl edit` コマンドを利用して編集する。

```
$ kubectl edit <kind> <resourceName>
```

## Remove Resource

リソースは `kubectl delete` コマンドを利用して削除する。

```
$ kubectl delete -f <manifest>
```

## Get Hands-On Practice

### Create Namespace

Namespaceはクラスタを仮想的に分離する。

```
# mynamespace.yaml
---
apiVersion: v1
kind: Namespace
metadata:
  name: mynamespace
```

```
$ kubectl apply -f mynamespace.yaml
```

### List Namespace

Namespaceを表示する。

```
$ kubectl get namespaces
```

### Create Pod

Podを作成する。

```yaml
# pod.yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: mynamespace
spec:
  containers:
  - args:
    - /bin/sh
    - -c
    - sleep 3600
    image: busybox
    name: busybox
  restartPolicy: Never
```

```
$ kubectl apply -f pod.yaml
```

### Get Pod

Podの情報を取得する。

```
$ kubectl get pods busybox -n mynamespace
```

### Get Pod Detail

Podの詳細情報を取得する。

```
$ kubectl describe pod busybox -n mynamespace
```

### Execute Command in a Running Container

Pod内のコンテナにログインする。

```
$ kubectl exec -ti busybox -n mynamespace -- /bin/sh
```

### Remove Pod

Podを削除する。

```
$ kubectl delete -f pod.yaml
```

### Remove Namespace

Namespaceを削除する。このときNamespaecに紐づく全てのリソースが削除される。

```
$ kubectl delete -f mynamespace.yaml
```
