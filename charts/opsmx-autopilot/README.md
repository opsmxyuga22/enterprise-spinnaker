# OpsMx Autopilot

Autopilot is a release verification platform that provides Dev/Ops engineers an intelligent automated real-time actionable risk assessment of a new release deployed.

For more information, visit https://www.opsmx.com

## TL;DR;
Install OpsMx Autopilot
  ```console
  $ helm repo add opsmx https://helmcharts.opsmx.com/
  $ helm install <release-name> opsmx/opsmx-autopilot
  ```
## Autopilot Setup Instructions

### Prerequisites

	- Kubernetes cluster 1.13 or later with at least 6 cores and 20 GB memory
	- Helm is setup and initialized in your Kubernetes cluster
	  ```console
	  $ helm version
	  ```
	  If helm is not setup, follow <https://helm.sh/docs/using_helm/#install-helm> to install helm.

	  If you are using helm v2.x, you need to initialize the Kubernetes to work with helm. If using helm v2.x, the helm version command should return both the client and server versions. If it does not return both client and server versions, you can follow these three simple steps to initialize helm v2.x in the kubernetes cluster:

	  ```console
	  $ kubectl create serviceaccount -n kube-system tiller
	  $ kubectl create clusterrolebinding tiller-binding --clusterrole=cluster-admin --serviceaccount kube-system:tiller
	  $ helm init --service-account tiller --wait
	  ```

### Installing the OpsMx Autopilot Chart

	- Add opsmx helm repo to your local machine

	   ```console
	   $ helm repo add opsmx https://helmcharts.opsmx.com/
	   ```

	- Docker registry credentials is setup as a secret in Kubernetes. Before you install Autopilot, please send an email to support@opsmx.com requesting access to the Autopilot images with your Dockerhub id. You can proceed with installation once your Dockerhub id has been granted access.

	  To be able to fetch Autopilot docker images, username and password shall be set in values.yaml or use --set imageCredentials.username=<username> --set imageCredentials.password=<password> while running helm install.

	- Your Kubernetes cluster supports persistent volumes and loadbalancer service type.

	- Helm v3 expects the namespace to be present before helm install command is run. If it does not exists,

	  ```console
	  $ kubectl create namespace mynamespace
	  ```

	- To install the chart with the release name `my-release`:

	  Helm v2.x
	  ```console
	  $ helm install --name my-release opsmx/opsmx-autopilot [--namespace mynamespace]
	  ```
		Helm v3.x
	  ```console
	  $ helm install my-release opsmx/opsmx-autopilot [--namespace mynamespace]
	  ```

	The command deploys Autopilot on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

	> **Tip**: List all releases using `helm list`

### Uninstalling the Chart

	To uninstall/delete the `my-release` deployment:

	Helm v2.x
	  ```console
	  $ helm delete my-release [--namespace mynamespace]
	  ```

	Helm v3.x
	  ```console
	  $ helm uninstall my-release [--namespace mynamespace]
	  ```

	The command removes all the Kubernetes components associated with the chart and deletes the release.

