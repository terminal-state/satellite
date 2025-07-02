# idm_server Ansible Role

This role installs and configures Red Hat Identity Management (IdM / FreeIPA) servers, supporting both **master** and **replica** modes, and is designed for use with Red Hat Satellite 6.x or 7.x.  

It also supports optional integrated DNS configuration.

---

## Features

✅ Installs IdM server or replica based on hostname pattern  
✅ Handles DNS server configuration via Satellite parameters  
✅ Idempotent — safe to re-run  
✅ Supports AlmaLinux / RHEL 8 module streams  
✅ Parameters fully driven by Satellite host group parameters

---

## How to use with Satellite

1. **Import the role into Satellite**  
   - Package this role as a tarball and upload it under  
     `Configure → Ansible Roles → Import`  
   - or place it in `/etc/ansible/roles/` on your Capsule and re-synchronize roles

2. **Assign the role**  
   - Go to `Configure → Host Groups → Your IDM Host Group → Ansible Roles`  
   - Attach the `idm_server` role

3. **Set host group parameters**  

Add these parameters to your Satellite host group:  

| Parameter                | Example                           |
|--------------------------|-----------------------------------|
| `idm_domain`             | `example.com`                     |
| `idm_realm`              | `EXAMPLE.COM`                     |
| `idm_ds_password`        | `DirectoryManagerSecret`          |
| `idm_admin_password`     | `AdminSecret`                     |
| `idm_setup_dns`          | `true`                            |
| `idm_forwarders`         | `8.8.8.8,1.1.1.1`                 |
| `idm_allow_zone_overlap` | `false`                           |
| `idm_auto_reverse`       | `true`                            |
| `idm_no_host_dns`        | `true`                            |
| `idm_ntp_servers`        | `ntp.example.com`                 |
| `idm_primary_server`     | `idm01.example.com`               |

> Use Satellite parameter *type* `boolean` for true/false values to avoid string parsing problems.  

> Use the *hidden* option for secure items like `idm_admin_password`.  

4. **Configure Ansible for the host**  
   - Ensure the host has `Ansible` enabled under its Host settings  
   - Confirm the Capsule can run Ansible jobs for the content view/lifecycle environment

5. **Trigger the play**  
   - Run a remote execution job  
   - or wait for the next Puppet/Ansible pull  
   - Satellite will apply the role, using the parameters above  

---

## How this role works

✅ Detects master vs replica based on your hostname (by default, any hostname containing `idm01` is treated as master)  
✅ Enables the correct IdM module (`idm:DL1`) on RHEL/AlmaLinux 8  
✅ Installs required IdM packages  
✅ Configures the server with DNS and chrony  
✅ Idempotent — will skip re-installing if `/etc/ipa/default.conf` is present  
✅ Uses Satellite group parameters to stay flexible

---

## Host naming convention

This role uses this pattern to choose server type:  

- `idm0?1.*` → master  
- everything else → replica  

You can adapt this in `tasks/main.yml` with your own regex if needed.

---

## Notes

- Make sure your Satellite Capsule has the **community.general** collection installed if you use modules like `community.general.timezone`.  
- You can install it on the Capsule:  
  ```bash
  ansible-galaxy collection install -p /usr/share/ansible/collections community.general
