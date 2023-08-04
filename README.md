<p align=center>
  <img src=https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/dafde3b6-0e3e-499c-8df5-22cb786c8856 height=100% width=100%>
</p>

# Deploying WordPress and MySQL using Kubernetes

## Step 1: Create a Kubernetes Cluster on Google Cloud Platform (GCP)

In this step, we will create a Kubernetes cluster on Google Cloud Platform (GCP) with the following specifications:
- Cluster name: cluster-faraguti-1 (You can rename it to your preference)
- Machine type: e2-small
- Boot disk size: 20GB (Standard persistent disk)
- Default pool: 2 nodes

### Step 1.1: Sign in to Google Cloud Console

1. Ensure you have a Google Cloud account and sign in to the [Google Cloud Console](https://console.cloud.google.com/).

### Step 1.2: Create a new Kubernetes Cluster

1. In the Google Cloud Console, click on the ``Navigation menu`` (three horizontal lines) in the upper-left corner.

2. Navigate to ``Kubernetes Engine > Clusters``.

3. Click on the ``Create`` button and then ``SWITCH TO STANDARD CLUSTER``.
   <br></br>
   <img src=https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/0b8164a2-23c6-4f42-b699-dbe8cf701e3c height=90% width=90%>

5. In the ``Cluster basics`` section:
   - Enter your desired ``Cluster name`` (e.g., cluster-faraguti-1).
   - Select the ``Location type`` as ``Zonal``.
   - Choose your preferred zone.
   <br></br>
   <img src=https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/eed6cb10-b01d-4b33-8dcd-d0ffdde09cfe height=90% width=90%>

7. In the ``Node pools`` section:
   - Under ``default pool``, set the desired number of nodes to ``2``.
   - Under ``Nodes``, set ``Machine type`` to ``e2-small``.
   - Under ``Boot disk size``, set the size to ``20GB``.
   <br></br>
   <img src=https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/828d23a1-4ced-404a-93a3-cb114ff348c9 height=90% width=90%>

8. Scroll down and click on the ``Create`` button to create the cluster.

### Step 1.3: Wait for the Cluster to Provision

1. The cluster creation process may take a few minutes. During this time, GCP will set up the necessary infrastructure and configure the Kubernetes components.
  
  <img src=https://sada.com/wp-content/plugins/bbpowerpack/assets/images/spinner.gif height=5% width=5%>

### Step 1.4: Verify the Cluster Status

  - Once the cluster is provisioned, you'll be redirected to the ``Kubernetes Engine > Clusters`` page.
  - In the list of clusters, you should see your newly created cluster with the name you provided.
  - The cluster status will initially show as ``Provisioning``, and it will change to ``Running`` once it's ready.

### Step 1.5: Connect to the Cluster

  1. Click on the ``Connect`` button next to your cluster's name.
     - In the new window that opens, click on the ``Run in Cloud Shell`` button. This will open a Google Cloud Shell session, and it will automatically configure the `kubectl` command-line tool to interact with your cluster.
     ``Note``: Google Cloud Shell will automatically type the necessary command to connect to your Kubernetes cluster. Simply press ``Enter`` to execute the command.
    
     <br></br>
     <img src=https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/c8b55f65-8a56-4727-8a65-ce569af5a74f height=90% width=90%>

4. Click on ``Authorize Cloud Shell`` if prompted. This will grant the necessary permissions to manage resources in your GCP project.

5. After a few moments, you are now connected to your Kubernetes cluster.

   ``Tip``: To verify that you are connected to the correct cluster, you can use the following command in the Cloud Shell:
   ```
   kubectl config current-context
   ```
   This will display the name of the currently selected Kubernetes cluster context. Ensure it matches the name of the cluster you want to work with.

<br></br>
## Step 2: Set up PersistentVolumeClaims and PersistentVolumes

Both MySQL and WordPress require a PersistentVolume to ensure data storage. The PersistentVolumeClaims (PVCs) will be automatically created during the deployment process.
In most cluster environments, there is a default StorageClass pre-configured. If a specific StorageClass is not specified in the PersistentVolumeClaim, the cluster's default StorageClass will be used instead. When a PersistentVolumeClaim is established, a PersistentVolume (PV) is dynamically provisioned based on the StorageClass settings.

### Step 2.1: Create a kustomization.yaml file on Cloud Shell:

We will use a ``kustomization.yaml`` file to manage the creation of a Secret that stores sensitive data like passwords or keys.

- In the Cloud Shell terminal, create the file named `kustomization.yaml` with the following content:

    ```
    cat <<EOF >./kustomization.yaml
    secretGenerator:
    - name: mysql-pass
      literals:
      - password=YOUR_PASSWORD
    EOF
    ```
   <br></br>
   <img src=https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/c765047c-0c39-4eef-9e06-e8480b611e33 height=90% width=90%>

<br></br>
## Step 3: Configure MySQL and WordPress Deployments

In this step, we will add the necessary resource configurations for both MySQL and WordPress deployments. These configurations are specified in YAML manifest files, and I will provide you with the links to download these files. Let's go through each deployment:

### Step 3.1 MySQL Deployment

  The first deployment we'll set up is for MySQL. The following manifest defines a single-instance MySQL Deployment. Inside the MySQL container, a PersistentVolume is mounted at `/var/lib/mysql` to ensure data persistence. Additionally, we set the `MYSQL_ROOT_PASSWORD` environment variable, which allows you to define the database password securely using a Secret.
  
  - Download the MySQL deployment configuration file.
    ```
    curl -LO https://k8s.io/examples/application/wordpress/mysql-deployment.yaml
    ```

###  Step 3.2: WordPress Deployment

  The second deployment is for WordPress. The manifest describes a single-instance WordPress Deployment. Within the WordPress container, the PersistentVolume is mounted at `/var/www/html`, ensuring that your website's data files are preserved even if the container is recreated. We also set the `WORDPRESS_DB_HOST` environment variable to point to the MySQL Service that we just deployed. WordPress will use this Service to access the MySQL database. The `WORDPRESS_DB_PASSWORD` environment variable is automatically sourced from the Secret generated by kustomize.
  
  - Download the WordPress configuration file.
    ```
    curl -LO https://k8s.io/examples/application/wordpress/wordpress-deployment.yaml
    ```

###  Step 3.3: Add them to kustomization.yaml file
  ```
  cat <<EOF >>./kustomization.yaml
  resources:
    - mysql-deployment.yaml
    - wordpress-deployment.yaml
  EOF
  ```
  <br></br>
  <img src=https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/f8c8e52d-fe5e-41bd-945b-145c3e4aa338 height=90% width=90%>


<br></br>
## Step 4: Apply and Verify

Now that we have all the necessary resource configurations for MySQL and WordPress deployments in our `kustomization.yaml`, we can proceed to apply and verify the setup.

- Apply the directory by running the following command:
   ```
   kubectl apply -k ./
   ```
- Verify that all objects exist:
   ```
   kubectl get secrets
   ```
- The response should include a Secret similar to this:
  <img src=https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/2a1ddd9a-94ea-43df-b960-95cae1ba369d height=90% width=90%>

- Verify that the PersistentVolumes got dynamically provisioned by running:
  ```
  kubectl get pvc
  ```
  > [!NOTE]  
  > It may take a few minutes for the PVs to be provisioned and bound.

- The response should include PVCs similar to this:
  <img src=https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/5dbb7f2f-c977-44bf-bb9c-bf18cc9a3264 height=90% width=90%>

- Check if the Pods are running by running:
  ```
  kubectl get pods
  ```
  
 > [!NOTE]  
 > It may take a few minutes for the Pod's Status to be RUNNING. The response should include Pods similar to this:





