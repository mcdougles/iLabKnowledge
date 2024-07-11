# Migration to Wazi Deploy

**In-place migration**

You can use your existing repository and make necessary changes to work with Wazi Deploy. Create a new branch from your main and alter your configuration. Then test your changes by creating Pull Requests an commits. When you ready to use your Wazi Deploy config, merge your changes into your main branch. If you have any runnig developments - story branches -, merge config changes back to those branches.

1. Create a new branch from your main branch

2. Update build.yml
    Change your `version` property to `v3` and `deployment-tool` from `plum` to `wazi`

    change `build` values:
    ``` yaml
    build:
		strategy: native-zos
		pipeline: zos-application
		version: v3
		config:
			hlq: CICD1.GENAPP
			deployment-tool: wazi
    ```

3. Update you deployment method configuration (`dbb_static_deploy.yml`)
    Convert your activities, actions and steps to follow the wazi deploy required structure and syntax.
    Definition of the deployment module for a step is changed. The plum syntax is the following:

    ``` yaml
    steps:
        - name: MEMBER_ARCHIVE_ZOAUCOPY
        - name: MEMBER_COPY_ZOAUCOPY
    ```

    The new syntax for wazi deploy has a `properties` key and it also has `key` and `value` pairs.
    The `key` named `template` should have the value of the name the deployment module˛, e.g.: `member_copy`

    ``` yaml
    steps:
        - name: COPY JCLs TO OPC LIBRARY
          short_name: JCL_TO_OPC
           description: |
             copy changed jcl members to the opc library
          properties:
          - key: template
            value: member_copy
    ```

    The full structure of an activity:

    ``` yaml
    activities:
      - name: <activity anem>
        short_name: <activity shortname>
        description: |
          <long description of the activity>
        actions:
          - name: <action name>
            short_name: <action shortname>
            description: |
              <long description of the actions>
            states: <UNDEFINED | DELETED>
            steps:
              - name: <step name>
                short_name: <step shortname>
                 description: |
                   <long description of the step>
                properties:
                - key: template
                  value: <module name>
            types:
              - name: <deployment type>
              - name: <deployment type>
            is_artifact: <True | False>
            tags: # step level
              <always | never | text>
            plan_tags: # step level
              <always | never | text>
          tags: # action level
            <always | never | text>
          plan_tags: # action level
            <always | never | text>
        tags: # activity level
          <always | never | text>
        plan_tags: # activity level
          <always | never | text>

    ```
4. Update your deployment configuration parameters in `deploy.yml`
    Edit your deploy.yml file and chanage your keys and values to match the required syntax of your deployment modules.
    The `member_copy/archive/delete` module now requires to change `pds_mapping` to `types`.
    Use the python Jinaja2 syntax - `{{ env.spec.keyname}}` - to refer on other keys in your yaml file. See the `spec` key refering on the dataset specification.

    from plum:
    ``` yaml
    spec:
        #
        #...
        #
        pds_mappings:
            - pattern: <selection pattern>
              spec: <dataset specification identifier>
              dataset: <target library>"
              dataset_backup: <backup library>"

    ```

    to wazi:
    ``` yaml
    spec:
        #
        #...
        #
        types:
            - pattern: <selection pattern>
              spec: "{{ env.spec.<dataset specification identifier> }}"
              dataset: <target library>"
              dataset_backup: <backup library>"

    ```
    Also, change the key names for the dataset specifications, e.g.: `space_primary` to `primary_space` :

    from plum:
    ``` yaml
    common_pds_load_spec:
        type: <datset type, SEQ | PDS | PDSE | LIBRARY | BASIC | LARGE>
        primary_space: <value><CYL | TRK | KB | MB | GB>
        secondary_space: <value><CYL | TRK | KB | MB | GB >
        record_format: <record format, FB | FBA | FBS | U | VB | VBA | VBS>
        record_length: <value>
        block_size: <value>
    ```

    to wazi:
    ``` yaml
    <dataset specification identifier>:
        type: <datset type, SEQ | PDS | PDSE | LIBRARY | BASIC | LARGE>
        space_primary: <value><CYL | TRK | KB | MB | GB>
        space_secondary: <value><CYL | TRK | KB | MB | GB >
        record_format: <record format, FB | FBA | FBS | U | VB | VBA | VBS>
        record_length: <value>
        block_size: <value>
    ```

    For other changes see the documentation article for the specific module.

5. Update your job templates
    Find your job templates, e.g.: for IWS, QMF or DB2 BIND modules and replace them using the samples from the wazi deploy template repository.

