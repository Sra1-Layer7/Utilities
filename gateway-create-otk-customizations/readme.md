## Creating your OTK Customizations Repository
An important part of administering the OTK is managing customizations. The Gateway Helm Chart and Layer Operator provide a way to both install and maintain these customizations, this example details how to go about exporting customizations from a development gateway for storage in source control. These can then be applied to your Gateways pre or post boot.

### Running the Gateway in Ephemeral Mode
When using the Gateway in Ephemeral mode (no mysql policy database) with the Gateway Helm Chart, these customizations will need to be bootstrapped using an [initContainer](https://github.com/Layer7-Community/Utilities/tree/main/gateway-init-container-examples). Your initContainer will work across both Gateway Helm Chart and Layer7 Operator.

The [Layer7 Operator](https://github.com/caapim/layer7-operator) supports dynamically applying these to your ephemeral Gateways at runtime.

### This guide currently covers SINGLE install mode only

## Install Modes
- Single
<!-- - Dual (Future)
  - DMZ
  - Internal -->

### Pre-requisites
- A Gateway with the OTK installed (v4.6.3+)
  - If you don't have a Gateway with the OTK available you can create one using the following guides
    - [Layer7 Operator](https://github.com/CAAPIM/layer7-operator/tree/main/example/otk/single)
    - [Gateway Helm Chart](https://github.com/CAAPIM/apim-charts/tree/stable/examples/otk#quick-start)
- [Graphman Client](https://github.com/Layer7-Community/graphman-client)
  - this guide assumes that you have used npm to install the graphman-client and have configured your ~/.graphman/graphman.configuration file with a default gateway set
    ```
    {
        "gateways": {
            "default": {
                "address": "https://localhost:9443/graphman",
                "username": "admin",
                "password": "7layer",
                "rejectUnauthorized": false,
                "keyFilename": null,
                "certFilename": null,
                "passphrase": "7layer",
                "allowMutations": true 
            }
        },
        "options": {
            "log": "info",
            "policyCodeFormat": "xml",
            "keyFormat": "pem"
        }
    }
    ```

### The Examples Folder
The [examples folder](./examples) contains
- An exported customizations folder (OTK v4.6.4)
  - in single (imploded) graphman bundle format that can be used if you don't have access to a Gateway with the OTK installed
  - the same graphman bundle in expanded (exploded) format

### Guide
- [Export your customizations](#export-your-customizations)
- [Expand into file/folder format](#create-repositories)
- [Update your customizations](#create-a-gateway)
- [Next Steps](#next-steps)

### Export your customizations
This command will export the /OTK/Customizations folder from your gateway and place it into a file called otk-single.json

```
graphman export --gateway default --using folder --variables.folderPath /OTK/Customizations --output otk-single.json
```

### Expand into file/folder format
This command will expand the file that you created in the previous command into the [otk-customizations-single](./otk-customizations-single/) folder.

```
graphman explode --input ./otk-single.json --output otk-customizations-single
```

##### Note
The [otk-customizations-single folder](./otk-customizations-single/) already has clusterProperties defined. This is done so that you do not need to export additional dependencies.

### Update your customizations
Add/Update/Remove any policies that you do not wish to customize

### Next Steps
You can either publish ([Github Example](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository)) your customizations to a Git repository or convert them back to a single graphman bundle file and include this on a gateway initContainer.

InitContainers work with both the Gateway Helm Chart and Layer7 Operator. If you are using a database backed gateway you can import these changes manually using the graphman-client or automate that as part of a CI/CD pipeline.

