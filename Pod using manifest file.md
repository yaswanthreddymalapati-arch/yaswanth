### Pod using manifest file



kind: Pod

apiVersion: v1

metadata:

  name: nginx-pod

  namespace: nginx

spec:

  containers:

  - name: nginx

    image: nginx:1.14.2

    ports:

    - containerPort: 80



kubectl apply -f pod.yml

kubectl get po -n nginx

kubectl exec -it nginx-pod -n nginx -- bash    #to go to the pod

ls

exit

 kubectl describe po nginx-pod -n nginx        #to describe the pod

kubectl delete po nginx-pod -n nginx           #delete the pod





### \# DaemonSet / Replicaset / Statefullset  / Deploymeny.



vi deployment.yml



apiVersion: apps/v1

kind: Deployment

metadata:

  name: nginx-deployment

  namespace: nginx

  labels:

    app: nginx

spec:

  replicas: 3

  selector:

    matchLabels:

      app: nginx

  template:

    metadata:

      labels:

        app: nginx

    spec:

      containers:

      - name: nginx

        image: nginx:1.14.2

        ports:

        - containerPort: 80



\# kubectl apply -f deployment.yml

kubectl get deploy -n nginx

kubectl get po -n nginx                    #to check the no of pod

kubectl delete po nginx name -n nginx      # to delete the pod

kubectl get po -n nginx -o wide            # to check full details

kubectl scale deploy nginx-deployment -n nginx --replicas=4  #to change the repicas

to check again the following steps

kubectl edit po -n nginx-deployment -n nginx



###### The above process is known as rolling update

#### 

#### ReplicaSet.



\# vi replicaset.yml



apiVersion: apps/v1

kind: ReplicaSet

metadata:

  name: nginx-deployment

  namespace: nginx

  labels:

    app: nginx

spec:

  replicas: 3

  selector:

    matchLabels:

      app: nginx

  template:

    metadata:

      labels:

        app: nginx

    spec:

      containers:

      - name: nginx

        image: nginx:1.14.2

        ports:

        - containerPort: 80



\# kubectl create -f replicaset.yml

\# kubectl get replicaset -n ngnix



Note:- Rather than replicaset the advanced part is deployment concept. In deployment we can do the rolling update, rollout etc. so that in real time rather than replicaset we use deployment only.





kind: persistenVolume

apiVersion: v1

metadata:

  name: local-pv

  namespace: nginx

  lables:

    app: local

spec:

  capacity:

    storage: 1Gi

  accesModes:

  - ReadModeOnce

  presistentVolumeReclamPolicy: Retain

  storageClassName: local-storage

  hostPath:

    path: /mnt/data









apiVersion: v1

kind: PersistentVolumeClaim

metadata:

  name: local-pvc

  namespace: nginx

  labels:

    app: local

spec:

  accessModes:

    - ReadWriteOnce

  resources:

    requests:

      storage: 1Gi

  storageClassName: local-storage



 kubectl apply -f persistenvolumeclam.yml -n nginx

kubectl get pvc -n nginx

kubectl get pv -n nginx

kubectl get all -n nginx





apiVersion: apps/v1

kind: Deployment

metadata:

  name: nginx-deployment

  namespace: nginx

  labels:

    app: nginx

spec:

  replicas: 4

  selector:

    matchLabels:

      app: nginx

  template:

    metadata:

      labels:

        app: nginx

    spec:

      containers:

      - name: nginx

        image: nginx:1.14.2

        ports:

        - containerPort: 80

        volumeMounts:

        - name: my-volume

          mountPath: /var/www/html

      volumes:

      - name: my-volume

        persistentVolumeClaim:

          claimName: local-pvc

