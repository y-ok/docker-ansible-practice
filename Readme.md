# フォルダ構成

- controller
  - config ･･･ ansibleの設定ファイル関連
  - ssh ･･･ ゲスト環境にアクセスするためのsshキー
- nodes
  - centos7 ･･･ ゲスト環境のDockerfile
- playbooks ･･･ controllerから実行するplaybookの保存

# コンテナの構成

- centos7 ･･･ ゲスト環境
- controller ･･･ ansible実行環境

`controller`コンテナでansibleコマンドを実行し、各ゲスト環境を更新する

# 起動方法

以下のコマンドでゲスト環境＋ansible環境が起動されます
```
> docker-compose up -d
```

# ansible環境を利用する

```
> docker exec -it controller /bin/bash
```

# 疎通確認

サンプルコード

```
> ansible-playbook -i hosts book.yml
```
実行結果

```

PLAY [all] **************************************************************************************************************************************************************************************************
TASK [Gathering Facts] **************************************************************************************************************************************************************************************ok: [ubuntu20]
ok: [centos7]

TASK [debug] ************************************************************************************************************************************************************************************************ok: [centos7] => {
    "msg": "hello from centos7 !!"
}
ok: [ubuntu20] => {
    "msg": "hello from ubuntu20 !!"
}

PLAY RECAP **************************************************************************************************************************************************************************************************centos7                    : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
ubuntu20                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```


# playbookの実行

playbooksをansible-controllerコンテナの`/workspace`にマウントしているので、
playbooksフォルダにplaybookを作成し試すことができます。

```
> ansible-playbook all /playbooks/xxxx.yml

```

# ゲスト環境が増えた場合

1. docker-compose.ymlに新たのコンテナを追加します。
2. playbooksフォルダのhostsに対象のサービス名を追加します。