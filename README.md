# ansible-test

**ansible-test** is a lightweight framework for testing Ansible roles under various conditions, including different sets of `defaults` variables. It provides a flexible structure for quickly validating roles against changing inputs, host configurations, and infrastructure setups.

## Features

- Simple environment-based configuration
- Automatic generation of temporary inventory, playbook, and variable files
- Built-in support for running tests with overridden variables
- Optional host reset between tests using VM snapshots
- Flexible handling of expected success or failure scenarios
- Clean, colorful, structured output for easy test result tracking

## Getting Started

### Prerequisites

- Bash (POSIX-compliant shell)
- Ansible installed and available in `PATH`
- Optional: Snapshot management tool (`snapshot`) if host reset functionality is needed

### Usage

First, pull and install [bash-test](https://github.com/coreyMerritt/bash-test).

Next, Set the env variable `ANSIBLE_TEST_PROJECT_DIR` to the directory that `ansible-test` is in.

Then, source the `ansible-test` framework in your `some-test-script.sh`:

```bash
source "$ANSIBLE_TEST_PROJECT_DIR/ansible-test"
```

Then, ensure the following environment variables are set:

| Variable           | Purpose                             |
|--------------------|-------------------------------------|
| `AT_ROOT_DIR`       | Root directory where inventories, playbooks, and vars will be created |
| `AT_ROLES_PATH`     | Path to available Ansible roles |
| `AT_HOST_ADDRESS`   | Address of the host that the ansible will run against |
| `AT_HOST_SSH_PORT`  | SSH port for connecting to the host |
| `AT_HOST_USER`      | SSH user for connecting to the host |
| `AT_HOST_PASS`      | SSH password for connecting to the host |
| `AT_VM_NAME`        | (Optional) VM name for snapshot resets |
| `AT_SNAPSHOT_NAME`  | (Optional) Snapshot name for resets |

You can also control test behavior with these optional variables:

| Variable                      | Purpose                                 | Default |
|--------------------------------|-----------------------------------------|---------|
| `at_expect_role_success`       | Expect the role to succeed (`1`) or fail (`0`) | `1`     |
| `at_reset_host_before_tests`   | Reset the host before each test (1 / 0) | `1`     |
| `at_run_included_tests`        | Run embedded role tests if available    | `1`     |
| `at_included_tests_wait_time`  | Wait time (seconds) before running embedded tests  | `150`   |

### Example

```bash
#!/bin/bash

source "$ANSIBLE_TEST_PROJECT_DIR/ansible-test"

# Example of running the role with default variables
atRunTest

# Example of running the role with overridden variables
override_vars=(
'example_variable: true
another_variable: "custom"'
)
atRunTest

# Example of expecting the role to fail
override_vars=(
'some_var: "BAD_VALUE"'
)
at_expect_role_success=0
atRunTest
```

### Resetting the Host

If `AT_VM_NAME` and `AT_SNAPSHOT_NAME` are set, `ansible-test` can automatically revert the test host to a clean state before each test run using the [snapshot](https://github.com/coreyMerritt/scripts) tool (requires manual installation):

```bash
atRunTest

atResetHost

# This will run against a fresh snapshot
atRunTest
```

## License

This project is licensed under the [MIT License](LICENSE).

---

**Note:**  
The framework is intended to remain lightweight, flexible, and easy to adapt to various testing scenarios without requiring constant updates to the framework itself.