### Configuration

	The following table lists the configurable parameters of the Autopilot chart and their default values.

	Parameter | Description | Default
	--------- | ----------- | -------
	`imagePullSecret` | Name of the image pull secret to fetch autopilot docker images from private registry | `autopilot-repo`
	`imageCredentials.registry` | The registry where Autopilot docker images are available | `https://index.docker.io/v1/`
	`imageCredentials.username` | Username of docker account to access docker registry | `dockerHubId`
	`imageCredentials.password` | Password of docker account | `dockerHubPwd`
	`imageCredentials.email` | Email associated with docker account | `abc@xyz.com`
	`autopilot.image.registry` | Registry to be used for Autopilot docker images | `opsmxdev`
	`autopilot.image.repository` | Repository to be used for Autopilot docker images | `ubi8-autopilot`
	`autopilot.image.tag` | Tag to be used for Autopilot docker images | `v2.9.10-202006181240`
	`autopilot.image.pullPolicy` | Image pull policy for Autopilot image | `IfNotPresent`
	`autopilot.service.type` | Service type over which autopilot is to be exposed | `LoadBalancer`
	`autopilot.config.pythonAlgorithm` | Algorithm for Data Science | `spell`
	`autopilot.config.buildAnalysis.enabled` | Set it to false to disable build analysis | `false`
	`autopilot.config.dataSource.url` | URL of DB for Autopilot | `jdbc:postgresql://db-opsmx:5432/opsmx`
	`autopilot.config.dataSource.username` | Username to communicate with DB | `postgres`
	`autopilot.config.dataSource.username` | Password to communicate with DB | `networks123`
	`autopilot.config.spinnaker.loginAdminEnabled` | Set it to true if admin login is enabled in Spinnaker | `false`
	`autopilot.config.spinnaker.spinGateUrl` | Spinnaker Gate URL to trigger Canary Analysis | `http://spin-gate:8084`
	`autopilot.config.appConfigJS.autopilotServerUrl` | URL of Autopilot to be used by UI to reach the backend. app.config.js file in configuration appends http:// to the provided URL | `autopilot:8090`
	`autopilot.config.spinnaker.spinUsername` | Username to communicate with Spinnaker | `dummyusername`
	`autopilot.config.spinnaker.spinPassword` | Password to communicate with Spinnaker | `dummypassword`
	`autopilot.config.ldap.authEnabled` | Set it to true if LDAP is to be enabled for Autopilot | `true`
	`autopilot.config.ldap.url` | URL of LDAP server | `ldap://oes-openldap:389`
	`autopilot.config.ldap.userDnPattern` | DN Pattern for Open LDAP | `cn={0}`
	`autopilot.config.ldap.basedn` | Base DN value | `cn=Users,dc=local,dc=opsmx,dc=com`
	`autopilot.config.ldap.pattern` | Base DN filter pattern | `(&(objectclass=person)(cn=USERNAME))`
	`autopilot.config.ssl.enabled` | Set it to true to enable SSL | `false`
	`autopilot.config.ssl.keyStore` | SSL Key Store | `keystore.p12`
	`autopilot.config.ssl.keyStorePassword` | SSL Key Store Password | `dummypwd`
	`autopilot.config.ssl.keyStoreType` | SSL Key Store Type | `PKCS12`
	`autopilot.config.ssl.keyAlias` | SSL key alias | `tomcat`
	`opsmxdb.enabled` | Set it to false if OpsMx DB is already installed on cluster | `true`
	`opsmxdb.podManagementPolicy` | Rollout strategy for DB(statefulset) pods  | `OrderedReady`
	`opsmxdb.securityContext.fsGroup` | FSGroup that owns the DB pod's volumes | `1000`
	`opsmxdb.storageMountSize` | Storage to be allocated to OpsMx DB | `8Gi`


	> **Tip**: Refer to values.yaml for detailed comments


	Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

	  ```console
	  $ helm install my-release opsmx/opsmx-autopilot --set imageCredentials.username=<username> --set imageCredentials.password=<password> [--namespace mynamespace]
	  ```

	Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example, Autopilot values.yaml can be first downloaded using below command and then customized.

	wget https://raw.githubusercontent.com/opsmx/enterprise-spinnaker/oes3.0/charts/opsmx-autopilot/values.yaml

	Helm v2.x
	  ```console
	  $ helm install opsmx/opsmx-autopilot --name my-release -f values.yaml
	  ```
	Helm v3.x
	  ```console
	  $ helm install my-release opsmx/opsmx-autopilot -f values.yaml
	  ```

### Connecting to Autopilot

	  Once the service is up and running, find the service ip address

	  		kubectl get svc autopilot [--namespace mynamespace]

	  Example output would be:

	      NAME         TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)                                        AGE
	      autopilot    LoadBalancer   10.0.4.246   34.66.226.138   8090:32097/TCP,8161:32527/TCP,9090:31265/TCP   9m11s

	  Now, UI can be accessed via EXTERNAL-IP address, go to http://<EXTERNAL-IP>:8161/
