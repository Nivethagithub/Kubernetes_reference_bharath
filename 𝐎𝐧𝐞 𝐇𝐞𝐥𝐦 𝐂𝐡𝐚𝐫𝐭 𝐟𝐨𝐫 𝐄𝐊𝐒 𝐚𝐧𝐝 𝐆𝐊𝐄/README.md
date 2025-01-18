In today’s cloud-native world, managing applications across multiple cloud providers is common. In this blog, will explore how to use one Helm chart to deploy an e-commerce application on both AWS EKS and Google GKE.

𝐇𝐢𝐠𝐡𝐥𝐢𝐠𝐡𝐭𝐬:

1. Real-world e-commerce app architecture.

2. Detailed Helm chart structure with YAML examples.

3. Steps to deploy to EKS and GKE.

To use one Helm chart for both EKS (Amazon Elastic Kubernetes Service) and GKE (Google Kubernetes Engine), we need to create a flexible Helm chart that supports configuration overrides for each environment. Let’s break this down using a real-world e-commerce application example.

𝐎𝐯𝐞𝐫𝐯𝐢𝐞𝐰

An e-commerce application might consist of:

1. 𝐅𝐫𝐨𝐧𝐭𝐞𝐧𝐝 𝐒𝐞𝐫𝐯𝐢𝐜𝐞: User-facing UI.

2. 𝐁𝐚𝐜𝐤𝐞𝐧𝐝 𝐒𝐞𝐫𝐯𝐢𝐜𝐞: APIs for order management, inventory, etc.

3. 𝐃𝐚𝐭𝐚𝐛𝐚𝐬𝐞: Persistent storage (e.g., PostgreSQL).

The 𝐇𝐞𝐥𝐦 𝐜𝐡𝐚𝐫𝐭 will define resources for:

Deployments (frontend and backend)

Services

Ingress (for external access)

Persistent Volume Claims (PVCs)

We will use:

values.yaml for common configurations.

values-eks.yaml and values-gke.yaml for environment-specific overrides.

𝐇𝐞𝐥𝐦 𝐂𝐡𝐚𝐫𝐭 𝐒𝐭𝐫𝐮𝐜𝐭𝐮𝐫𝐞

![image alt] (https://github.com/bharathreddy2103/Kubernetes/blob/5c9c12d5118e836989a40b64042b2143fb0d9edd/%F0%9D%90%8E%F0%9D%90%A7%F0%9D%90%9E%20%F0%9D%90%87%F0%9D%90%9E%F0%9D%90%A5%F0%9D%90%A6%20%F0%9D%90%82%F0%9D%90%A1%F0%9D%90%9A%F0%9D%90%AB%F0%9D%90%AD%20%F0%9D%90%9F%F0%9D%90%A8%F0%9D%90%AB%20%F0%9D%90%84%F0%9D%90%8A%F0%9D%90%92%20%F0%9D%90%9A%F0%9D%90%A7%F0%9D%90%9D%20%F0%9D%90%86%F0%9D%90%8A%F0%9D%90%84/%F0%9D%90%87%F0%9D%90%9E%F0%9D%90%A5%F0%9D%90%A6%20%F0%9D%90%82%F0%9D%90%A1%F0%9D%90%9A%F0%9D%90%AB%F0%9D%90%AD%20%F0%9D%90%92%F0%9D%90%AD%F0%9D%90%AB%F0%9D%90%AE%F0%9D%90%9C%F0%9D%90%AD%F0%9D%90%AE%F0%9D%90%AB%F0%9D%90%9E.PNG)

𝟏. 𝐂𝐡𝐚𝐫𝐭.𝐲𝐚𝐦𝐥

Defines the metadata of the Helm chart.


𝟐. 𝐯𝐚𝐥𝐮𝐞𝐬.𝐲𝐚𝐦𝐥 (𝐂𝐨𝐦𝐦𝐨𝐧 𝐂𝐨𝐧𝐟𝐢𝐠𝐮𝐫𝐚𝐭𝐢𝐨𝐧)

Used for shared values across environments.


𝟑. 𝐯𝐚𝐥𝐮𝐞𝐬-𝐞𝐤𝐬.𝐲𝐚𝐦𝐥 (𝐄𝐊𝐒-𝐒𝐩𝐞𝐜𝐢𝐟𝐢𝐜 𝐎𝐯𝐞𝐫𝐫𝐢𝐝𝐞𝐬)

Contains configurations for deploying on Amazon EKS, including replicaCount: 3, ingress.host as ecommerce.eks.example.com, and storageClass: gp2.


𝟒. 𝐯𝐚𝐥𝐮𝐞𝐬-𝐠𝐤𝐞.𝐲𝐚𝐦𝐥 (𝐆𝐊𝐄-𝐒𝐩𝐞𝐜𝐢𝐟𝐢𝐜 𝐎𝐯𝐞𝐫𝐫𝐢𝐝𝐞𝐬)

Contains configurations for deploying on Google GKE, including replicaCount: 2, ingress.host as ecommerce.gke.example.com, and storageClass: standard.


𝐓𝐞𝐦𝐩𝐥𝐚𝐭𝐞𝐬

𝟓. _𝐡𝐞𝐥𝐩𝐞𝐫𝐬.𝐭𝐩𝐥

Defines reusable labels.


𝟔. 𝐝𝐞𝐩𝐥𝐨𝐲𝐦𝐞𝐧𝐭-𝐛𝐚𝐜𝐤𝐞𝐧𝐝.𝐲𝐚𝐦𝐥

Defines the backend deployment.


𝟕. 𝐝𝐞𝐩𝐥𝐨𝐲𝐦𝐞𝐧𝐭-𝐟𝐫𝐨𝐧𝐭𝐞𝐧𝐝.𝐲𝐚𝐦𝐥

Defines the frontend deployment.


𝟖. 𝐬𝐞𝐫𝐯𝐢𝐜𝐞.𝐲𝐚𝐦𝐥

Defines services for both frontend and backend.


𝟗. 𝐢𝐧𝐠𝐫𝐞𝐬𝐬.𝐲𝐚𝐦𝐥

Defines ingress for external access.


𝟏𝟎. 𝐩𝐯𝐜.𝐲𝐚𝐦𝐥

Defines persistent storage.


𝐒𝐭𝐞𝐩𝐬 𝐭𝐨 𝐃𝐞𝐩𝐥𝐨𝐲

𝟏. 𝐃𝐞𝐩𝐥𝐨𝐲 𝐭𝐨 𝐄𝐊𝐒


𝟐. 𝐃𝐞𝐩𝐥𝐨𝐲 𝐭𝐨 𝐆𝐊𝐄


𝐂𝐨𝐧𝐜𝐥𝐮𝐬𝐢𝐨𝐧

Using a single Helm chart to deploy applications on both EKS and GKE simplifies multi-cloud operations by providing a unified framework for managing Kubernetes resources. By leveraging environment-specific values files (values-eks.yaml and values-gke.yaml), we can easily tailor deployments to match the unique requirements of each platform while maintaining consistency in the overall configuration. This approach not only reduces complexity but also improves scalability and operational efficiency.

Multi-cloud deployments are becoming the norm, and mastering such techniques is crucial for modern DevOps Engineers. Start building your Helm charts today and unlock seamless multi-cloud application management.

Share your feedback and let me know how this helps you streamline your multi-cloud deployments.
