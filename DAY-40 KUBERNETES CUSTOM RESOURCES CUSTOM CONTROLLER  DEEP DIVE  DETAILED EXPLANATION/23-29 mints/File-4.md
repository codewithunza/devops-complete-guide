### Detailed Explanation of Custom Resources, Custom Resource Definitions (CRDs), and Custom Controllers in Kubernetes

#### Step-by-Step Breakdown:

1. **Deploying the Custom Resource Definition (CRD):**
   - **Purpose**: The first step in extending Kubernetes functionality involves deploying a **Custom Resource Definition (CRD)**. This is needed when you want to define a new type of resource that is not available natively in Kubernetes. CRDs extend the Kubernetes API by allowing the creation of custom resource types.
   - **Example**: If you are working with Istio or Argo CD, these tools introduce new resource types that Kubernetes doesn't provide out of the box, such as Virtual Services (for Istio). You need a CRD to define this custom resource.
   - **Command**: To deploy a CRD in Kubernetes, you might typically use:
```bash
     kubectl apply -f crd.yaml
```
     Here, `crd.yaml` is a YAML file defining the structure of the custom resource (like Istio’s Virtual Service).

2. **Deploying the Custom Controller:**
   - **Purpose**: After defining the custom resource, a **custom controller** must be deployed. This controller watches over the custom resource and manages its lifecycle, ensuring the custom resource behaves as expected.
   - **Example**: For Istio, a controller would be responsible for managing Virtual Service resources. If you deploy a Virtual Service, the controller will handle its validation and implementation.
   - **Command**: Deploying a custom controller often involves Helm charts, operators, or manifests. For example, using Helm:
```bash
     helm install istio-controller ./istio-controller-chart
```

3. **Deploying the Custom Resource:**
   - **Purpose**: Users or developers who wish to use the new custom resource type can now create instances of the custom resource in their namespaces. This is where they configure the resource’s behavior.
   - **Example**: A user in the "Abhishek" namespace might create an Istio **Virtual Service** after the CRD and controller are deployed.
   - **Command**: The user can create the custom resource by running:
```bash
     kubectl apply -f virtual-service.yaml
```

#### Comparing Native Resources with Custom Resources

- **Native Kubernetes Resources** like Deployments, Services, etc., are built into Kubernetes. When you create a Deployment, Kubernetes validates it against the built-in resource definitions and uses native controllers (e.g., Deployment controller) to manage Pods.
- **Custom Resources** are similar in concept. However, instead of being native to Kubernetes, these resources are defined through CRDs, validated against them, and managed by custom controllers.

#### Writing a Custom Kubernetes Controller

- **Popular Language**: The most common way to write custom controllers is using **Go** (Golang). This is because Kubernetes itself is written in Go, and the **client-go** library is the primary API for interacting with the Kubernetes API.
- **Client-go**: When writing a custom controller, the `client-go` package is used to communicate with the Kubernetes API. This allows your controller to monitor changes and perform actions in response.
- **Alternatives**: Though Go is the preferred language, custom controllers can also be written in Python or Java, using respective client libraries.

   - **Setting Up Watchers**: Watchers in Kubernetes observe events like **create**, **update**, or **delete** on resources. For native resources like Deployments or Services, Kubernetes already has built-in watchers. In the case of custom controllers, you need to create your own watchers to monitor the custom resources.
     - **Example**: A custom controller might watch for the creation of Virtual Services, and when detected, process them in the controller logic.

- **Frameworks**: 
   - **Kubernetes Controller Runtime**: A framework in Go that simplifies the creation of custom controllers. You don't need to build everything from scratch—this package offers utilities to set up watchers and reconcile loops.
   - **Reflector and Worker Queues**: When a resource is modified, the controller uses a **reflector** to monitor these changes, then places the events into a **work queue**. The controller processes each event sequentially to make the necessary updates in Kubernetes.

#### Command Output and Scenarios

Here’s an example output for a command deploying a CRD:

```bash
kubectl apply -f virtual-service-crd.yaml
customresourcedefinition.apiextensions.k8s.io/virtualservices.networking.istio.io created
```

For deploying the custom controller, output might look like this:

```bash
helm install istio-controller ./istio-controller-chart
NAME: istio-controller
LAST DEPLOYED: Mon Aug 30 12:32:44 2024
NAMESPACE: default
STATUS: deployed
REVISION: 1
```

And for deploying the custom resource:

```bash
kubectl apply -f virtual-service.yaml
virtualservice.networking.istio.io/vs-abhishek created
```

#### Key Scenarios and Purpose:

- **Extending Kubernetes**: CRDs allow you to extend Kubernetes’ native resources. Tools like Istio, Argo CD, or custom tools you develop often need custom resources and controllers.
- **Managing Specific Namespaces**: Not all namespaces might require the custom resource’s capabilities. Only those where users want to apply features like Virtual Services will deploy the custom resource.
- **Automation via Controllers**: A custom controller automates how Kubernetes handles the lifecycle of your custom resources. Just as Kubernetes manages Deployments with a built-in controller, custom controllers manage resources like Virtual Services, adding automation and enforcement of policies.

#### Summary of Key Points:

1. **Custom Resource Definitions (CRDs)** allow for the extension of Kubernetes by defining new resource types.
2. **Custom Controllers** are necessary to automate the handling of custom resources, ensuring they are properly managed.
3. **Writing Custom Controllers** is commonly done in Go using the **client-go** library, which interacts with the Kubernetes API.
4. **Watchers and Workers** in the controller architecture monitor and process changes in resources.
5. **Deploying CRDs, Controllers, and Custom Resources** involves standard Kubernetes commands, with the deployment process tailored to the specific tool or resource being managed.