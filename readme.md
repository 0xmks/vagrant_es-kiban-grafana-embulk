# 概要
- Vagrant + VirtualBOX で CentOS7 上に elasticcache / kibana / grafana / embulk の動作する環境を構築。

# 使い方
- リポジトリをClone
- `vagrant up` で構築を開始

# IPアドレス、ホスト名解決について
- Vagrantfile 内で vagrant-hostsupdater によるホストマシン上のhosts書き換えを行っています。
- 必要に応じて該当箇所を書き換え、または削除します。
```
    if Vagrant.has_plugin?("vagrant-hostsupdater")
      node.vm.network "private_network", ip: "192.168.33.10"
      node.vm.hostname = "es-kibana.localdomain"
      node.hostsupdater.aliases = ["grafana.localdomain"]
    end

```

# Elasticcache
- http://es-kibana.localdomain:9200

# kibana
- http://es-kibana.localdomain:5601

# grafana
- http://es-kibana.localdomain:3000
- 初期ログイン：admin / admin

# embulk
- vagrant ssh で仮想マシンにログイン後 `/usr/local/bin/embulk` から実行可能

