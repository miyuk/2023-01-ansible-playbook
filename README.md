# Software Design 2023 年 1 月号 ansible-playbook

このレポジトリは`Software Design 2023年1月号`の`Ansible現場を支えるPlaybook`で紹介されている Playbook のサンプルが配置されています。

## 概要

Netbox を利用した IP アドレスの自動払い出しを行う Ansible Playbook

## ディレクトリの説明

```bash
.
├── README.md  # 本ファイル
├── assign_address.yml  # Playbook本体
├── collections
│   └── requirements.yml  # Playbook実行に必要なAnsible Collection一覧を記載
├── inventory
│   ├── group_vars
│   │   └── all.yml  # Netbox接続パラメータを記載
│   └── hosts.yml
├── requirements.txt  # Playbookの実行に必要なPythonモジュール一覧を記載
└── vars
    └── vm_parameter.yml  # 自動払い出しを行う対象についてのパラメータを記載
```

## Playbook 実行前準備

1. Netbox を事前に構築して、パラメータを`inventory/group_vars/all.yml`に記載する

   構築方法は[netbox-docker レポジトリ](https://github.com/netbox-community/netbox-docker#quickstart)を参照

   各パラメータは以下の通り

   ```yaml
   ---
   netbox_url: "http://172.20.0.70:8000" # NetboxのURL
   netbox_token: "0123456789abcdef0123456789abcdef01234567" # Netboxにアクセスするためのトークン(docker作成すると自動で発行される)
   netbox_validate_certs: false # Netboxへのアクセス時に証明書チェックを行うか(httpアクセスの場合無視される)
   ```

2. Python モジュールインストール

   ```bash
   pip install -r requirements.txt
   ```

3. Ansible Collection インストール

   ```bash
   ansible-galaxy collection install -r collections/requirements.yml
   ```

4. IP アドレスを払い出すためのパラメータを`vars/vm_parameter.yml`に記載

   各パラメータは以下の通り

   ```yaml
   ---
   __hostname: "TESTSERVER" # Netboxに登録するホスト名
   __cluster: "test-cluster" # ホストを登録する仮想基盤クラスタ
   __nic: "eth0" # IPアドレスを紐づけるNIC名
   __prefix: "192.168.0.0/24" # IPアドレスを払い出し元プレフィックス名
   ```

## Playbook 実行

下記コマンドを実行

```bash
ansible-playbook -i inventory assign_address.yml
```
