<p align="center">
  <img src="https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/dafde3b6-0e3e-499c-8df5-22cb786c8856" height="100%" width="100%">
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

1. In the Google Cloud Console, click on the **Navigation menu** (three horizontal lines) in the upper-left corner.

2. Navigate to **Kubernetes Engine > Clusters**.

3. Click on the **"Create"** button and then **SWITCH TO STANDARD CLUSTER**.
<br/>
<img src="https://github.com/faraguti/GCP-K8s-WordPress-MySQL/assets/5418256/0b8164a2-23c6-4f42-b699-dbe8cf701e3c" height="80%" width="80%">



5. In the **"Cluster basics"** section:
   - Enter your desired **Cluster name** (e.g., cluster-faraguti-1).
   - Select the **"Location type"** as **"Zonal"**.

6. In the **"Master version"** section, select the latest stable Kubernetes version.

7. In the **"Node pools"** section, click on **"default-pool"**:
   - Under **"Nodes"**, set the desired number of nodes to **2**.
   - Under **"Machine type"**, select **"e2-small"**.
   - Under **"Boot disk size"**, set the size to **20GB**.

8. Scroll down and click on the **"Create"** button to create the cluster.

### Step 1.3: Wait for the Cluster to Provision

The cluster creation process may take a few minutes. During this time, GCP will set up the necessary infrastructure and configure the Kubernetes components.

### Step 1.4: Verify the Cluster Status

1. Once the cluster is provisioned, you'll be redirected to the **Kubernetes Engine > Clusters** page.

2. In the list of clusters, you should see your newly created cluster with the name you provided.

3. The cluster status will initially show as **"Provisioning"**, and it will change to **"Running"** once it's ready.

### Step 1.5: Connect to the Cluster

1. Click on the **"Connect"** button next to your cluster's name.

2. In the new window that opens, click on the **"Run in Cloud Shell"** button. This will open a Google Cloud Shell session, and it will automatically configure the `kubectl` command-line tool to interact with your cluster.

3. You are now connected to your Kubernetes cluster.

### Step 1.6: Explore and Use the Cluster

Congratulations! You have successfully created a Kubernetes cluster on Google Cloud Platform. You can now proceed to the next steps to deploy WordPress and MySQL to the cluster, ensuring data persistence through Persistent Volumes and Persistent Volume Claims.

Before deploying your applications, you can further customize the cluster configuration, add node pools, set up networking, and more based on your specific requirements.

Please note that running a default pool with only 2 nodes might be suitable for small-scale deployments or testing purposes. For production scenarios, consider scaling up the cluster to handle higher traffic loads and provide better high-availability.

For more information on managing Kubernetes clusters on GCP, consult the official [Google Kubernetes Engine documentation](https://cloud.google.com/kubernetes-engine/docs).

Happy deploying!
