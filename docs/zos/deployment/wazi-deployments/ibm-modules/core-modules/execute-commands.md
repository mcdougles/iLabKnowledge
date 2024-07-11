# Submit Jobs

You can use thios module to execute unix shell, tso or system commands during your deployment. These modules do not use artifacts. You should define your commands in your deployment configuration.

## Available Modules

**[shell_command](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_shell_command.html)**

This building block runs a shell command. It is not related to artifacts.

**[system_command](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_syst_command.html)**

This building block runs a z/OS command. It is not related to artifacts.

**[tso_command](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_tso_command.html)**

This building block runs one or more TSO commands on the target z/OS system with the provided options and receives a structured response.. It is not related to artifacts.


## Configuration

In your `deploy.yml` create a new variable under `spec`. Define you command variables which will be passed to the appropriate modules. Under this variables define your commands and other parameters.

### Define command settings

``` yaml

  # UNIX shell command/script
  <shell command identifier>: |
    #!/bin/sh
    <your shell script>"
  shell_command_cmd_default: |
    #!/bin/sh
    <your default shell script>"

# TSO command
  <tso command identifier>: <your tso command>
  tso_command_default: <your default tso command>


  # SYSTEM command
  <system command identifier>: <your system command>
  system_command_cmd_default: <your default system command>

```

- **command variable**: the name of your shell/tso/system command variable. You should use this name when configuring the shell/tso/system command module execution.
    - **command**: the shell script or tso or system command you want to execute


If you do not indicate your `<command variable>` property in a step of the deployment method, then the values of the default `shell_command_cmd_default` or `tso_command_default` or `system_command_cmd_default` variables are used by the `shell/tso/system command` modules.

## Samples

deploy.yml

``` yaml
# executest the shell command named 'my_shell_command', use this name in your deployment method definition for 'shell_command' module
  my_shell_command: |
    #!/bin/sh
    echo "my other shell command"

  # executest the default shell command
  shell_command_cmd_default: |
   #!/bin/sh
   echo "default shell command"

  # executest the system command named 'my_system_command', use this name in your deployment method definition for 'system_command' module
  my_system_command: "D SYMBOLS"

  # executest the default system command
  system_command_cmd_default: "D T"

  # executest the shell command named 'my_system_command', use this name in your deployment method definition for 'tso_command' module
  my_tso_command:
    commands:
      - LU TESTUSER
    max_rc: 4

  # executest the default tso command
  tso_command_default:
    commands:
      - DELETE '{{ env.spec.app_hlq }}.SEQ01'
      - DELETE '{{ env.spec.app_hlq }}.PDS01'
    max_rc: 4
```

deployment plan

``` yaml
  - name: RUN UNIX SHELL COMMANDS ACTIVITIES
    short_name: SHELL_COMMAND
    description: |
      sample unix shell command activities
    actions:
      - name: RUN UNIX SHELL COMMANDS ACTIONS
        short_name: SHELL_COMMAND
        description: |
          sample unix shell command actions
        steps:
        - name: TEST STEP FOR SHELL_COMMAND
          short_name: SHELL_COMMAND_TEST
          description: |
            sample unix shell command
          properties:
            - key: template
              value: shell_command
            - key: var_shell_command
              value: my_shell_command # from deploy_sample.yml
          tags:
            - always
          plan_tags:
            - always
        is_artifact: False
        tags:
          - always
        plan_tags:
          - always

  - name: RUN ZOS SYSTEM COMMANDS ACTIVITIES
    short_name: SYSTEM_COMMAND
    description: |
      sample zos system command activities
    actions:
      - name: RUN ZOS SYSTEM COMMANDS ACTIONS
        short_name: SYSTEM_COMMAND
        description: |
          sample zos system actions
    steps:
      - name: TEST STEP FOR SYSTEM COMMAND DISPLAY SYMBOLS
        short_name: SYSTEM_COMMAND_TEST
        description: |
          sample zos system command
        properties:
        - key: template
          value: system_command
        - key: var_system_command
          value: my_system_command # from deploy_sample.yml
        tags:
          - always
        plan_tags:
          - always
    is_artifact: False
    tags:
      - always
    plan_tags:
      - always

  - name: RUN TSO COMMANDS ACTIVITIES
    short_name: TSO_COMMAND
    description: |
      sample tso command activities
    actions:
      - name: RUN TSO COMMANDS ACTIONS
        short_name: TSO_COMMAND
        description: |
          sample tso command actions
        steps:
        - name: TEST STEP FOR TSO_COMMAND
          short_name: TSO_COMMAND_TEST
          properties:
            - key: template
              value: tso_command
            - key: var_tso_command
              value: my_tso_command # from deploy_sample.yml
          tags:
            - always
          plan_tags:
            - always
        is_artifact: False
        tags:
          - always
        plan_tags:
          - always
```