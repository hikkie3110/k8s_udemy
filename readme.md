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

	
## 56. Practice Test - Resource Requirements and Limits
* 既存のpodをyamlファイルに出力する。

```
kubectl get pods -o yaml nginx-test-58586b9f9c-4qqk7
```

## 59. Practice Test - DaemonSets

特になし(kubernetes.ioからyamlファイルをコピーして作れた。）

## 61. Practice Test - Static Pods

* static pod pathの特定方法

```
$ ps -aux | grep kubelet
root      2053  5.5  4.6 944284 95720 ?        Ssl  18:58   0:09 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --cgroup-driver=cgroupfs --cni-bin-dir=/opt/cni/bin --cni-conf-dir=/etc/cni/net.d --network-plugin=cni
```
実行結果のconfigに指定されているconfig.yamlにstatpodpathの記述が入っている。
そこにyamlファイルをおけばOK

## 63. Practice Test - Multiple Schedulers

* namespaceの指定
--namespace=kube-system dem　　　　ででも良いが
-n kube-systemの方が楽。

* podにscheduler設定を入れるには
　spec.schedulerNameに　schedulerを指定する。
 
 
 ## 70. Practice Test - Monitoring
 
* ディレクトリ内にあるマニフェストファイルを一度にapplyする。
```
k apply -f ./
```

 ## 73. Practice Test - Monitor Application Logs

* pod内にコンテナが複数ある場合のlogコマンド
```
k logs [pod名] -c [container名]
```
## 79. Practice Test - Rolling Updates and Rollbacks

特になし

## 84. Practice Test - Commands and Arguments


* commandとargsだけ変えても`k apply -f`でupdateはできない。

```
aster $ k apply -f ubuntu-sleeper-3.yaml
The Pod "ubuntu-sleeper-3" is invalid: spec: Forbidden: pod updates may not change fields other than `spec.containers[*].image`, `spec.initContainers[*].image`, `spec.activeDeadlineSeconds` or `spec.tolerations` (only additions to existing tolerations)
```


* podとdockerfileでの起動時挙動の記述について
```
The 'command' (entrypoint) is overridden in the pod definition. So the answer is --color green
```

## 87. Practice Test: Environment Variables

特になし

## 90. Practice Test - Secrets

* No.7の設問がよくわからなかった。解説も回答もないので再度チャレンジ。どうやってsecretを適用するか。

## 93. Practice Test - Multi Container PODs

* No.8-9がわからない。
そもそも'app'のpodがないから何をしたらいいのか不明。


## 96. Practice Test - Init Containers

特になし


## 102. Practice Test - OS Upgrades

* メンテ前：daemonsetを無視して、nodeからpodを追い出す。
```
kubectl drain node01 --ignore-daemonsets
```

* メンテ後：ノードの復帰

|  コマンド  |  意味  |
| ----          | ---- |
|  `k cordon`  |  ノードをclusterから除外する（今動いているpodは追い出さない）  |
|  `k uncordon`  |  ノードをclusterに復帰させる  |


## 106. Practice Test - Cluster Upgrade

* clusterのversionを調べる

`kubectl version`


```
master $ k version
Client Version: version.Info{Major:"1", Minor:"16", GitVersion:"v1.16.0", GitCommit:"2bd9643cee5b3b3a5ecbd3af49d09018f0773c77", GitTreeState:"clean", BuildDate:"2019-09-18T14:36:53Z", GoVersion:"go1.12.9", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"16", GitVersion:"v1.16.0", GitCommit:"2bd9643cee5b3b3a5ecbd3af49d09018f0773c77", GitTreeState:"clean", BuildDate:"2019-09-18T14:27:17Z", GoVersion:"go1.12.9", Compiler:"gc", Platform:"linux/amd64"}

```

* stableな最新バージョンの調べ方

`kubeadm upgrade plan`

* upgradeの手順

** kubeletのupgrade

```apt install kubeadm=1.17.0-00```

```apt install kubelet=1.17.0-00```



** kubeadmのupgradeコマンド実行

```kubeadm upgrade node config --kubelet-version $(kubelet --version | cut -d ' ' -f 2)```


## 109. Practice Test - Backup and Restore Methods

* backup/restoreコマンド

あとでかく。


## 124. Practice Test - View Certificates

* `kube-apiserver`の認証情報の確認をする。

マニフェストファイルをみる。
```
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```

```
cat /etc/kubernetes/manifests/etcd.yaml
```

* CNの調べ方

```openssl x509 -in file-path.crt -text -noout```を実行する。

ex1.
```
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text
```


* No.12以降がよくわからない。


* Sign a new certificate for the apiserver-etcd-client
```
openssl x509 -req -in /etc/kubernetes/pki/apiserver-etcd-client.csr -CA /etc/kubernetes/pki/etcd/ca.crt -CAkey /etc/kubernetes/pki/etcd/ca.key -CAcreateserial -out /etc/kubernetes/pki/apiserver-etcd-client.crt
```


## 126. Practice Test - Certificates API

* CSRの作り方

[qiita](https://qiita.com/knqyf263/items/aefb0ff139cfb6519e27)

1. csrをbase64化する。
 ```
 cat akshay.csr | base64 | tr -d '\n'
 ```
2. yamlファイル作成
1. approveする。(rejectする場合はdenyコマンド)
```
kubectl certificate approve akshay
``` 

## 128. Practice Test - KubeConfig

* clusteryやユーザについてkubeconfigを調べる。

```
kubectl config view
```

config fileを見ることも可能

```
kubectl config view --kubeconfig my-kube-config
```

* contextを切り替える

```
kubectl config --kubeconfig=/root/my-kube-config use-context research
```

* kubeconfigを切り替える。

configファイルの入れかえ。

`$HOME/.kube/config`

## 132. Practice Test - RBAC

* authorization modeの確認

kube-apiserverの `--authorization-mode=` を確認する。

```
kubectl describe pod kube-apiserver-master -n kube-system
```

* roleがasignされているアカウントを確認する。

```
kubectl describe rolebinding weave-net -n kube-system
```

* あるユーザの権限でコマンドを実行する。

`--as [User]`で実行

```
k get po --as dev-user
```

* role/rolebindingの作成


## 134. Practice Test - Cluster Roles and Role Bindings




