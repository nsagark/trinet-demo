# Dockerfile Environment Variable Policy

## Policy Details:

- **Policy Name:** validate-environment-variables
- **Check Description:** This policy checks whether environment variables `ACCESS_KEY` and `ACCESS_SECRET` are defined in the Dockerfile but do not contain the actual values.
- **Policy Category:** Dockerfile Best Practices for Security

## How It Works:

### Validation Criteria:

**Keys**: ACCESS_KEY, ACCESS_SECRET

- **Condition:** If values for `ACCESS_KEY` and `ACCESS_SECRET` are included in the Dockerfile
  - **Result:** FAIL

- **Condition:** If environment variables `ACCESS_KEY` and `ACCESS_SECRET` are defined but do not include values
  - **Result:** PASS

### Policy Validation Testing Instructions:

To evaluate and test the policy ensuring Dockerfile compliance:

1. **Extract JSON equivalent of the Dockerfile:**
    ```
    nctl scan dockerfile -r test/good-test/Dockerfile --show-json > payload.json
    ```

2. **Test the Policy with Kyverno:**
    ```
    kyverno-json scan --payload payload.json --policy validate-environment-variables.yaml
    ```
    
    a. **Test Policy Against Valid Payload:**
    ```
    kyverno-json scan --policy validate-environment-variables.yaml --payload test/good-test/good-payload.json
    ```

    This produces the output:
    ```
    Loading policies ...
    Loading payload ...
    Pre processing ...
    Running ( evaluating 1 resource against 1 policy ) ...
    - validate-environment-variables / validate-environment-variables /  PASSED
    Done
    ```

    b. **Test Against Invalid Payload:**
    ```
    kyverno-json scan --policy validate-environment-variables.yaml --payload test/bad-test/bad-payload.json
    ```

    This produces the output:
    ```
    Loading policies ...
    Loading payload ...
    Pre processing ...
    Running ( evaluating 1 resource against 1 policy ) ...
    - validate-environment-variables / validate-environment-variables /  FAILED: Environment variables ACCESS_KEY and ACCESS_SECRET should not contain values in the Dockerfile: any[0].check.(Stages[].Commands[?Name==env[]][?Arguments==ACCESS_KEY || Arguments==ACCESS_SECRET][] | length(@) > `0`): Invalid value: false: Expected value: true
    Done
    ```

By adhering to this policy, you enhance the security posture of your Dockerized applications by ensuring that sensitive environment variables are properly handled, minimizing the potential risk of exposing sensitive information.