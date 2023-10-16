## Agents Thruk Plugin

Agent configuration for SNClient+ agents in Naemon.

![Thruk Agents Plugin](preview.png "Thruk Agents Plugin")

## Installation

Assuming you are using OMD (omdistro.org).
All steps have to be done as site user:

    %> cd etc/thruk/plugins-enabled/
    %> git clone https://github.com/sni/thruk-plugin-agents.git agents
    %> omd reload apache

You now have a new menu item under System -> Agents.

Create a example configuration file:

`~/etc/thruk/thruk_local.d/agents.conf`.

For example:

    <Component Thruk::Agents>
      <snclient>
        # set a default password macro, ex.: $USER5$
        default_password = $USER5$

        # add extra options to check_nsc_web
        check_nsc_web_extra_options = "-k -t 35"

        # override check interval
        check_interval = 1

        # override inventory interval
        inventory_interval = 60

        # settings for check_network
        <disable network>
          enabled != true
          name    !~ ^(en|br)
        </disable>

        # settings for check_drivesize
        <disable drivesize>
          fstype  !~ ^(|tracefs|securityfs|debugfs|configfs|pstorefs|fusectl|cgroup2fs|bpf|efivarfs|sysfs|fuseblk|rpc_pipefs|nsfs|ramfs|binfmt_misc|proc|nfs|devpts|mqueue|hugetlbfs)$
          drive   !~ ^(/run/|/dev|/boot/efi)
          mounted = 1
        </disable>

        # include services in discovery
        <service>
          # service name (available placeholder: %s - service name)
          name  = service %s
          service = snclient
          service = apache2
          service = postfix
          service = ssh
          service = exim4
          service = mariadb
          service = ntp
          service = squid

          # restrict to specific hosts (regular expression)
          #host = ANY
        </service>

        <proc>
          # service name (available placeholder: %u - user | %e - executable)
          name  = ssh controlmaster %u
          match = /usr/bin/ssh.*ControlMaster=yes
          user  = mon
          # restrict to specific hosts (regular expression)
          #host = ANY
        </proc>
      </snclient>
    </Component>

You have to reload the apache to activate changes
from the `thruk_local.d` folder.