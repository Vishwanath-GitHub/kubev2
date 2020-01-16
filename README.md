# Kubebuilder-Multi-Version-API
> This repository contains code for the Multi-Version Cronjob (v1 & v2) implemented by Kubebuilder. It doesn't contain webhook implementation.

This **Kubebuilder Multi-Version API Cronjob Implementation** is the direct implementation from the [Kubebuilder Multi-Version Tutorial](https://book.kubebuilder.io/multiversion-tutorial/tutorial.html) featuring the same file struture. This Operator implementation is done for Cronjob in a way that when we deploy the Cronjobs from their .yaml files, the Operator/Controller gets the information of the Cronjobs in a struct{} and which contains the Cronjob Schedule, it's starting deadline seconds, job history and concurrency policy. The `Reconcile()` function of the Controller\Operator sets the Clock in which it declares and defines how to deploy the Cronjobs and get information about them.

Changes made after the Single-Version (v1) Kubebuilder Cronjob Implementation:
* New API is created by Kubebuilder which further applies the Multi-Version concept. It means that now our Operator/Controller will monitor and manage Cronjobs in different versions with different configurations.
* The concept of Hubs & Spokes is implemented. This means that we are defining one version as the main version (Hub) of Cronjobs and if any of different version (Spokes) Cronjobs are deployed, then they will have to pass through this one main version and then convert to the new version.
* For the purpose of conversion between APIs, "cronjob_conversion.go" files are added in all versions of Cronjob. The main version defines that it is just the Hub. Other versions have the methods `ConvertTo()` and `ConvertFrom()` to convert to and from other or main version.
* In the new version of Cronjob, we are adding new fields that could be added under `schedule:` field of Cronjob. To do this, the "types.go" of this new version API is updated. The `struct{}` now contains the individual new fields that could be described under `schedule:` field. These are actually the same values that `schedule:` field get by default in a Cronjob.
* The first version of Cronjob now contains a new marker `// +kubebuilder:storageversion` telling Kubernetes that this is now the main version and Kubernetes API server must use this to store our data.

After this, the entire Operator/Controller with its configuration is deployed using `make install` and `make run` locally (outside the cluster). To deploy it inside the cluster, use make `docker-build` and `make deploy`. After deploying the contoller, it starts watching the events from the workloads and shows them by logs of the Operator/Controller pod. Now, when the .yaml files of Cronjobs are applied, the logs start updating with the status about and from the Cronjobs and now this Operator\Controller will manage and monitor these Cronjobs.

However, Webhooks are NOT implemented in this Operator/Controller configuration due to the error "Undefined v1beta1.Webhook" delivered by "parser.go".
