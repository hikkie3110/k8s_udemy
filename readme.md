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


