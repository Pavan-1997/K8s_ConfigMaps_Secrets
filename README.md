# K8s ConfigMaps and Secrets     
              
As a comprehensive container orchestration platform, Kubernetes offers a fully-featured platform to support all your application needs from container lifecycle management to networking services, etc. Managing application configurations and sensitive data such as passwords is a core consideration in almost any software development. Kubernetes uses ConfigMaps and Secrets to facilitate this need in containerized application development.
  
- ConfigMaps:         
  
  The main purpose of using a ConfigMap is to decouple the application configuration data from the application itself. This separation improves the reusability of the container as a decoupled architecture enables developers to use and test containerized applications under different configurations. However, ConfigMaps are not designed to handle large amounts of data and are capped at 1MB. Developers need to look for other solutions such as volumes or external data sources if configuration data exceeds that limit. Thus, ConfigMaps are ideal for implementing non-sensitive configuration data less than 1MB in total size.
 

- Secrets:
  
  As an independent entity, secrets also ensure that this confidential data will not be exposed during the Pod creation or modification process. Kubernetes will also treat secrets differently and will abstain from writing them to nonvolatile storage. 

---

- The primary difference between these two is that while ConfigMaps are designed to store any type of non-sensitive application data, Secrets are designed to store sensitive application data such as passwords, tokens, etc. Moreover, Secrets must adhere to the specific secret types that determine which kind of data can be handled by the secret.

- Both these K8s objects can be created either via kubectl or a YAML file independently of any other process or object.

---

# Implementing the below three tasks 

TASK 1: Creating ConfigMaps on  Minikube and using as Env variable in Deployments

TASK 2: Creating ConfigMaps and Volume on  Minikube and using as Volumemounts file in Deployments

TASK 3: Creating Secrets and Volume on  Minikube and using as Volumemounts file in Deployments


1. Create the Kubernetes cluster using Minikube 

    Follow the Minikube Instructions in OneNote 


2. Clone the repo for manifest files


3. Now deploy the configmap manifest file in the repo 
```
kubectl apply -f configmap.yml
```

4. To check the configmap created 
```
kubectl get cm
```

![image](https://github.com/Pavan-1997/K8s_ConfigMaps_Secrets/assets/32020205/e2ce82b6-3a85-4390-8de7-40b4bf05c595)


5. To check inside the configmap created 
```
kubectl describe cm sample-python-app-configmap
```

6. Now login to anyone of pod, we are doing these to view the enivorment variable of the pod
```
kubectl get pods
```
```
kubectl exec -it <pod-name> -- /bin/bash
```

7. Now search for enviroment variable db inside the pod
```
env | grep DB
```
  This will show no env variable as of now
  
![image](https://github.com/Pavan-1997/K8s_ConfigMaps_Secrets/assets/32020205/68a83562-db06-4565-bc63-b0de6d6b6807)

8. Modifying the deployemnt manifest file by adding tasks 

- Create a environmental variable in the Pod
- Map the Configmap file name that was created earlier to it 
- Specify the key of the variable as created in the Configmap

    The above changes are implemented in deployemnt_cm.yml file 


9. Now login to Pod and check for environment variable DB

    Follow Step 6. , 7.
    
    You should be seeing the DB port mapped

![image](https://github.com/Pavan-1997/K8s_ConfigMaps_Secrets/assets/32020205/27ee85f9-98d0-4351-9834-d3b2ef0b0eaa)


11. Once this is mapped, Now the developer inside his Java/Python application he can just say as below and retreive the value for his DB connection
```
os.env("DB-PORT") 
```

11. Considering a scenario, If Admin wants to change the DB-PORT in the deployemnt manifest file but doing this doesn't change the port in the Pod and app still uses the old DB port untill and unless the changed deployment is applied on the cluster which can lead to prod issues.

    To solve this problem: 

    We can use VolumeMounts, where the variables are stored in a file rather than passing in enivorment, which are attached to the Pods  

    Modifying the deployemnt manifest file by adding tasks 

- Create a volume
- Map the Configmap file name that was created earlier to it
- Mount the volume
- Give the mount path 

    The above changes are implemented in deployemnt_cm_vol.yml file 


12. Now login to Pod and check and view the file 

Follow Step 6. 
```
cd /opt
```
```
cat db-port | more
```

13. Now change the port number in the Configmap and apply the manifest file to the cluster 

    Now login back to the pod and view the port in the file which should now have the updated port number, this is how we can solve the problem in Step 11.


14. Now creating secrets, secrets are of different types like generic, tls etc

    Now create a secret, this is another way of creating the Configmap/Secret in a single line
```
kubectl create secret generic sample-python-app-secret --from-literal=db-port="3306"
```

15. To check inside the secret created 
```
kubectl describe secret sample-python-app-secret
```

![image](https://github.com/Pavan-1997/K8s_ConfigMaps_Secrets/assets/32020205/b35f526b-b0c1-4cbd-9c44-d9694aceee2f)

   As you see the value for the db-port in base64 encryption

   The encryption can also be seen by using the below 
```
kubectl edit secret sample-python-app-secret
```
   Now to just convert the base64 value use below
```
echo <encrypted-text> | base64 --decode | more
```

![image](https://github.com/Pavan-1997/K8s_ConfigMaps_Secrets/assets/32020205/afd0d4f0-d7e2-4176-85e9-a29d33867da5)

   By defauld K8s doesnt provide a good encryption, hence we can use custom encryption from Hashicorp Vault, Seal Secrets at namespace level or during K8s secret creation at the rest we have pass it to API server using the etcd


15. Doing the Step 11. , 12. by replacing Configmap with secret

    The above changes are implemented in deployemnt_sec_vol.yml file 
    
    Apply the above deployment file and follow the above steps as did for Configmap volumes and check the db_port value inside the Pod

![image](https://github.com/Pavan-1997/K8s_ConfigMaps_Secrets/assets/32020205/00b8d9e3-1f93-4d3d-9f34-2357042495fb)

