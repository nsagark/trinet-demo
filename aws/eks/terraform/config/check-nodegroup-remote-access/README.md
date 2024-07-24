# Check Nodegroup Remote Access

As a general security measure, it's crucial to ensure that your AWS EKS node group does not have implicit SSH access from 0.0.0.0/0, thus not being accessible over the internet. This protects your EKS node group from unauthorized access by external entities.

## Policy Details:

- **Policy Name:** check-nodegroup-remote-access
- **Check Description:** Ensure AWS EKS node group does not have implicit SSH access from 0.0.0.0/0
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
    nctl scan terraform --resources test/good-01.tf --policies check-nodegroup-remote-access.yaml 
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
    +-------------------------------+-------------------------------+-----------------+---------+--------+
    |            POLICY             |             RULE              |    RESOURCE     | MESSAGE | RESULT |
    +-------------------------------+-------------------------------+-----------------+---------+--------+
    | check-nodegroup-remote-access | check-nodegroup-remote-access | test/good-01.tf |         | pass   |
    +-------------------------------+-------------------------------+-----------------+---------+--------+

    Done
    ```

    b. **Test Against Invalid Terraform Config File:**
    ```
    nctl scan terraform --resources test/bad-01.tf --policies check-nodegroup-remote-access.yaml
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
    +-------------------------------+-------------------------------+----------------+------------------------------------------------------------------------------+--------+
    |            POLICY             |             RULE              |    RESOURCE    |                                   MESSAGE                                    | RESULT |
    +-------------------------------+-------------------------------+----------------+------------------------------------------------------------------------------+--------+
    | check-nodegroup-remote-access | check-nodegroup-remote-access | test/bad-01.tf | AWS EKS node group should not have implicit SSH access from 0.0.0.0/0:       | fail   |
    |                               |                               |                | all[0].check.~.(resource.aws_eks_node_group.values(@)[])[0].~.(remote_access |        |
    |                               |                               |                | || `[]`)[0].(contains(keys(@), 'ec2_ssh_key') && !contains(keys(@),          |        |
    |                               |                               |                | 'source_security_group_ids')): Invalid value: true: Expected value: false    |        |
    +-------------------------------+-------------------------------+----------------+------------------------------------------------------------------------------+--------+

    Done
    ```

---