---
post_title: Opt-Out
layout: page
published: true
---

## Authentication

You can opt-out of the DC/OS authentication by disabling it for your cluster. To disable authentication, add this parameter to your [`config.yaml`][4] file during installation (note this requires using the [CLI][1] or [advanced][2] installers):

```
auth_enabled: 'false'
```

If you’ve already installed your cluster and you want to disable this in-place, you can go through an [upgrade][3] with the same parameter set.

## Telemetry

You can opt-out of providing anonymous data by disabling telemetry for your cluster. To disable telemetry, add this parameter to your [`config.yaml`][4] file during installation (note this requires using the [CLI][1] or [advanced][2] installers):

```
telemetry_enabled: 'false'
```

If you’ve already installed your cluster and want to disable this in-place, you can go through an [upgrade][3] with the same parameter set.

[1]: /administration/installing/custom/cli/
[2]: /administration/installing/custom/advanced/
[3]: FIXME
[4]: /administration/installing/custom/configuration-parameters/
