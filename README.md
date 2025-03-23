# k8s-in-proxmox

Here I have spinned up several VMs on my proxmox server and install k8s. Here is my spec.

![Screenshot (88)](https://github.com/user-attachments/assets/1e0dd2ad-6332-4cac-b688-41a09d78f288)

```
controller - 2 core. At least 2GB Ram ( 192.168.0.19 )
node 1 - 2 core. At least 1GB Ram ( 192.168.0.22 )
node 2 - 2 core. At least 1GB Ram ( 192.168.0.24 )
node 3 - 2 core. At least 1GB Ram ( 192.168.0.27 )
```

## The excellent tutorial I follow
- https://www.youtube.com/watch?v=U1VzcjCB_sY&t=462s
- https://www.learnlinux.tv/how-to-build-an-awesome-kubernetes-cluster-using-proxmox-virtual-environment/

Command adjustment due to kubernetes-xenial release obsolete

- https://discuss.kubernetes.io/t/e-the-repository-https-apt-kubernetes-io-kubernetes-xenial-release-does-not-have-a-release-file/28121
  
```
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo apt update
```

## useful kubectl commands

```
user@k8s-controller:~$ kubeadm token create --print-join-command
kubeadm join 192.168.0.19:6443 --token <hidden> --discovery-token-ca-cert-hash sha256:<hidden>
user@k8s-controller:~$
```

```
user@k8s-controller:~$ kubectl get nodes
NAME             STATUS   ROLES           AGE     VERSION
k8s-controller   Ready    control-plane   16m     v1.28.15
k8s-node1        Ready    <none>          5m5s    v1.28.15
k8s-node2        Ready    <none>          4m54s   v1.28.15
k8s-node3        Ready    <none>          115s    v1.28.15
```

```
user@k8s-controller:~$ kubectl get pods
NAME            READY   STATUS    RESTARTS   AGE
nginx-example   1/1     Running   0          14s
```

```
user@k8s-controller:~$ kubectl apply -f pod.yml
pod/nginx-example created

user@k8s-controller:~$ kubectl apply -f service-nodeport.yml
service/nginx-example created

user@k8s-controller:~$ kubectl get service
NAME            TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes      ClusterIP   10.96.0.1      <none>        443/TCP        38m
nginx-example   NodePort    10.99.12.144   <none>        80:30080/TCP   13s
user@k8s-controller:~$
```

```
user@k8s-controller:~$ kubectl get pods -o wide
NAME            READY   STATUS    RESTARTS   AGE   IP           NODE        NOMINATED NODE   READINESS GATES
nginx-example   1/1     Running   0          76s   10.244.3.2   k8s-node3   <none>           <none>
```

## nginx on browser

- To show nginx is working on k8s and can be connected on web browser
- And it can be connected by any k8s-controller, k8s-node addresses

http://192.168.0.19:30080/
http://192.168.0.22:30080/
http://192.168.0.24:30080/
http://192.168.0.27:30080/

![image](https://github.com/user-attachments/assets/f564cb40-2cb8-422b-af5c-771dbce412da)

