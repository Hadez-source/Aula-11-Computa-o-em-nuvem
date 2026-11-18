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

Passo 2
root@controlplane:~$ kubectl -n aula11 create job tarefa \
  --image=busybox:1.36 -- sh -c \
  'echo Inicio; date; sleep 3; echo Concluido'
job.batch/tarefa created
root@controlplane:~$ 
kubectl -n aula11 get job tarefa
NAME     STATUS    COMPLETIONS   DURATION   AGE
tarefa   Running   0/1           10s        10s
root@controlplane:~$ kubectl -n aula11 get pods
NAME           READY   STATUS      RESTARTS   AGE
tarefa-58c84   0/1     Completed   0          22s
