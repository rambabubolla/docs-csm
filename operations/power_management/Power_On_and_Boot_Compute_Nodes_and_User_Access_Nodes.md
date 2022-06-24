# Power On and Boot Compute and User Access Nodes

Use Boot Orchestration Service \(BOS\) and choose the appropriate session template to power on and boot compute and UANs.

This procedure boots all compute nodes and user access nodes \(UANs\) in the context of a full system power-up.

## Prerequisites

* All compute cabinet PDUs, servers, and switches must be powered on.
* The Slingshot Fabric is up and configured.
  Refer to the following documentation for more information on how to bring up the Slingshot Fabric:
  * The *HPE Slingshot Operations Guide* PDF for HPE Cray EX systems.
  * The *HPE Slingshot Troubleshooting* PDF.
* An authentication token is required to access the API gateway and to use the `sat` command. See the "SAT Authentication" section
  of the HPE Cray EX System Admin Toolkit (SAT) product stream documentation (S-8031) for instructions on how to acquire a SAT authentication token.

## Procedure

1. List detailed information about the available boot orchestration service \(BOS\) session template names.

    Identify the BOS session template names (such as `"cos-2.0.x"`, `slurm`, or `uan-slurm`), and choose the appropriate compute and UAN node templates for the power on and boot.

    ```bash
    ncn-m001# cray bos sessiontemplate list
    ```

    Example output:

    ```text
    [[results]]
    name = "cos-2.0.x"
    description = "BOS session template for booting compute nodes, generated by the installation"
    . . .
    name = "slurm"
    description = "BOS session template for booting compute nodes, generated by the installation"
    . . .
    name = "uan-slurm"
    description = "Template for booting UANs with Slurm"
    ```

1. To display more information about a session template, for example `cos-2.0.0`, use the `describe` option.

    ```bash
    ncn-m001# cray bos sessiontemplate describe cos-2.0.x
    ```

1. Use `sat bootsys boot` to power on and boot UANs and compute nodes.

    **Attention:** Specify the required session template name for `COS_SESSION_TEMPLATE` and `UAN_SESSION_TEMPLATE` in the following command line.

    Use `--loglevel debug` command line option to provide more information as the system boots.

    ```bash
    ncn-m001# sat bootsys boot --stage bos-operations \
                --bos-templates COS_SESSION_TEMPLATE,UAN_SESSION_TEMPLATE
    ```

    Example output:

    ```text
    Started boot operation on BOS session templates: cos-2.0.x, uan.
    Waiting up to 900 seconds for sessions to complete.
    
    Waiting for BOA k8s job with id boa-a1a697fc-e040-4707-8a44-a6aef9e4d6ea to complete. Session template: uan.
    To monitor the progress of this job, run the following command in a separate window:
        'kubectl -n services logs -c boa -f --selector job-name=boa-a1a697fc-e040-4707-8a44-a6aef9e4d6ea'
    
    Waiting for BOA k8s job with id boa-79584ffe-104c-4766-b584-06c5a3a60996 to complete. Session template: cos-2.0.0.
    To monitor the progress of this job, run the following command in a separate window:
        'kubectl -n services logs -c boa -f --selector job-name=boa-79584ffe-104c-4766-b584-06c5a3a60996'
    
    [...]
    
    All BOS sessions completed.
    ```

    Note the returned job ID for each session; for example: `"boa-caa15959-2402-4190-9243-150d568942f6"`

1. Use the job ID strings to monitor the progress of the boot job.

    **Tip:** The commands needed to monitor the progress of the job are provided in the output of the `sat bootsys boot` command.

    ```bash
    ncn-m001# kubectl -n services logs -c boa -f --selector job-name=boa-caa15959-2402-4190-9243-150d568942f6
    ```

1. In another shell window, use a similar command to monitor the UAN session.

    ```bash
    ncn-m001# kubectl -n services logs -c boa -f --selector job-name=boa-a1a697fc-e040-4707-8a44-a6aef9e4d6ea
    ```

1. Wait for compute nodes and UANs to boot and check the Configuration Framework Service \(CFS\) log for errors.

1. Verify that nodes have booted and indicate `Ready`.

    ```bash
    ncn-m001# sat status
    ```

    Example output:

    ```text
    +----------------+------+----------+-------+------+---------+------+----------+-------------+----------+
    | xname          | Type | NID      | State | Flag | Enabled | Arch | Class    | Role        | Net Type |
    +----------------+------+----------+-------+------+---------+------+----------+-------------+----------+
    | x1000c0s0b0n0  | Node | 1001     | Ready | OK   | True    | X86  | Mountain | Compute     | Sling    |
    | x1000c0s0b0n1  | Node | 1002     | Ready | OK   | True    | X86  | Mountain | Compute     | Sling    |
    | x1000c0s0b1n0  | Node | 1003     | Ready | OK   | True    | X86  | Mountain | Compute     | Sling    |
    | x1000c0s0b1n1  | Node | 1004     | Ready | OK   | True    | X86  | Mountain | Compute     | Sling    |
    | x1000c0s1b0n0  | Node | 1005     | Ready | OK   | True    | X86  | Mountain | Compute     | Sling    |
    [...]
    ```

1. Make nodes available to customers and refer to [Validate CSM Health](../validate_csm_health.md) to check system health and status.

## Next Step

Return to [System Power On Procedures](System_Power_On_Procedures.md) and continue with next step.