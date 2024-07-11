# Submit Jobs

This module can be used if you want to submit JOBs. The module does not uses artifacts. You should define your JOBs in your deployment configuration statically.

## Available Modules

**[job_submit](https://www.ibm.com/docs/en/SSQ2R2_16.0.0/com.ibm.wazi.deploy.doc/wd_templ_job_submit.html)**

This building block submits the execution of a JCL from a PDS or a z/OS UNIX System Services directory. It is not related to artifacts.

## Configuration

In your `deploy.yml` create a new variable under `spec`. Define you job submission variables which will be passed to the `job_submit` module. Under this variable define your jobs and settings.

### Define job submission settings

``` yaml
  <job submit variable>:
     src: '{{ env.spec.<template variable>.src }}'
     max_rc: <rc value>
     wait_time_s: <time value>

  job_submit_default:
     src: '{{ env.spec.app_hlq }}.<jcl library>(<jcl member>)'
     max_rc: <rc value>
     wait_time_s: <time value>
```

- **job submit variable**: the name of your job submission list variable. You should use this name when configuring the `job_submit` module execution.
    - **src**: the path or dataset name of your jcl file or member
    - **max_rc**: the highest expected return code
    - **wait_time_s**: time in seconds to wait for the job to complete

If you do not indicate your `<job submit variable>` property in a step of the deployment method, then the values of the default `job_submit_default` variables are used by the `job_submit` module.

## Samples

deploy.yml

``` yaml

my_template_jcl:
    src: "{{ env.spec.app_uss_root }}/source_templates/test01-jcl.j2"
    dest: "{{ env.spec.app_uss_root }}/rendered_files/test01.jcl"

  my_job_submit:
     src: '{{ env.spec.my_template_jcl.dest }}'
     max_rc: 8
     wait_time_s: 25

  job_submit_default:
     src: '{{ env.spec.app_hlq }}.JCL(HELOWRLD)'
     max_rc: 4
     wait_time_s: 30
```

deployment plan

``` yaml
  - name: SUBMIT A JCL TO RUN A JOB
    short_name: SUBMIT_JOB
    description: |
      sample job submission activites
    actions:
      - name: SUBMIT A JCL TO RUN A JOB
        short_name: SUBMINT_JOB
        description: |
          submit job from jcl file actions
        steps:
        - name: TEST STEP FOR THE SUBMISSION OF A JCL
          short_name: JOB_TEST
          description: |
              submit job from jcl file
          properties:
            - key: template
              value: job_submit
            - key: var_job_submit
              value: my_job_submit # from deploy_sample.yml
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