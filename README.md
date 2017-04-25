# Running caveats

You may have to have administrator rights to enable binding to ports below
1024.

# Spinning up multiple instances

An important point to note is that you can spin up multiple instances
with a loop:

```ruby
  (1..3).each do |i|
    Vm.new(
      config: config,
      box: 'ubuntu/xenial64',
      name: 'flocker-node-%02d' % i,
      ip: "172.20.54.#{i+100}",
      provision_shell_paths: [
        'flocker-node.provision.sh',
      ]
    ).up()
  end
```

# Adding your own ssh public key

```ruby

  ssh_pubkey = File.readlines(
    File.expand_path('~/.ssh/id_rsa.pub')
  ).first.strip

  config.vm.provision "shell", inline: <<-SHELL
    set -e

    # See https://bugs.launchpad.net/cloud-images/+bug/1621393
    ln -svf /run/resolvconf/interface/enp0s3.dhclient /etc/resolv.conf

    echo '#{ssh_pubkey}' | tee -a /home/ubuntu/.ssh/authorized_keys

    apt-get update
    apt-get install -y --no-install-recommends python
    set +e
  SHELL
```
