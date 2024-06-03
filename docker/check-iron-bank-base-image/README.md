# Enforce Iron Bank Base Image Policy

The "Enforce Iron Bank Base Image" policy ensures that the Dockerfile base image comes from the Iron Bank registry, which is the Department of Defense's trusted repository for container images.

## Policy Details:

- **Policy Name:** enforce-ironbank-base-image
- **Description:** This policy validates that the Dockerfile base image is pulled from the Iron Bank registry.
- **Category:** Container Security
- **Severity:** Medium

## Policy Explanation:

### Validation Criteria:

- **Condition:** If the Dockerfile contains at least one stage with a base image.
  - **Result:** PASS

- **Condition:** If the base image starts with `harbor.cloudiolite-demo.karthikconsulting.com/`.
  - **Result:** FAIL

### Testing Instructions:

To test compliance with the policy:

1. **Extract JSON equivalent of the Dockerfile:**
    ```bash
    nctl scan dockerfile -r test/good-test/Dockerfile --show-json | jq > payload.json
    ```

2. **Test the Policy with Kyverno:**
    ```bash
    kyverno-json scan --payload payload.json --policy enforce-ironbank-base-image.yaml
    ```

    a. **Test Policy Against Valid Payload:**
    ```bash
    kyverno-json scan --policy enforce-ironbank-base-image.yaml --payload test/good-test/good-payload.json
    ```

    This produces the output:
    ```
    Loading policies ...
    Loading payload ...
    Pre processing ...
    Running ( evaluating 1 resource against 1 policy ) ...
    - enforce-ironbank-base-image / enforce-ironbank-base-image /  PASSED
    Done
    ```

    b. **Test Against Invalid Payload:**
    ```bash
    kyverno-json scan --policy enforce-ironbank-base-image.yaml --payload test/bad-test/bad-payload.json
    ```

    This produces the output:
    ```
    Loading policies ...
    Loading payload ...
    Pre processing ...
    Running ( evaluating 1 resource against 1 policy ) ...
    - enforce-ironbank-base-image / enforce-ironbank-base-image /  FAILED: The Dockerfile base image must be pulled from the Iron Bank registry.
    Done
    ```

## Ensuring Compliance with Iron Bank Registry

To adhere to this policy, ensure that your Dockerfiles reference base images from the Iron Bank registry. For example:

```Dockerfile
FROM harbor.cloudiolite-demo.karthikconsulting.com/iron-bank-base-image:latest
```

By enforcing this policy, you strengthen the security of your containerized environments by using trusted images from the Iron Bank registry, approved by the Department of Defense.