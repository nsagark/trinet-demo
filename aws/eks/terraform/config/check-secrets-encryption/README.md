# Check Secrets Encryption in AWS EKS

Secrets encryption is essential to encrypt sensitive data, such as secrets and credentials stored within Kubernetes, adding an additional layer of protection against unauthorized access

You can read more about it [here](https://aws.amazon.com/about-aws/whats-new/2020/03/amazon-eks-adds-envelope-encryption-for-secrets-with-aws-kms/)

## Policy Details:

- **Policy Name:** check-secrets-encryption
- **Check Description:** Ensure Secrets Encryption in AWS EKS
- **Policy Category:** EKS Best Practices 

### Policy Validation Testing Instructions

To evaluate and test the policy, follow the steps outlined below:

For testing this policy you will need to:
- Make sure you have `nctl` installed on the machine 

1. **Test the Policy with nctl:**
    ```
   nctl scan terraform --resources tf-config.tf --policy policy.yaml
    ```

    a. **Test Policy Against Valid Terraform Config File:**
    ```
    nctl scan terraform --resources test/good.tf --policies check-secrets-encryption.yaml
    ```

    This produces the output:
    ```
    Version: 4.0.1
    Fetching policies...
    Loading policies...
    • found 1 policies
    Running analysis...
    • no errors
    Results...
    +--------------------------+--------------------------+--------------+---------+--------+
    |          POLICY          |           RULE           |   RESOURCE   | MESSAGE | RESULT |
    +--------------------------+--------------------------+--------------+---------+--------+
    | check-secrets-encryption | check-secrets-encryption | test/good.tf |         | pass   |
    +--------------------------+--------------------------+--------------+---------+--------+

    Done
    ```

    b. **Test Against Invalid Terraform Config Files:**
    ```
    nctl scan terraform --resources test/bad-01.tf --policies check-secrets-encryption.yaml 
    ```

    This produces the output:
    ```
    Version: 4.0.1
    Fetching policies...
    Loading policies...
    • found 1 policies
    Running analysis...
    • no errors
    Results...
    +--------------------------+--------------------------+----------------+-----------------------------------------------------------------------------+--------+
    |          POLICY          |           RULE           |    RESOURCE    |                                   MESSAGE                                   | RESULT |
    +--------------------------+--------------------------+----------------+-----------------------------------------------------------------------------+--------+
    | check-secrets-encryption | check-secrets-encryption | test/bad-01.tf | Encryption config must be present for all clusters:                         | fail   |
    |                          |                          |                | all[0].check.~.(resource.aws_eks_cluster.values(@)[])[0].(contains(keys(@), |        |
    |                          |                          |                | 'encryption_config')): Invalid value: false: Expected value: true           |        |
    +--------------------------+--------------------------+----------------+-----------------------------------------------------------------------------+--------+

    Done
    ```

    ```
    nctl scan terraform --resources test/bad-02.tf --policies check-secrets-encryption.yaml 
    ```

    This produces the output:
    ```
    Version: 4.0.1
    Fetching policies...
    Loading policies...
    • found 1 policies
    Running analysis...
    • no errors
    Results...
    +--------------------------+--------------------------+----------------+---------------------------------------------------------------------------------------------------+--------+
    |          POLICY          |           RULE           |    RESOURCE    |                                              MESSAGE                                              | RESULT |
    +--------------------------+--------------------------+----------------+---------------------------------------------------------------------------------------------------+--------+
    | check-secrets-encryption | check-secrets-encryption | test/bad-02.tf | Secrets must be encrypted to avoid unauthorized access:                                           | fail   |
    |                          |                          |                | all[1].check.~.(resource.aws_eks_cluster.values(@)[].encryption_config[])[0].(contains(resources, |        |
    |                          |                          |                | 'secrets')): Invalid value: false: Expected value: true                                           |        |
    +--------------------------+--------------------------+----------------+---------------------------------------------------------------------------------------------------+--------+

    Done
    ```

---