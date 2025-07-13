Of course. Here are the common ways to pass environment variables to containers using both Docker and `kubectl` commands.

### **Docker**

With Docker, you typically pass environment variables at runtime using the `docker run` command.

**1. Using the `-e` or `--env` flag**

This is the most direct method. You can use the flag multiple times for multiple variables.

  * **Syntax:** `docker run -e "VAR_NAME=VAR_VALUE" your-image-name`

  * **Example (Single Variable):**
    This command starts an NGINX container and sets an environment variable `API_URL` inside it.

    ```bash
    docker run -d -p 8080:80 -e "API_URL=https://api.example.com/v1" nginx
    ```

  * **Example (Multiple Variables):**

    ```bash
    docker run -d -p 8080:80 \
      -e "API_URL=https://api.example.com/v1" \
      -e "DB_USER=myuser" \
      nginx
    ```

  * **Passing a variable from your shell:**
    If you have a variable exported in your current shell, you can pass it into the container without specifying its value.

    ```bash
    export API_KEY=abcdef12345
    docker run -d -e API_KEY nginx
    ```

**2. Using an Environment File (`--env-file`)**

For a larger number of variables, it's cleaner to put them in a file.

  * **Create a file** (e.g., `app.env`):

    ```
    API_URL=https://api.example.com/v1
    DB_USER=myuser
    DB_PASS=supersecret
    ```

  * **Run the container using the file:**

    ```bash
    docker run -d --env-file ./app.env nginx
    ```

-----

### **Kubernetes (kubectl)**

In Kubernetes, while configuration is most often defined declaratively in YAML files, you can manage environment variables imperatively with `kubectl` commands, usually by updating an existing resource like a Deployment.

**1. Setting Variables on an Existing Deployment (`kubectl set env`)**

This is the most direct imperative command. You first create the deployment and then set the environment variables on it.

  * **Step 1: Create a deployment (if it doesn't exist):**

    ```bash
    kubectl create deployment my-nginx --image=nginx
    ```

  * **Step 2: Set environment variables:**

      * **Syntax:** `kubectl set env deployment/your-deployment-name VAR_1=VALUE_1 VAR_2=VALUE_2`

      * **Example (Single and Multiple Variables):**
        This command updates the `my-nginx` deployment to include two environment variables. Kubernetes will trigger a rolling update to the pods with the new configuration.

        ```bash
        kubectl set env deployment/my-nginx \
          API_URL=https://api.example.com/v1 \
          WORKER_THREADS=4
        ```

  * **To view the variables on a deployment:**

    ```bash
    kubectl set env deployment/my-nginx --list
    ```

  * **To remove a variable:**
    Append a minus sign (`-`) to the variable name.

    ```bash
    kubectl set env deployment/my-nginx WORKER_THREADS-
    ```

---


**2. Defining Variables in YAML (Declarative Approach)**

This is the most common and recommended best practice for production environments as it allows your configuration to be version-controlled.

  * **Example `deployment.yaml`:**
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: my-nginx
    spec:
      replicas: 1
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
            image: nginx
            ports:
            - containerPort: 80
            env:
            - name: API_URL
              value: "https://api.example.com/v1"
            - name: WORKER_THREADS
              value: "4"
    ```
    You would then apply this file with `kubectl apply -f deployment.yaml`.

**3. Using `ConfigMap` and `Secret` (Advanced/Best Practice)**

For managing configuration and sensitive data, you should use `ConfigMap` and `Secret` objects. You can then inject all key-value pairs from these objects as environment variables.

  * **Example using `envFrom` with a ConfigMap:**
    ```yaml
    # In your deployment.yaml under the container spec
    spec:
      containers:
        - name: nginx
          image: nginx
          envFrom:
          - configMapRef:
              name: my-app-config # Name of your ConfigMap
    ```

---