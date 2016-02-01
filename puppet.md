[Beginner's Guide to Modules](https://docs.puppetlabs.com/guides/module_guides/bgtm.html)
---


default resource type
---
If a resource expression includes a resource body whose title is the special value default, Puppet won’t create a new resource named “default.”

Instead, every other resource in that expression will use attribute values from the default body if it doesn’t have an explicit value for one of those attributes.
```ruby
file {
  default:
    ensure => file,
    owner  => "root",
    group  => "wheel",
    mode   => "0600",
  ;
  ['ssh_host_dsa_key', 'ssh_host_key', 'ssh_host_rsa_key']:
    # use all defaults
  ;
  ['ssh_config', 'ssh_host_dsa_key.pub', 'ssh_host_key.pub', 'ssh_host_rsa_key.pub', 'sshd_config']:
    # override mode
    mode => "0644",
  ;
}
```
