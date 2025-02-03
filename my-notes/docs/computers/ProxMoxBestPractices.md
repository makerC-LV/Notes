
From Youtube: https://www.youtube.com/watch?v=VAJWUZ3sTSI

Enable iommu

#### Proxmox helper scripts:
https://community-scripts.github.io/ProxmoxVE/scripts<br>
[Post pve install script](https://community-scripts.github.io/ProxmoxVE/scripts?id=post-pve-install) Disables enterprise repo, enables no-subscription repo, disables high-availability, etc.

## Remove enterprise repo, add no-subscription repo

[Reddit link](https://www.reddit.com/r/Proxmox/comments/v7g8kc/did_i_turn_off_enterprise_repo_correctly/)
Summary:In GUI, select the node, There is a detail entry for `Repositories`. To remove enterprise repos, select and click `Disable`. To add no-subscription repo, click `Add`, and in the dialog that appears, select `No-subscription` from the dropdown.
[Screenshot](./disable-enterprise-repo.png)
