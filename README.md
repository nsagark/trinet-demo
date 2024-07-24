# kyverno-json-policies

This is a private repository to collaborate on Kyverno JSON use cases. The policies will be moved to [kyverno-policies](https://github.com/nirmata/kyverno-policies) repo once it is ready for public consumption.

# Policy writing checklist

To maintain consistent policy UX and good repo hygiene, follow the below guidelines.

## Directory Structure
Here are some sample directory structures -
* `aws/ecs/terraform/plan/validate-ecs-containers-nonprivileged`
* `cloud-custodian/detect-pe-instances`
* `dockerfile/verify-base-image`

Every directory should contain -
* `README.md` - this file should contain some explanation of what the policy does, and how to run the policy
* `<policy-name>.yaml` - this is the policy YAML file
* `test` - this is the directory that should contain
    * good-payload.json
    * bad-payload.json
    * good-terraform.tf / good-dockerfile
    * bad-terraform.tf / bad-dockerfile

## Nirmata Annotations

The following annotations have to be present for all the policies. Reference policy - [Disallow Host Namespaces](https://github.com/nirmata/kyverno-policies/blob/789c79d510db09df38957047e3d29900f8e803da/pod-security/baseline/disallow-host-namespaces/disallow-host-namespaces.yaml#L6-L17)
* `policies.kyverno.io/title`
* `policies.kyverno.io/category`
* `policies.kyverno.io/severity`
* `policies.kyverno.io/description`
* `policies.nirmata.io/remediation-docs` (optional)
* `policies.nirmata.io/remediation` (optional)

