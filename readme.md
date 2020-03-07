# Summary

Udemy講座の勉強メモです。

[Certified Kubernetes Administrator (CKA) with Practice Tests](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/)


## 23.Practice Test - Pods
一からpodを作るチートシートのリンク先を探す。
"k run"で作った場合、applyするファイルはどこにあるのか？

```yamlの出力コマンド
kubectl run mydeploy --image nginx -o yaml  --generator=run-pod/v1 --dry-run
```


kubectl editでどうやってfixするか？
->　上記でyamlできたらapplyすればいいからskipする。

## 26.Practice Test - ReplicaSet

* yamlのissue fixについて
まず、実行してエラーの内容を確認。
それから
```
k explain rs
```
にて
内容を確認する

```確認結果
master $ k explain rs
KIND:     ReplicaSet
VERSION:  apps/v1

DESCRIPTION:
     ReplicaSet ensures that a specified number of pod replicas are running at
     any given time.

FIELDS:
   apiVersion   <string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources

   kind <string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds

   metadata     <Object>
     If the Labels of a ReplicaSet are empty, they are defaulted to be the same
     as the Pod(s) that the ReplicaSet manages. Standard object's metadata. More
     info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

   spec <Object>
     Spec defines the specification of the desired behavior of the ReplicaSet.
     More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status

   status       <Object>
     Status is the most recently observed status of the ReplicaSet. This data
     may be out of date by some window of time. Populated by the system.
     Read-only. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status
```

---
The ReplicaSet "replicaset-2" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"tier":"nginx"}: `selector` does not match template `labels`

-> Tierってなんだ？？？
---


## yamlファイルの作り方

```Create an NGINX Pod
kubectl run --generator=run-pod/v1 nginx --image=nginx

```



```Generate POD Manifest YAML file (-o yaml). Don't create it(--dry-run)
kubectl run --generator=run-pod/v1 nginx --image=nginx --dry-run -o yaml
```


```Create a deployment
kubectl create deployment --image=nginx nginx
```


```Generate Deployment YAML file (-o yaml). Don't create it(--dry-run)
kubectl create deployment --image=nginx nginx --dry-run -o yaml
```

```Generate Deployment YAML file (-o yaml). Don't create it(--dry-run) with 4 Replicas (--replicas=4)
kubectl create deployment --image=nginx nginx --dry-run -o yaml > nginx-deployment.yaml
```

## 30. PracticeTest Deployment


特になし

## 32. Practice Test - Namespaces

* 全てのnamespaceのpodを探す。

```
k get po --all-namespaces
```

## 35.Practice Test - Services

## Tips

kubectl create deployment does not have a --replicas option. 
You could first create it and then scale it using the kubectl scale command.

## 37. Practice Test - Imperative Commands

* kubectl run と　kubectl createの違いを押さえておく。
* どっちがいずれなくなるんだっけ？ ->完全ガイドを読んで確認しておく。


## 43. Practice Test - Manual Scheduling

* Podを指定のNodeにデプロイするには

[spec]-[nodeName]に指定
```
master $ cat nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  -  image: nginx
     name: nginx
  nodeName: node01
```


## 45. Practice Test - Labels and Selectors

* Lableを指定してobjectを検索する。

--selectorを利用
```
kubectl get pods --selector env=dev
```
* 複数labelを指定してobjectを検索する。
,(カンマ)を使って指定
```
kubectl get all --selector env=prod,bu=finance,tier=frontend
```

## 48. Practice Test - Taints and Tolerations
* nodeにtaintをつける
```
kubectl taint nodes node01 spray=mortein:NoSchedule
```
key=value:Effect


* podにtolerationを設定する

マニフェストファイルに以下を記述する。
spec.tolerations
```
spec:
  containers:
  - image: nginx
    name: bee
    resources: {}
  tolerations:
   - key: spray
     value: mortein
     effect: NoSchedule
 ```
 
 * nodeからtaintを削除する。
 
 effectの末尾に`-`をつける。
 
 ```
 kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule-
 ```
 
* TaintとTolerationの関係

>例えばCLIからnodeにTaintsを設定する場合、
>`kubectl taint nodes node1 key=value:effect`
>のような形式で実行する。
>Taintsはkeyとvalueとeffectの3つで構成されている。
>(effectの説明は後でするとして)もし以下のように実行した場合、
>`kubectl taint nodes node1 app=batch:NoSchedule`
>Tolerationsに`app=batch`をもったpodではないとこのnodeにpodをscheduleできなくなる。
[※引用](https://qiita.com/sheepland/items/8fedae15e157c102757f)


## 51. Practice Test - Node Affinity
* nodeにlabelをつける
```
kubectl label node node01 color=blue
```

* node affinityの書き方
kubernetes.ioから探せばOK

* key/operator/valueの意味を理解しておく必要がある。
operatorはkeyに対するvalueの等式


|  値           |  意味  |
| ----          | ---- |
|  In  |  ラベルの値が指定したValueのいずれかに一致する  |
|  NotIn  |  ラベルの値が指定したValueのいずれにも一しない時  |
|  Exists  |  ラベルが存在する時  |
|  DoesNotExist  |  ラベルが存在しない時  |
|  Gt  |  ラベルの値が指定した値よりも大きい時  |
|  Lt  |  ラベルの値が指定した値よりも小さい時  |

	
