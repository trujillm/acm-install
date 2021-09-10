# Installing Red Hat Advanced Cluster Management

## Assumptions: 
* You have access to a AWS account and are using IPI to install
* You have a generated public ssh key
* You have already downloaded the IPI installation program [here](https://console.redhat.com/openshift/install) needed for the OCP install
* You have already created a pull secret [here](https://console.redhat.com/openshift/install/pull-secret)
* You have already created a service registry account [here](https://access.redhat.com/terms-based-registry/)
* You have created project open-cluster-management


### Required steps:
1. Configure aws cli for account
    * We need to run the aws command to configure the aws account we want to use on the command line. If you are not familiar with this please see [here](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.html)
    * Once you have the profile configured for the correct account you want to deploy you can run the following command:
    
        `export AWS_PROFILE=default`
2. Install the Hub and managed OCP clusters

    * Modify the install.yaml files located int acm-test-cluster-hub and acm-test-cluster-managed with you pull secret and ssh public key
    * Create the hub cluster by running the following command
    
      * `./openshift-install create cluster --dir=acm-test-cluster-hub --log-level=debug `
    * Create the managed cluster by running the following command

        * `./openshift-install create cluster --dir=acm-test-cluster-hub --log-level=debug`
        * Once the manage cluster is up add label cluster = marcus-test-cluster-managed

3. Create secret needed for for registry. You should have pulled the yaml file when you created the service registry account. See assumptions if this is unclear
      
    * Log into the hub cluster on the command line

    * Run the following command to create the secret from the yaml file

      * `oc create -f acm-test01-secret.yaml --namespace=open-cluster-management`

4. Apply ACM operator group via yaml file

    * `oc apply -f acm-operator.yaml`

5. Apply ACM subscription via yaml file

    * `oc apply -f acm-subscription.yaml`

6. Create multiClusterHub via yaml file 

   * `oc apply -f ../Training\ Material/ACM/multicluster-acm.yaml`

7. Add managed cluster to Hub cluster

    * Log into ACM
      * You can do this by getting the route in the OCP cluster that is used for the hub

    * Once you are logged in
      * Select the top right drop down
      * Select Automate infrastructure
      * Select Clusters
      * Select Add cluster in drop down
      * Select Import an existing cluster

   * Generate Command
     * Configuration
       * Cluster name: acm-test-cluster-managed
     * Labels
       * Cloud: AWS
       * Environment: dev
       * additional lables: cluster = marcus-test-cluster-managed

8. Confirm you see new cluster in Hub
