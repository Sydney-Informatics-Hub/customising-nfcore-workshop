In the case of the nf-core/rnaseq workflow, parameters are grouped based on various stages of the workflow:

1. **Input/output options** for specifying which files to process and where to save results
2. **UMI options** for processing reads with unique molecular identifiers (UMI)
3. **Read filtering options** to be run prior to alignment
4. **Reference genome options** related to pre-processing of the reference FASTA
5. **Read trimming options** prior to alignment
6. **Alignment options** for read mapping and filtering criteria
7. **Process skipping options** for adjusting the processes to run with the workflow
Three additional parameter sections are hidden from view. This is because they are less commonly used. They include:

8. **Institutional config options** for various compute environments
9. **Max job request options** for limiting memory and cpu usage based on what is available to you
10. **Generic options** focused on how the pipeline is run

For the sake of expediency, we are using prepared subset data for this session. All the data (including fastqs, input manifest, reference fasta, gtf, and STAR indexes) are available on an external file system called CernVM-FS. CernVM-FS is a read-only file system that Pawsey have used to store files such as containerised tools ([Biocontainers](https://biocontainers.pro/)), reference datasets, and other shared resources that are commonly used by many researchers. Take a look [here](https://support.pawsey.org.au/documentation/display/US/Nimbus+for+Bioinformatics) for more information on bioinformatics resources provided by Pawsey on Nimbus. 

::

In Nextflow workflows, additional configuration files can be included using the keyword `includeConfig`. In the nf-core/rnaseq workflow's `nextflow.config` this keyword is used to load the `conf/base.config` file, which defines a set of process-specific and workflow-level parameters that define resource requirements. This config file should be appropriate for general use on most HPC environments. Take a look at this file: 

```default
cat /home/ubuntu/session2/nf-core-rnaseq-3.11.1/workflow/conf/base.config
```

The `conf/base.config` makes use of a number of Nextflow keywords. It has a `process` section and a series of `withLabel` and `withName` subsections: 

* The [Process](https://www.nextflow.io/docs/latest/process.html) scope defines default resource requirements for all processes in the workflow
* Process selectors [withLabel](https://www.nextflow.io/docs/latest/config.html?highlight=withlabel#process-selectors) and [withName](https://www.nextflow.io/docs/latest/config.html?highlight=withlabel#process-selectors) define process-specific configurations that can be applied selectively depending on their resource requirements. 

Let's look at how processes have been labelled to reflect these resource configuration in the nf-core/rnaseq workflow: 

```default
grep "label" rnaseq/modules/*/*/main.nf
```

```default
rnaseq/modules/nf-core/fastp/main.nf:       label 'process_medium'
rnaseq/modules/nf-core/fastqc/main.nf:      label 'process_medium'
rnaseq/modules/nf-core/gffread/main.nf:     label 'process_low'
rnaseq/modules/nf-core/gunzip/main.nf:      label 'process_single'
rnaseq/modules/nf-core/sortmerna/main.nf:   label "process_high"
rnaseq/modules/nf-core/trimgalore/main.nf:  label 'process_high'
rnaseq/modules/nf-core/untar/main.nf:       label 'process_single'
```

Only some of the processes have been labelled, for example the [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) process that performs quality checks on fastq files, has been labelled as a medium process. The `conf/base.config` file allocates processes labelled a medium a maxiumum of 6 CPUs, 36 Gb and 8 hours of walltime. 

```default
withLabel:process_medium {
  cpus   = { check_max( 6     * task.attempt, 'cpus'    ) }
  memory = { check_max( 36.GB * task.attempt, 'memory'  ) }
  time   = { check_max( 8.h   * task.attempt, 'time'    ) }
}
```

The `check_max()` function applies the thresholds set by `--max_cpus`, `--max_memory` and `--max_time` parameters. The * task.attempt means that these values are doubled if a process is automatically retried after failing with an exit code that corresponds to a lack of resources.

::: callout-tip
### **Challenge**{.unlisted}

:::

::: {.callout-caution collapse="true"}
### Solution

:::

Resource configurations present in the `conf/base.config` file are not appropriate for our compute environment, given we are working with only 2 CPUs, 8 Gb of RAM, and <40 Gb of disk space. Additionally, from previous experience running FastQC we know this resource allocation goes above requirements for RNAseq data. The [`conf/base.config` files are deliberately generous](https://nf-co.re/usage/configuration#tuning-workflow-resources) due to the variety of possible workflow applications. There are opportunities for us to configure resources for this workflow to make better use of the computational environment we're working with. 

### **When to use a custom config file**

In the past two lessons, we've successfully overridden these maximum resource allocations specified in the `nextflow.config` and `conf/base.config` using flags in our run command. There are a number of situations in which you may want to write a custom configuration file:

1. To override the default resource allocations of the workflow specified in the `nextflow.config` 
2. To override the default resource allocations for a process specified in `conf/base.config`
3. To use a different software installation method than those supported by nf-core 
4. To run a workflow on an HPC and interact with a job scheduler like PBSpro or SLURM 

Creating a custom configuration file is good practice to ensure that your workflow runs efficiently and reproducibly across different environments. It also allows you to easily share your workflow with others, who can use your custom config file to run the workflow in the same way in the same computational environment or on the same infrastructure. 

### **Write a custom config**

Let's create a custom configuration file to override the default configurations specified by `nextflow.config` and `conf/base.config` that match the compute resources available to us on our instances. Open a new file called `custom.config` and add the following lines: 

```default
// Custom config for 2c.8r Nimbus instance

params {
  max_cpus = 2
  max_memory = '8.GB'
}
```

Now rerun the workflow with your custom config,again using the resume function and our `params.yaml` file but this time without using the --max_cpu and --max_mem flags: 

```default
nextflow run rnaseq/main.nf \
  -c custom.config \
  -profile singularity \
  -resume \
  -params-file params.yaml \
  --outdir Exercise3  
```

Note that we recieve a warning about specifying parameters in a config file and our workflow fails to run as we've not adequately specified maximum resources for CPUs and RAM:

```default
WARN: ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  Multiple config files detected!
  Please provide pipeline parameters via the CLI or Nextflow '-params-file' option.
  Custom config files including those provided by the '-c' Nextflow option can be
  used to provide any configuration except for parameters.

  Docs: https://nf-co.re/usage/configuration#custom-configuration-files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

### **How to use a config file**

You can apply a custom configuration file to your workflow by passing it as an argument in the nextflow run command using the `-c` flag. The syntax for specifying a custom configuration file is: 

```default
nextflow run <pipeline>/main.nf -c <name>.config
```

nf-core also offers a centralised collection of Nextflow configurations files that work at an infrastructure or institutional level, called [nf-core/configs](https://github.com/nf-core/configs). These configuration files may be:

* **Global profiles** that can be applied to all nf-core pipelines (in theory)
* **Pipeline profiles** for configuring a specific nf-core pipeline at an institutional infrastructure.

These can be applied to your workflow by including the [`-profile`](https://www.nextflow.io/docs/latest/config.html#config-profiles) flag in your run command.  The syntax for specifying an institutional configuration file is: 

```default
nextflow run <pipeline>/main.nf -profile <name>.config
```
