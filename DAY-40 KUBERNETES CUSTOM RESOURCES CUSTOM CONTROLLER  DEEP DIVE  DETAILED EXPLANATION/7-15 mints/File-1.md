### Concepts Related to Custom Resource Definitions (CRD), Custom Resources (CR), and Custom Controllers in Kubernetes:

#### 1. **Extending Kubernetes API:**
Kubernetes provides default resources like `Deployment`, `Service`, `Pod`, `ConfigMap`, and `Secrets`. These are built-in resources that come "out of the box" and are handled by Kubernetes' internal components. However, if a functionality is not covered by these resources, Kubernetes allows users to extend its API using **Custom Resource Definitions (CRD)**.

##### Purpose:
- The purpose of extending Kubernetes' API is to introduce new resources that can handle specialized functionalities or applications, such as advanced security features, GitOps, service meshes, etc.
  
  Example: Tools like **Istio**, **Argo CD**, or **Keycloak** extend Kubernetes' API by adding new resources to solve specific problems like security, GitOps, or identity management.

##### Scenario:
If you want to use GitOps on Kubernetes with Argo CD, Argo CD defines its own resource types, such as `Application` or `Project`. These new resources are not native to Kubernetes, so Argo CD creates a **Custom Resource Definition (CRD)** that tells Kubernetes how to interpret and validate these resources.

---

#### 2. **Actors in the Custom Resource Ecosystem:**
There are two main actors when dealing with CRDs and custom controllers:
- **DevOps Engineer**: Responsible for deploying CRDs and custom controllers.
- **User**: Can create or manage custom resources based on the CRD.

---

#### 3. **Custom Resource Definition (CRD):**
A **Custom Resource Definition (CRD)** is a YAML file that defines a new type of resource in Kubernetes. It provides a specification for what fields and types a custom resource can contain. The CRD is what extends Kubernetes' API, allowing new resource types to be added.

##### Key Parts of CRD:
- **API Version**: Defines which version of the Kubernetes API this custom resource operates under.
- **Kind**: The type of resource being defined (e.g., `VirtualService` for Istio).
- **Spec**: Describes the specific fields and configurations of the resource.

Example YAML for a CRD (Istio Virtual Service):

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: example-virtual-service
spec:
  hosts:
    - "my-service"
  http:
    - route:
        - destination:
            host: my-service
```

##### Purpose:
- CRD serves to define new resource types in Kubernetes.
- Kubernetes uses CRDs to validate whether the custom resources created by users are correct or not.

---

#### 4. **Custom Resource (CR):**
Once a CRD is defined, users can create **Custom Resources (CR)** that conform to the specifications provided by the CRD. Custom Resources allow users to interact with Kubernetes in a specialized way that wouldn't be possible using just the built-in resources.

##### Example:
- **Istio's VirtualService** is an example of a custom resource defined by a CRD.
- The user creates a `VirtualService` that specifies how traffic should be routed within the service mesh.

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: example-virtual-service
spec:
  hosts:
    - "my-service"
  http:
    - route:
        - destination:
            host: my-service
```

##### Purpose:
- CRs allow users to interact with and configure Kubernetes clusters in ways that go beyond built-in resources. CRDs serve as the template, and CRs are the actual instances of these new resource types.

---

#### 5. **Validation in Kubernetes:**
Kubernetes validates whether a YAML definition (for both native and custom resources) is correct based on a **resource definition**. For native resources like `Deployment` or `Service`, Kubernetes has built-in definitions. For custom resources, Kubernetes uses the CRD to check if the CR is valid.

##### Scenario:
If a user tries to add an invalid field to a custom resource YAML, Kubernetes will throw an error because that field is not part of the CRD's definition.

---

#### 6. **Custom Controller:**
A **Custom Controller** is a process that watches for changes to Custom Resources and ensures that the desired state is achieved. The controller reacts to changes in the state of Custom Resources and makes adjustments to the actual cluster state to match the resource definition.

