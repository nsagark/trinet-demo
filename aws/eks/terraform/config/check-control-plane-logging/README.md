# Check Control Plane Logging for Amazon EKS

Enabling Amazon EKS control plane logging for all log types is a best practice for enhancing the security, monitoring, troubleshooting, performance optimization, and operational management of your Kubernetes clusters. By capturing comprehensive logs of control plane activities, you can effectively manage and secure your EKS infrastructure while ensuring compliance with regulatory requirements and industry standards.

To enable control plane logging for all types in Amazon EKS, ensure that **enabled_cluster_log_types** includes all these types: "api", "audit", "authenticator", "controllerManager" and "scheduler". You can read more about the log types [here](https://docs.aws.amazon.com/eks/latest/userguide/control-plane-logs.html)

## Policy Details:

- **Policy Name:** check-control-plane-logging
- **Check Description:** Ensure Amazon EKS control plane logging is enabled for all log types
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
    nctl scan terraform --resources test/good.tf --policies check-control-plane-logging.yaml 
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
    +-----------------------------+-----------------------------+--------------+---------+--------+
    |           POLICY            |            RULE             |   RESOURCE   | MESSAGE | RESULT |
    +-----------------------------+-----------------------------+--------------+---------+--------+
    | check-control-plane-logging | check-control-plane-logging | test/good.tf |         | pass   |
    +-----------------------------+-----------------------------+--------------+---------+--------+
    
    Done
    ```

    b. **Test Against Invalid Terraform Config File:**
    ```
    nctl scan terraform --resources test/bad-01.tf --policies check-control-plane-logging.yaml 
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
    +-----------------------------+-----------------------------+----------------+--------------------------------------------------------------------------------------+--------+
    |           POLICY            |            RULE             |    RESOURCE    |                                       MESSAGE                                        | RESULT |
    +-----------------------------+-----------------------------+----------------+--------------------------------------------------------------------------------------+--------+
    | check-control-plane-logging | check-control-plane-logging | test/bad-01.tf | EKS control plane logging must be enabled for all log types:                         | fail   |
    |                             |                             |                | [all[0].check.~.(resource.aws_eks_cluster.values(@)[])[0].(enabled_cluster_log_types |        |
    |                             |                             |                | || `[]`).(contains(@, 'audit')): Invalid value: false: Expected value: true,         |        |
    |                             |                             |                | all[0].check.~.(resource.aws_eks_cluster.values(@)[])[0].(enabled_cluster_log_types  |        |
    |                             |                             |                | || `[]`).(contains(@, 'authenticator')): Invalid value: false: Expected value: true, |        |
    |                             |                             |                | all[0].check.~.(resource.aws_eks_cluster.values(@)[])[0].(enabled_cluster_log_types  |        |
    |                             |                             |                | || `[]`).(contains(@, 'controllerManager')):                                         |        |
    |                             |                             |                | Invalid value: false: Expected value: true,                                          |        |
    |                             |                             |                | all[0].check.~.(resource.aws_eks_cluster.values(@)[])[0].(enabled_cluster_log_types  |        |
    |                             |                             |                | || `[]`).(contains(@, 'scheduler')): Invalid value: false: Expected value: true,     |        |
    |                             |                             |                | all[0].check.~.(resource.aws_eks_cluster.values(@)[])[0].(enabled_cluster_log_types  |        |
    |                             |                             |                | || `[]`).(contains(@, 'api')): Invalid value: false: Expected value: true]           |        |
    +-----------------------------+-----------------------------+----------------+--------------------------------------------------------------------------------------+--------+

    Done
    ```

---
