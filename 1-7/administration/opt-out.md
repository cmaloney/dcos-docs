---
post_title: Opt-Out
menu_order: 6
---
## Authentication

You can opt-out of the DC/OS authentication by disabling it for your cluster. To disable authentication, add this parameter to your [`config.yaml`][1] file during installation (note this requires using the [CLI][2] or [advanced][3] installers):

`auth_enabled: 'false'`

If you’ve already installed your cluster and you want to disable this in-place, you can go through an [upgrade][4] with the same parameter set.

## Telemetry

You can opt-out of providing anonymous data by disabling telemetry for your cluster. To disable telemetry, add this parameter to your [`config.yaml`][1] file during installation (note this requires using the [CLI][2] or [advanced][3] installers):

`telemetry_enabled: 'false'`

If you’ve already installed your cluster and want to disable this in-place, you can go through an [upgrade][4] with the same parameter set.

 [1]: /administration/installing/custom/configuration-parameters/
 [2]: /administration/installing/custom/cli/
 [3]: /administration/installing/custom/advanced/
 [4]: FIXME