##### Example:
- If a user defines a `VirtualService` (a custom resource) for Istio, the Istio custom controller will handle the actual implementation of traffic routing rules within the cluster.

##### Purpose:
- Custom controllers ensure that the desired state described by custom resources is applied correctly within the Kubernetes cluster.

---

### Commands and Scenarios

#### 1. **Create a CRD**:
```bash
kubectl apply -f custom-resource-definition.yaml
```

This command will create a new CRD in your Kubernetes cluster. This CRD allows Kubernetes to recognize and handle a new type of resource (e.g., `VirtualService` for Istio).

##### Scenario:
If you're setting up Istio, you would apply the CRD for Istio's `VirtualService`. This would allow Kubernetes to understand this new resource type.

#### 2. **Create a Custom Resource**:
```bash
kubectl apply -f custom-resource.yaml
```

This command creates a custom resource, such as an Istio `VirtualService`, based on the CRD.

##### Scenario:
After defining the CRD for a `VirtualService`, you can now create actual instances of virtual services that handle routing within your Kubernetes service mesh.

#### 3. **List CRDs**:
```bash
kubectl get crds
```

This command lists all the CRDs that are currently installed in your Kubernetes cluster.

##### Scenario:
Running this command will show you all the custom APIs your Kubernetes cluster can handle.

#### 4. **List Custom Resources**:
```bash
kubectl get virtualservices.networking.istio.io
```

This command lists all the `VirtualService` custom resources in your Kubernetes cluster.

##### Scenario:
If you have several custom `VirtualServices` set up in Istio, you can see them listed by running this command.

---

### Conclusion
- **Custom Resource Definitions (CRD)** are used to extend the Kubernetes API, enabling the cluster to recognize new types of resources.
- **Custom Resources (CR)** are instances of these new resources, created and managed by users or DevOps engineers.
- **Custom Controllers** ensure that the desired state defined by the custom resource is achieved and maintained in the cluster.
  
--------------------------------------------------------------------------------------------------------------------------------
# OR
--------------------------------------------------------------------------------------------------------------------------------

Let’s break down each concept and dive deep into their explanations while keeping everything easy to understand, along with scenarios and command outputs. We’ll cover **Custom Resource Definitions (CRD)**, **Custom Resources (CR)**, and **Custom Controllers** in Kubernetes.

### 1. **Custom Resource Definition (CRD)**
#### Explanation:
A **Custom Resource Definition (CRD)** allows Kubernetes users to introduce a new resource to the Kubernetes API. Kubernetes comes with a set of default resources such as Pods, Services, Deployments, ConfigMaps, etc., which are known as "native" resources. However, in certain scenarios, you may want to extend Kubernetes with new, non-native resources that address specific functionalities (like security, identity, or networking enhancements).

When someone wants to introduce a new resource into Kubernetes (e.g., Istio introduces new APIs like Virtual Services or Destination Rules), they define this new resource using a **CRD**. The CRD is essentially a **YAML file** that defines the structure of the new resource, outlining the fields, structure, and validation rules.

#### Example:
Here’s a basic example of a **CRD YAML** for a fictional resource called `MyApp`:
```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: myapps.example.com
spec:
  group: example.com
  versions:
    - name: v1
      served: true
      storage: true
  scope: Namespaced
  names:
    plural: myapps
    singular: myapp
    kind: MyApp
    shortNames:
      - ma
```

#### Purpose:
- **Define a new API type** in Kubernetes.
- **Extend Kubernetes' capabilities** without modifying its core.
- **Validate custom resources** based on the defined structure in the CRD.

#### Command to apply a CRD:
```bash
kubectl apply -f crd.yaml
```

#### Output:
```
customresourcedefinition.apiextensions.k8s.io/myapps.example.com created
```

### 2. **Custom Resource (CR)**
#### Explanation:
A **Custom Resource (CR)** is an instance of the API that was defined by a CRD. After defining a CRD (which acts as a template for a new resource), users can create custom resources based on it. These custom resources function like native Kubernetes resources (e.g., Pods or Services) but offer additional functionality as defined in the CRD.

