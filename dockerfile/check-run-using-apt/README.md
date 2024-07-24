The preference for apt-get over `apt` in scripts, including Dockerfiles, primarily stems from `apt-get`'s stable interface guarantee and its non-interactive nature, which are essential for automation and scripting purposes. 

`apt-get` provides a command-line interface that is guaranteed to be stable over time. This means its options, behavior, and output are less likely to change in future versions, ensuring scripts that use `apt-get` remain functional and predictable across different environments and updates.

This policy checks whether you're using the `apt` command in the Dockerfile. If you're using the `apt` command, the policy will give you failing checks else passing checks will be given if you're using `apt-get` command.

**In order to test this policy, use the following commands:**

- Make sure you have `kyverno-json` installed on the machine
- Make sure you have [nctl `v3.4.0`](https://downloads.nirmata.io/nctl/downloads/) or above.


1. **Extract JSON equivalent of the dockerfile:**
    ```bash
    nctl scan dockerfile -r test/good-test/Dockerfile --show-json > payload.json
    ```

2. **Test the Policy with Kyverno:**
    ```bash
    kyverno-json scan --payload payload.json --policy check-apt-command-force-yes.yaml
    ```
    a. **Test Policy Against Valid Payload:**
    ```bash
    kyverno-json scan --policy check-run-using-apt.yaml --payload test/good-test/good-payload.json
    ```

    This produces the output:

    ```
    Loading policies ...
    Loading payload ...
    Pre processing ...
    Running ( evaluating 1 resource against 1 policy ) ...
   - check-run-using-apt / check-run-using-apt /  PASSED
    Done
    ```

    b. **Test Policy Against Invalid Payload:**
    ```bash
    kyverno-json scan --policy check-run-using-apt.yaml --payload test/bad-test/bad-payload.json
    ```

    This produces the output:
    ```
    Loading policies ...
    Loading payload ...
    Pre processing ...
    Running ( evaluating 1 resource against 1 policy ) ...
   - check-run-using-apt / check-run-using-apt /  FAILED
    -> Use `apt-get` over  `apt` since `apt-get` interface is more stable
    -> all[0].check.~.(Stages[].Commands[?Name=='RUN'].CmdLine[][])[0].((starts_with(@, 'apt ') || contains(@, ' apt '))): Invalid value: true: Expected value: false
    Done
    ```