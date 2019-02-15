Redfish request processor
=========================

A simple role to send HTTP requests to a redfish enabled BMC controller.

Requirements
------------

None.

Role Variables
--------------

A set of host/group variables, which define the actual BMC host is expected:

```yml
# The IP or hostname of the BMC controller for a host
bmc_host:
# The BMC user login with sufficient privileges to perform tasks
bmc_admin:
# The BMC user password
bmc_admin_pass:
```

A set of request variables to be passed to the role:

```yml
# The name of a request to be performed
item.name:
# The variable part of the actual URL contents for a request
# No trailing slash!
item.url:
# The method for a request
item.method: GET
# The payload for a request as a dict. Applies only to POST, PUT or PATCH
# methods.
item.body: None
```

Dependencies
------------

None.

Example Playbook
----------------

All tasks are delegated to localhost. Tasks are tagged `always` to enable
tagging, when using dynamic includes.

```yml
- hosts: baremetal
  gather_facts: no

  tasks:

    - name: Get BMC details
      include_role:
        name: redfish
      loop:
        - name: Get BMC details
          url: Managers/1

    - name: Configure BIOS
      include_role:
        name: redfish
      loop:
        - name: Reset BIOS to factory defaults
          url: Systems/1/BIOS/Settings
          method: PATCH
          body:
            Attributes:
              RestoreManufacturingDefaults: 'Yes'
        - name: Apply BIOS settings
          url: Systems/1/BIOS/Settings
          method: PATCH
          body: "{{ hw_settings_bios }}"
      tags: bios

    - name: Reboot
      include_role:
        name: redfish
      vars:
        item:
          name: Force restart
          url: Systems/1/Actions/ComputerSystem.Reset
          method: POST
          body:
            ResetType: ForceRestart
      tags: reboot, never
```

License
-------

GPLv3

Author Information
------------------

Grzegorz Adamiak