For example, after creating a CRD for `VirtualService` in Istio, you can create a **Custom Resource (CR)** for `VirtualService` to manage traffic routing between microservices in your Kubernetes cluster.

#### Example:
Here's a YAML file for a **Custom Resource** of `MyApp` created using the CRD above:
```yaml
apiVersion: example.com/v1
kind: MyApp
metadata:
  name: myapp-instance
spec:
  replicas: 3
  image: myapp-image:v1
```

#### Purpose:
- **Introduce new behaviors or features** in the Kubernetes cluster.
- **Extend Kubernetes' functionality** to match the needs of specific applications like Istio (e.g., `VirtualService` in Istio).
- **Allow end users** to create and manage non-native resources.

#### Command to apply a Custom Resource:
```bash
kubectl apply -f custom-resource.yaml
```

#### Output:
```
myapp.example.com/myapp-instance created
```

### 3. **Custom Controller**
#### Explanation:
A **Custom Controller** is a Kubernetes controller that automates the management of **Custom Resources**. Kubernetes controllers continuously observe the state of the cluster and reconcile the actual state with the desired state defined by users (for native resources like Deployments, Pods, etc.). Similarly, custom controllers monitor and reconcile custom resources created through CRDs.

For example, if you have a custom resource that requires specific logic for scaling, a **Custom Controller** will be responsible for watching those resources and ensuring that they behave as expected. Controllers are responsible for managing the lifecycle of custom resources like Istio’s `VirtualService`.

#### Example:
- A custom controller for Istio’s `VirtualService` might ensure that traffic rules are applied and updated correctly.
- Custom controllers are typically written in Go and use Kubernetes client libraries to monitor and update resources.

#### Purpose:
- **Automate the management** of custom resources.
- **Maintain the desired state** of resources.
- **Extend Kubernetes' control plane** to manage non-native resources.

#### Command to deploy a Custom Controller (assuming it's packaged as a deployment):
```bash
kubectl apply -f custom-controller.yaml
```

#### Output:
```
deployment.apps/custom-controller created
```

### **Scenarios & Purpose of Use:**
1. **Scenario 1: Extending Kubernetes with Custom Resources**
   - **Purpose**: Let’s say your organization uses Istio for service mesh capabilities. Istio needs its own custom resources, such as `VirtualService`, to define how traffic should flow between services. These resources are not native to Kubernetes. Hence, by introducing CRDs, Istio extends the Kubernetes API to include `VirtualService` resources.
   - **Command**:
```bash
     kubectl apply -f istio-crd.yaml
```
   - **Output**:
```
     customresourcedefinition.apiextensions.k8s.io/virtualservices.networking.istio.io created
```

2. **Scenario 2: Automating Management with Custom Controllers**
   - **Purpose**: Custom controllers, such as the ones used by Istio or ArgoCD, ensure that the custom resources behave as expected. For instance, an ArgoCD controller ensures that GitOps workflows are correctly applied to Kubernetes resources.
   - **Command**:
```bash
     kubectl apply -f argocd-controller.yaml
```
   - **Output**:
```
     deployment.apps/argocd-controller created
```

### **Summary of Concepts**:
1. **CRD (Custom Resource Definition)**: Defines a new API and structure for a custom resource in Kubernetes.
2. **CR (Custom Resource)**: An instance of a custom resource, built based on a CRD, to extend the capabilities of Kubernetes.
3. **Custom Controller**: A Kubernetes controller responsible for managing and reconciling the state of custom resources.

### **Key Commands Recap**:
- **CRD Deployment**:
```bash
  kubectl apply -f crd.yaml
```
- **Custom Resource Deployment**:
```bash
  kubectl apply -f custom-resource.yaml
```
- **Custom Controller Deployment**:
```bash
  kubectl apply -f custom-controller.yaml
```

This covers the core concepts of CRD, CR, and Custom Controllers in Kubernetes, making it clear how they extend Kubernetes functionality and are applied in real-world scenarios.