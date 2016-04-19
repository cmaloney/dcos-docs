---
UID: 56f9844a53401
post_title: Updating the CLI
post_excerpt: ""
layout: page
published: true
menu_order: 26
page_options_require_authentication: false
page_options_show_link_unauthenticated: false
hide_from_navigation: false
hide_from_related: false
---
You can update the DCOS CLI to the latest version or downgrade to an older version.

# <a name="upgrade"></a>Upgrade the CLI

You can upgrade an existing DCOS CLI installation to the latest build.

1.  From your DCOS CLI installation directory, enter this command to update the DCOS CLI:
    
        $ sudo pip install -U dcoscli
        

# <a name="downgrade"></a>Downgrade the CLI

You can downgrade an existing DCOS CLI installation to an older version.

**Tip:** Downgrading is necessary if you are running an older version of DCOS and want to reinstall the DCOS CLI.

1.  Delete your DCOS CLI installation directories:
    
        $ sudo rm -rf dcos && rm -rf ~/.dcos
        

2.  Install the legacy version of the DCOS CLI, where <public-master-ip> is the public IP of your master node:
    
        mkdir -p dcos && cd dcos && 
          curl -O https://downloads.dcos.io/dcos-cli/install-legacy.sh && 
          bash ./install-legacy.sh . <public-master-ip> && 
          source ./bin/env-setup