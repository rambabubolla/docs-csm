# Stage 1 - Ceph image upgrade

**Reminder:** If any problems are encountered and the procedure or command output does not provide relevant guidance, see
[Relevant troubleshooting links for upgrade-related issues](README.md#relevant-troubleshooting-links-for-upgrade-related-issues).

- [Start typescript](#start-typescript)
- [Apply boot order workaround](#apply-boot-order-workaround)
- [Argo web UI](#argo-web-ui)
- [Storage node image upgrade](#storage-node-image-upgrade)
- [Ensure that `rbd` stats monitoring is enabled](#ensure-that-rbd-stats-monitoring-is-enabled)
- [Stop typescript](#stop-typescript)
- [Stage completed](#stage-completed)

## Start typescript

1. (`ncn-m001#`) If a typescript session is already running in the shell, then first stop it with the `exit` command.

1. (`ncn-m001#`) Start a typescript.

    ```bash
    script -af /root/csm_upgrade.$(date +%Y%m%d_%H%M%S).stage_1.txt
    export PS1='\u@\H \D{%Y-%m-%d} \t \w # '
    ```

If additional shells are opened during this procedure, then record those with typescripts as well. When resuming a procedure
after a break, always be sure that a typescript is running before proceeding.

## Apply boot order workaround

(`ncn-m001#`) Apply a workaround for the boot order:

```bash
/usr/share/doc/csm/scripts/workarounds/boot-order/run.sh
```

## Argo web UI

Before starting [Storage node image upgrade](#storage-node-image-upgrade), access the Argo UI to view the progress of this stage.
Note that the progress for the current stage will not show up in Argo before the storage node image upgrade script has been started.

For more information, see [Using the Argo UI](../operations/argo/Using_the_Argo_UI.md).

## Storage node image upgrade

(`ncn-m001#`) Run `ncn-upgrade-worker-storage-nodes.sh` for all storage nodes to be upgraded. Provide the storage nodes in a comma-separated list, such as `ncn-s001,ncn-s002,ncn-s003`. This upgrades the storage nodes sequentially.

```bash
/usr/share/doc/csm/upgrade/scripts/upgrade/ncn-upgrade-worker-storage-nodes.sh ncn-s001,ncn-s002,ncn-s003
```

**`NOTE`**
It is possible to upgrade a single storage node at a time using the following command.

```bash
/usr/share/doc/csm/upgrade/scripts/upgrade/ncn-upgrade-worker-storage-nodes.sh ncn-s001
```

## Ensure that `rbd` stats monitoring is enabled

(`ncn-m001#`) Run the following commands to enable the `rbd` stats collection on the pools.

```bash
ceph config set mgr mgr/prometheus/rbd_stats_pools "kube,smf"
ceph config set mgr mgr/prometheus/rbd_stats_pools_refresh_interval 600
```

## Stop typescript

Stop any typescripts that were started during this stage.

## Stage completed

All the Ceph nodes have been rebooted into the new image.

This stage is completed. Continue to [Stage 2](Stage_2.md).