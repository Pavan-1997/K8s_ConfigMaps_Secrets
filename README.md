# K8s ConfigMaps and Secrets

As a comprehensive container orchestration platform, Kubernetes offers a fully-featured platform to support all your application needs from container lifecycle management to networking services, etc. Managing application configurations and sensitive data such as passwords is a core consideration in almost any software development. Kubernetes uses ConfigMaps and Secrets to facilitate this need in containerized application development.

ConffigMaps:

The main purpose of using a ConfigMap is to decouple the application configuration data from the application itself. This separation improves the reusability of the container as a decoupled architecture enables developers to use and test containerized applications under different configurations. However, ConfigMaps are not designed to handle large amounts of data and are capped at 1MB. Developers need to look for other solutions such as volumes or external data sources if configuration data exceeds that limit. Thus, ConfigMaps are ideal for implementing non-sensitive configuration data less than 1MB in total size.


Secrets:

As an independent entity, secrets also ensure that this confidential data will not be exposed during the Pod creation or modification process. Kubernetes will also treat secrets differently and will abstain from writing them to nonvolatile storage. 

---

- The primary difference between these two is that while ConfigMaps are designed to store any type of non-sensitive application data, Secrets are designed to store sensitive application data such as passwords, tokens, etc. Moreover, Secrets must adhere to the specific secret types that determine which kind of data can be handled by the secret.

- Both these K8s objects can be created either via kubectl or a YAML file independently of any other process or object. 
