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

Ensure you have a Google Cloud account and sign in to the [Google Cloud Console](https://console.cloud.google.com/).

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

8. Scroll down and click on the **``Create``** button to create the cluster.

### Step 1.3: Wait for the Cluster to Provision

The cluster creation process may take a few minutes. During this time, GCP will set up the necessary infrastructure and configure the Kubernetes components.
  
  <img src=https://sada.com/wp-content/plugins/bbpowerpack/assets/images/spinner.gif height=10% width=10%>

### Step 1.4: Verify the Cluster Status

1. Once the cluster is provisioned, you'll be redirected to the ``Kubernetes Engine > Clusters`` page.

2. In the list of clusters, you should see your newly created cluster with the name you provided.

3. The cluster status will initially show as ``Provisioning``, and it will change to ``Running`` once it's ready.

### Step 1.5: Connect to the Cluster

1. Click on the **"Connect"** button next to your cluster's name.

2. In the new window that opens, click on the **"Run in Cloud Shell"** button. This will open a Google Cloud Shell session, and it will automatically configure the `kubectl` command-line tool to interact with your cluster.

   **Note**: Google Cloud Shell will automatically type the necessary command to connect to your Kubernetes cluster. Simply press **Enter** to execute the command.
   <br></br>
   <img src=https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/c8b55f65-8a56-4727-8a65-ce569af5a74f height=90% width=90%>

4. Click on **"Authorize Cloud Shell"** if prompted. This will grant the necessary permissions to manage resources in your GCP project.

5. After a few moments, you are now connected to your Kubernetes cluster.

   **Tip**: To verify that you are connected to the correct cluster, you can use the following command in the Cloud Shell:
   ```
   kubectl config current-context
   ```
   This will display the name of the currently selected Kubernetes cluster context. Ensure it matches the name of the cluster you want to work with.

<br></br>
## Step 2: Create PersistentVolumeClaims and PersistentVolumes

MySQL and WordPress each require a PersistentVolume to store data. The PersistentVolumeClaims (PVCs) will be created during the deployment step.

In many cluster environments, there is a default StorageClass installed. If a StorageClass is not specified in the PersistentVolumeClaim, the cluster's default StorageClass is used instead.

When a PersistentVolumeClaim is created, a PersistentVolume (PV) is dynamically provisioned based on the StorageClass configuration.

**Warning**: In local clusters, the default StorageClass often uses the hostPath provisioner, which is suitable only for development and testing purposes. With hostPath volumes, data lives in `/tmp` on the node where the Pod is scheduled and does not move between nodes. If a Pod dies and gets scheduled to another node, or the node is rebooted, the data will be lost.

**Note**: If you are using a cluster that needs to use the hostPath provisioner, ensure that the `--enable-hostpath-provisioner` flag is set in the controller-manager component.

**Note**: For Google Kubernetes Engine (GKE) clusters, please refer to the official guide for more details.

### Create a `kustomization.yaml`

We will use a `kustomization.yaml` file to manage the creation of a Secret that stores sensitive data like passwords or keys.

1. Create a file named `kustomization.yaml` with the following content:

```yaml
secretGenerator:
- name: mysql-pass
  literals:
  - password=YOUR_PASSWORD
