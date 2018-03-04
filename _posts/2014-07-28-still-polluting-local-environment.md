---
layout: post
title: Are you still polluting your local environment?
description: this post explains why you should be using Vagrant for development, and forget about your local environment
---

I still remember when doing the environment setup was a nightmare, and you had to spend about two days fixing stuff until you got everything up and running. It was horrible, yes. And at some point, It all decided to crash again, so you had to follow again the wiki (if any) to make everything work properly. Uh, I still remember that!

I also remember the *it works on my machine* thing. All developers had different environments, different versions, operating systems... So it was very likely that something didn't work on someone's machine, but the same code worked perfectly for other guys. Terrible!

But luckily, everything computer-related evolves quickly, so now we have better alternatives to that, allowing us to be more productive in less time <i class="em em-smile"></i>. Say hello to virtualisation!

Last time I suffered a problem with my computer (about nine months ago), I had to uninstall some of the packages, install them again... and pray. Everything started to fail, and I didn't want to do the setup again, because I already knew it implies about one day of work, so I decided to find an alternative solution; something that actually provides me with a reproducible way of setting up the environment. Obviously, virtualisation is the answer. And with current hardware, we can't say *it's slow* anymore (you could say that 5 years ago or so)!

Please welcome your answer, ***Vagrant***! With it, you can *create and configure lightweight, reproducible, and portable development environments*. Sounds perfect, and actually it is <i class="em em-smile"></i>.

## How can I install it?

Just click [here](https://www.vagrantup.com/downloads.html), download the version for your operating system, follow some instructions and you're done. You'll also need to install the provider, but we'll explain that in a bit.

## How can I configure it?

You just need a single file in the root of the project, called *Vagrantfile*. This file holds the configuration for the virtual machine and the provisioning for that machine. Let's see an example:

{% highlight ruby startinline %}
Vagrant.configure("2") do |config|
    config.vm.provider :virtualbox do |v|
        v.name = "my-project"
        v.customize ["modifyvm", :id, "--memory", 1024]
    end
    config.vm.box_url = "https://vagrantcloud.com/ubuntu/..."

    config.vm.network :private_network, ip: "192.168.1.1"

    config.vm.provision "ansible" do |ansible|
        ansible.playbook = "ansible/playbook.yml"
    end

	config.vm.synced_folder
		"./",
		"/var/www/my-project",
		id: "vagrant-root",
		:nfs => true
end
{% endhighlight %}

Basically, we're saying: *I want an Ubuntu Trusty 64 with VirtualBox as a provider, 1GB memory, and I want to be able to access it via 192.168.1.1, with the root of the project as synced folder (using NFS), and my machine is provisioned by Ansible*.

So you also need to configure your *provider* and your *provisioner*:

* Provider: basically, it's who is going to serve the machine (usually VirtualBox or VMWare). It actually needs no configuration, just installing it (you'll actually spend more time downloading here than actually doing stuff <i class="em em-smile"></i>). You can see all the available providers [here](https://docs.vagrantup.com/v2/providers/index.html).

* Provisioner: in order to install software on your machine, you need to tell Vagrant who will do that. If you're old-fashioned, you can just use a shell script; if you're better than that, you should probably take a look at ***Ansible***, ***Chef*** or ***Puppet*** (I prefer the first one... easy and always works!). You can see all the available provisioners [here](https://docs.vagrantup.com/v2/provisioning/index.html).

## What is a synced folder?

It is basically the folder you want to share between your local environment and the virtual machine. Withe the configuration we've just shown, as soon as you change a file using your favourite IDE or text editor, It'll be copied to the */var/www/my-project* folder on the virtual machine, via NFS.

## What should I do in the provisioning part?

Well, you can do whatever you want: install packages, setup the environment... Let's see a small example (not a best practice) using Ansible:

{% highlight yaml startinline %}
- name: apt update
  apt: update_cache=yes

- name: system base packages
  apt: pkg={{ item }} state=latest
  with_items:
    - curl
    - wget
    - python-software-properties
    - make

- name: system extra packages
  apt: pkg={{ item }} state=latest
  with_items:
  	- git
  	- vim

- name: install apache
  apt: pkg=apache2 state=latest

- name: install apache site
  template: src=vhost.conf.tpl dest=/etc/apache2/sites-available/my-project.conf

- name: enable new site
  shell: a2ensite my-project.conf
{% endhighlight %}

We're basically installing some common system packages, and then ***Apache***, and also configured the VHost. Easy! But that's a simple example, feel free to use another provisioner if you don't know Ansible (the easiest one is probably Shell provisioner).

## Next steps...

Now that we've installed everyting and configured your project and the provisioner, you just need to type ```vagrant up``` and you're done! Awesome <i class="em em---1"></i>! If you change the provisioning (lets say you want to install new software or you upgrade a package) just type ```vagrant provision```.

If you're tired of the machine, type ```vagrant destroy``` <i class="em em-smile"></i>

Still not convinced? That's probably because I haven't explained it the right way, so go and read some documentation. You won't regret!
