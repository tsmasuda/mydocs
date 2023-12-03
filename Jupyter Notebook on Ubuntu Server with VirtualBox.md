# Jupyter NotebookをVirtualBox上のUbuntu Serverでセットアップしたときの備忘録

## 1. Jupyter Notebookをインストール

[こちらに従う](https://www.digitalocean.com/community/tutorials/how-to-set-up-jupyter-notebook-with-python-3-on-ubuntu-20-04-and-connect-via-ssh-tunneling)

## 2. ネットワークを設定

### VirtualBox側

1. Adapter 1 (NAT)のPort Forwardingを設定 ... 8888 -> 8888
2. Adapter 2 (Host-only Adapter)を有効化

### Ubuntu Server側 

> インストール時に設定しなかったため

1. **/etc/netplan/00-installer-config.yaml**を開き、enp0s8を有効化
2. `sudo netplan apply`で設定ファイルを有効化
3. `ifconfig -a`で付与されているIPアドレスを確認

## 3. Jupyter Notebookを設定

> デフォルト設定ではホスト側からアクセスすることができなかったため

1. `jupyter notebook --generate-config`で設定ファイルを生成
2. **.jupyter/jupyter_notebook_config.py**を開き、以下を末に追加
  - c.NotebookApp.allow_origin = '*'
  - c.NotebookApp.ip = '0.0.0.0'

## 4. Jupter Notebookの自動起動設定

> Serviceを利用

1. `touch /home/toshi/run_jupyter_notebook.sh`でサービスによって実行するシェルスクリプトを作成
  ```
  #!/bin/bash
  
  virtualenv (virtualenv name)
  source ~/(folder name)/(virtualenv name)/bin/activate
  jupyter notebook --no-browser
  ```
2. `sudo touch /etc/systemd/system/jupyter.service`でサービス用のファイルを作成
  ```
  [Unit]
  After=network.target
  
  [Service]
  User=toshi
  ExecStart=/home/(username)/run_jupyter_notebook.sh
  
  [Install]
  WantedBy=default.target
  ```
3. `sudo systemctl enable jupyter`でサービスを有効化し、`sudo systemctl start jupyter`で起動
