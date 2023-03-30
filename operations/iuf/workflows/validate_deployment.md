# Validate deployment

- [1. Execute the IUF `post-install-service-check` stage](#1-execute-the-iuf-post-install-service-check-stage)
- [2. Next steps](#2-next-steps)

## 1. Execute the IUF `post-install-service-check` stage

1. Refer to the "Install and Upgrade Framework" section of each individual product's installation documentation to determine if any special actions need to be performed outside of IUF for the `post-install-service-check` stage.

1. Invoke `iuf run` with `-r` to execute the [`post-install-service-check`](../stages/post_install_service_check.md) stage.

    (`ncn-m001#`) Execute the `post-install-service-check` stage.

    ```bash
    iuf -a "${ACTIVITY_NAME}" run -r post-install-service-check
    ```

Once this step has completed:

- Validation scripts have executed to verify the health of the product microservices
- Per-stage product hooks have executed for the `post-install-service-check` stage

## 2. Next steps

- If performing an initial install or an upgrade of non-CSM products only, return to the
  [Install or upgrade additional products with IUF](install_or_upgrade_additional_products_with_iuf.md)
  workflow to continue the install or upgrade.

- If performing an upgrade that includes upgrading CSM, return to the
  [Upgrade CSM and additional products with IUF](upgrade_csm_and_additional_products_with_iuf.md)
  workflow to continue the upgrade.