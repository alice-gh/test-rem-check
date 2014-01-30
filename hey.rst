====================================================
VeeWee Definitions for Creating Mageia Vagrant Boxes
====================================================

Various new and modified template definitions to create
`veewee <https://github.com/jedi4ever/veewee>`_ boxes for
`vagrant <https://github.com/mitchellh/vagrant>`_.

Please see below for instructions how to build boxes using veewee and
this definitions.


Definitions
==============

General Configuration
~~~~~~~~~~~~~~~~~~~~~~

- Based on `prior work
  <https://github.com/thatsamguy/veewee-definitions>`_ from thatsamguy.

- All definitions are minimal, including only vboxadditions, sshd and
  urpmi.

- Mageias `auto configuration feature
  <https://wiki.mageia.org/en/Auto_inst>`_ is used.

- No localization, no timezone, no key-mappings, no mouse, and no
  hardware definitions

- Clean up installations much more, move most custom package
  installation into several smaller post-install scripts.

- Installs VirtualBox Guests Additions for the installed kernel.

- Change vagrant base config to 2 CPU, 512MB RAM

- Optional install Ruby (from distribution), puppet (from
  distribution) and chef (from gems)

- Use boot-nonfree iso for network based install.


Definition List
~~~~~~~~~~~~~~~~~~~~~~

:Mageia-Cauldron-Vagrant-Server-minimal-x86_64:
  * base config: 2 CPU, 512MB RAM
  * a minimal server, including only vboxadditions, sshd and urpmi.

:Mageia-4-Vagrant-Server-minimal-x86_64:
  * base config: 2 CPU, 512MB RAM
  * a minimal server, including only vboxadditions, sshd and urpmi.

:Mageia-3-Vagrant-Server-minimal-x86_64:
  * base config: 2 CPU, 512MB RAM
  * a minimal server, including only vboxadditions, sshd and urpmi.



Building Boxes
=====================

Mind the firewall!


Installing veewee and requirements
----------------------------------------

Install prebuild native extensions (this saves from installing the
development packages)::

  urpmi ruby-nokogiri ruby-ffi ruby-posix-spawn ruby-libxml ruby-libvirt
  gem install --no-ri --no-rdoc veewee


If you want to remove these gems later use this line::

  gem list --local | grep -Pi '^[a-z]' | sed 's/ .*//' | xargs gem uninstall


Installing the box definitions
------------------------------------------

::
  git clone https://github.com/htgoebel/veewee-definitions
  cd veewee-definitions


Buidling the Boxes
---------------------------

Again: Mind the firewall on your workstation!

  for name in Mageia-3-server-x86_64-minimal Mageia-4-server-x86_64-minimal ; do
      ./build-box.sh $name
      ./test-box.sh $name
  done

  mv -i *.box /path/to/store/vagrant-boxes/


This basically does the following for each box definition::

      veewee vbox build --nogui --force $name
      veevee vbox halt $name
      VBoxManage modifyvm $name --ostype "Mageia_64"
      veewee vbox export --force $name
      veewee vbox destroy $name

      vagrant box add $name $PWD/$name.box
      vagrant init $name
      vagrant up
      vagrant ssh
      vagrant destroy
      rm Vagrantfile


..
  Local Variables:
  ispell-local-dictionary: "american"
  End:
