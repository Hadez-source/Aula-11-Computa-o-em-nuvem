# Aula-11-Computa-o-em-nuvem
Erick da Silva Rodrigues TADS

Passo 1
root@controlplane:~$ kubectl get nodes 
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   12d   v1.37.1
node01         Ready    <none>          12d   v1.37.1
root@controlplane:~$ kubectl create namespace aula11
namespace/aula11 created
root@controlplane:~$ kubectl get namespace aula11
NAME     STATUS   AGE
aula11   Active   18s
root@controlplane:~$ 
