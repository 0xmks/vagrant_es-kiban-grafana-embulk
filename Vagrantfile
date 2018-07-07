# -*- mode: ruby -*-
# vi: set ft=ruby :
#
# 解析環境構 Vagrantファイル
# Elasticsearch/ kibana/ grafana/ embulk をAnsibleでインストール

## 起動後の ansible ローカル実行メモ:
# vagrant ssh 
# cd /vagrant/ansible
# ansible-playbook -i localhost, -c local site.yml --check
# ansible-playbook -i localhost, -c local site.yml
# windows でvagrant 終了時 にwinnfsd も落とす場合 → vagrant halt && taskkill /f /im winnfsd.exe

## OS判定
module OS
  def OS.windows?
    Vagrant::Util::Platform.windows?
  end
  def OS.mac?
    (/darwin/ =~ Vagrant::Util::Platform.platform) != nil
  end
  def OS.unix?
    !Vagrant::Util::Platform.platform
  end
  def OS.linux?
    OS.unix? and not OS.mac?
  end
end

## Vagrantプラグイン インストール
if OS.windows?
  required_plugins = %w(vagrant-winnfsd vagrant-hostsupdater vagrant-cachier)
elsif OS.mac?
  required_plugins = %w(vagrant-hostsupdater vagrant-cachier)
end

plugins_to_install = required_plugins.select { |plugin| not Vagrant.has_plugin? plugin }
if not plugins_to_install.empty?
  puts "Installing plugins: #{plugins_to_install.join(' ')}"
  if system "vagrant plugin install #{plugins_to_install.join(' ')}"
    exec "vagrant #{ARGV.join(' ')}"
  else
    abort "Installation of one or more plugins has failed. Aborting."
  end
end

# plugin 無効化
if OS.mac?
  config.winnfsd = false
end

## VM起動
Vagrant.configure("2") do |config|

  config.vm.provider "virtualbox" do |v|
    v.cpus = 2
    v.memory = 2048
  end

  # bento Pre-built Boxes ( https://app.vagrantup.com/bento)
  config.vm.box = "bento/centos-7"

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end

  config.vm.define "node01.vm" do |node|

    if Vagrant.has_plugin?("vagrant-hostsupdater")
      node.vm.network "private_network", ip: "192.168.33.10"
      node.vm.hostname = "es-kibana.localdomain"
      node.hostsupdater.aliases = ["grafana.localdomain"]
    end

    # ホスト/ゲスト ディレクトリ共有
    # node.vm.synced_folder ".","/vagrant", type: "nfs"

    # ansible_local でのセットアップ
    node.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "ansible/build.yml"
    end

  end
end


