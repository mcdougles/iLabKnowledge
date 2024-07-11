# Setup your repository for Wazi Deploy

**New repository**

You can create a new repository using the wazi-deploy template. After you created a new repository, your can alter the pipeline configuration and do your testing. After you are ready with your configuration you can copy your artifacts from your old repository into your main branch (this will not trigger the pipeline).

1. Create a new repository

    Use the [zOS Application Template](https://github.ibm.com/cio-ci-cd/zos-application-template){:target="_blank" rel="noopener"} to [create your new repository](../../migration/repository-setup.md){:target="_blank" rel="noopener"}.

    As you changing your repository you must repeat the encryption key setup in your `build.yml`.

2. Create new deployment method
	Using the provided template and the samples create your `dbb_static_package.yml` for wazi deploy.

3. Create new environment configurations for your environments
	Using the provided template and the samples create your new `deploy.yml` for wazi deploy.

4. Create your templates and other configuration files
    Using the provided templates and the samples create your job templates and other supplementary files, e.g.: for BIND, QMF, IWS, etc. You can copy them from your old repository as well. Make sure you understand [Jinja2 templating](https://jinja.palletsprojects.com/en/3.1.x/){:target="_blank" rel="noopener"}

5. Move your source code
    After your pipeline is running from the new repository, you can copy source files from your old repository (`/src/main/...`) It is suggested to move your soursec into the `main` branch, else it will trigger a build on all the sources.