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

Passo 3
root@controlplane:~$ kubectl -n aula11 wait --for=condition=complete \
  job/tarefa --timeout=120s
job.batch/tarefa condition met
root@controlplane:~$ kubectl -n aula11 get job tarefa
NAME     STATUS     COMPLETIONS   DURATION   AGE
tarefa   Complete   1/1           12s        90s
root@controlplane:~$ kubectl -n aula11 logs job/tarefa
Inicio
Wed Nov 18 01:31:05 UTC 2026
Concluido

Passo 4
root@controlplane:~$ cat > lote.yaml <<'EOF'
apiVersion: batch/v1
kind: Job
metadata: {name: lote, namespace: aula11}
spec:
  completions: 3
  parallelism: 2
  template:
    spec:
      containers:
      - name: lote
        image: busybox:1.36
        command: ['sh', '-c', 'date; sleep 5; echo OK']
      restartPolicy: Never
EOF
root@controlplane:~$  kubectl apply -f lote.yaml
job.batch/lote created
root@controlplane:~$ kubectl -n aula11 get pods -w
NAME           READY   STATUS      RESTARTS   AGE
lote-p4tjw     0/1     Completed   0          26s
lote-qkwh7     0/1     Completed   0          26s
lote-z5jn6     0/1     Completed   0          17s
tarefa-b22np   0/1     Completed   0          5m5s

Passo 5
root@controlplane:~$ kubectl -n aula11 create cronjob relogio \
  --image=busybox:1.36 \
  --schedule='*/1 * * * *' -- \
  sh -c 'date; echo Execucao agendada'
cronjob.batch/relogio created
root@controlplane:~$ kubectl -n aula11 get cronjob relogio
NAME      SCHEDULE      TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
relogio   */1 * * * *   <none>     False     0        29s             43s

Passo 6
root@controlplane:~$ kubectl -n aula11 create job teste-agora \
  --from=cronjob/relogio
job.batch/teste-agora created
root@controlplane:~$ kubectl -n aula11 wait --for=condition=complete \
  job/teste-agora --timeout=120s
job.batch/teste-agora condition met
root@controlplane:~$ kubectl -n aula11 logs job/teste-agora
Wed Nov 18 01:50:18 UTC 2026
Execucao agendada

