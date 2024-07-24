# Validating Exposed Ports for the Service in Dockerfile

Ensuring that a container image only exposes the necessary ports for the implemented service is crucial for minimizing the attack surface and following the principle of least privilege. This policy, `validate-expose-port-8080`, aims to validate the exposed ports in a Dockerfile to meet this requirement.

## Policy Details:

- **Policy Name:** validate-expose-port-8080
- **Check Description:** This policy checks whether Dockerfiles expose only the ports used for the service.
- **Policy Category:** Dockerfile Security Best Practices

## How It Works:

### Validation Criteria:

**Key**: Ports

- **Condition:** If `Ports` under EXPOSE contains only specified ports (e.g., 8080/tcp)
  - **Result:** PASS
  - **Reason:** Compliance with the policy is achieved when only the necessary ports for the service are exposed.

- **Condition:** If `Ports` contains any other port
  - **Result:** FAIL
  - **Reason:** Exposing unnecessary ports can increase the attack surface, violating security best practices.

### Policy Validation Testing Instructions

To evaluate and test the policy ensuring Dockerfile compliance:

For testing this policy, follow these steps:
- Ensure you have `kyverno-json` installed on your machine.
- Ensure you have [nctl `v3.4.0`](https://downloads.nirmata.io/nctl/downloads/) or a higher version installed.

1. **Extract JSON equivalent of the Dockerfile:**
    ```
    nctl scan dockerfile -r test/good-test/Dockerfile --show-json > payload.json
    ```

2. **Test the Policy with Kyverno:**
    ```
    kyverno-json scan --payload payload.json --policy validate-expose-port-8080.yaml
    ```
    
    a. **Test Policy Against Valid Payload:**
    ```
    kyverno-json scan --policy validate-expose-port-8080.yaml --payload test/good-test/good-payload.json
    ```

    This produces the output:
    ```
    Loading policies ...
    Loading payload ...
    Pre processing ...
    Running ( evaluating 1 resource against 1 policy ) ...
    - validate-expose-port-8080 / validate-expose-port-8080 /  PASSED
    Done
    ```

    b. **Test Against Invalid Payload:**
    ```
    kyverno-json scan --policy validate-expose-port-8080.yaml --payload test/bad-test/bad-payload.json
    ```

    This produces the output:
    ```
    Loading policies ...
    Loading payload ...
    Pre processing ...
    Running ( evaluating 1 resource against 1 policy ) ...
    - validate-expose-port-8080 / validate-expose-port-8080 /  FAILED
    -> All exposed ports should be either 8080, 22, 443, or 80.
    -> any[0].check.~.(Stages[].Commands[?Name=='EXPOSE'].Ports[][])[1].(contains(['8080','22','443','80'], @)): Invalid value: false: Expected value: true
    Done
    ```

**Note**

  Update the port in the Docker

