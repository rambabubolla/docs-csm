# Exporting and Importing BSS Data

- [Prerequisites](#prerequisites)
- [Export BSS boot parameters](#export-bss-boot-parameters)
- [Restore BSS boot parameters](#restore-bss-boot-parameters)
- [Update BSS after IMS import](#update-bss-after-ims-import)

## Prerequisites

- Ensure that the `cray` command line interface (CLI) is authenticated and configured to talk to system management services.
  - See [Configure the Cray CLI](../configure_cray_cli.md).
- In order to use the automated procedures, the latest CSM documentation RPM must be installed on the node where the procedure is being performed.
  - See [Check for latest documentation](../../update_product_stream/README.md#check-for-latest-documentation).

## Export BSS boot parameters

1. (`ncn-mw#`) Create a JSON file with the boot parameters.

   ```bash
   cray bss bootparameters list --format json > cray-bss-boot-parameters-dump.json
   ```

1. (`ncn-mw#`) Create a JSON file with the boot parameters for only the compute nodes.

   ```bash
   xnames=`cray hsm state components list --type Node --role Compute --format json | jq -r '.[] | map(.ID) | join(",")'`
   echo $xnames
   cray bss bootparameters list --name $xnames --format json > cray-bss-compute-boot-parameters-dump.json
   ```

## Restore BSS boot parameters

1. Copy the file generated by the export command to the node where the import procedure is being performed.

1. (`ncn-mw#`) Set up an API token.

   ```bash
   export TOKEN=$(curl -k -s -S -d grant_type=client_credentials -d client_id=admin-client \
       -d client_secret=$(kubectl get secrets admin-client-auth -o jsonpath='{.data.client-secret}' | base64 -d) \
       https://api-gw-service-nmn.local/keycloak/realms/shasta/protocol/openid-connect/token | jq -r '.access_token')
   ```

1. (`ncn-mw#`) Restore the boot parameters.

   > `cray-bss-compute-parameters-dump.json` is the name of the file created from the export procedure.

   ```bash
   /usr/share/doc/csm/scripts/operations/boot_script_service/bss-restore-bootparameters.sh cray-bss-compute-parameters-dump.json
   ```

## Update BSS after IMS import

**After** running an IMS
[Automated import procedure](../image_management/Exporting_and_Importing_IMS_Data.md#automated-import-procedure),
run the following script to update the BSS boot parameters.
The IMS import script should have generated a file containing the IMS ID and S3 `etag` mappings -- it is displayed near
the end of the script output.

1. (`ncn-mw#`) Set up an API token.

   ```bash
   export TOKEN=$(curl -k -s -S -d grant_type=client_credentials -d client_id=admin-client \
       -d client_secret=$(kubectl get secrets admin-client-auth -o jsonpath='{.data.client-secret}' | base64 -d) \
       https://api-gw-service-nmn.local/keycloak/realms/shasta/protocol/openid-connect/token | jq -r '.access_token')
   ```

1. (`ncn-mw#`) Update BSS based on the changes made during the IMS import.

   > `/root/ims-import-export-data/ims-id-maps-post-import-12f86451ce7c49d79e345bee42cc8586.json` is the file from the IMS import procedure.

   ```bash
   /usr/share/doc/csm/scripts/operations/boot_script_service/bss-update-ids-egags.py /root/ims-import-export-data/ims-id-maps-post-import-12f86451ce7c49d79e345bee42cc8586.json
   ```