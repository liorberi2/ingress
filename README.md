
1. Deploy a demo application in namespace called “namespace-a”

kubectl config set-context --current --namespace=namespace-a

2. Deploy another demo application in other namespace called “namespace-b”

kubectl config set-context --current --namespace=namespace-b

3. Create a namespace called “jobs”

kubectl config set-context --current --namespace=jobs

4. Create a Cronjob in the “jobs” namespace called “job-a” that run an ubuntu container 
and run the command “kubectl get all -n namespace-a” (ensure it have only get access permissions for the namespace “namespace-a”)
