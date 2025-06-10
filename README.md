# lab-cluster-app

Ansible playbooks to deploy and manage applications and platform components on the `lab-cluster`.

---

## 概要

このリポジトリは、実験用 Kubernetes クラスタ (`lab-cluster`) に、監視スタックや GPU 管理ツールなどの各種アプリケーションを宣言的にデプロイ・管理することを目的としています。
プロビジョニングには [Ansible](https://www.ansible.com/) を利用し、再現性の高い環境構築を目指します。

## 管理対象アプリケーション

現在、以下のアプリケーションを管理しています。

| アプリケーション (Role)    | 説明                                                                                                                                                               | Namespace      |
| :------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------- |
| `prometheus`               | [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) による監視スタック (Prometheus, Grafana, etc.) | `monitoring`   |
| `gpu_operator`             | [NVIDIA GPU Operator](https://github.com/NVIDIA/gpu-operator) による GPU ノードの管理・監視                                                                        | `gpu-operator` |
| _（追加したらここに追記）_ |                                                                                                                                                                    |                |

## 前提条件

この Playbook を実行するマシンには、以下のツールが必要です。

- `kubectl`: Kubernetes クラスタにアクセスできること
- `ansible-core >= 2.15`
- Ansible Collections:
  - `kubernetes.core`
  - `community.kubernetes`

以下のコマンドで必要な Ansible コレクションをインストールできます。

```bash
ansible-galaxy collection install kubernetes.core community.kubernetes
```

## ディレクトリ構成 (Directory Structure)

```
.
├── ansible.cfg         # Ansible設定ファイル
├── inventory/          # インベントリファイル
│   └── hosts.yml
├── playbook.yml        # メインのPlaybook
└── roles/
    ├── prometheus/     # kube-prometheus-stack用ロール
    └── gpu_operator/   # NVIDIA GPU Operator用ロール
```

## 使用方法 (Usage)

1.  **リポジトリをクローンします。**

    ```bash
    git clone https://github.com/gen-yuu/lab-cluster-app.git
    cd lab-cluster-app
    ```

2.  **インベントリファイルを確認・編集します。**
    `inventory/hosts.yml` が対象クラスタを指していることを確認してください。（ローカル実行の場合は `hosts: localhost` のままで OK です）

3.  **Ansible Playbook を実行します。**

    ```bash
    # ドライラン (実行内容の確認)
    ansible-playbook -i inventory/hosts.yml playbook.yml --check --diff

    # 本実行
    ansible-playbook -i inventory/hosts.yml playbook.yml
    ```

## 設定 (Configuration)

各アプリケーションの細かい設定 (Helm の values) は、それぞれのロール内にある `files/<app>-values.yml` や `templates/` で管理しています。

設定を変更した場合は、再度 Playbook を実行することでクラスタに反映されます。

<!-- ```bash
lab-cluster-app/
├── root-app.yaml              # (LEVEL 1) 全てを管理するCEO
│
├── applications/              # (LEVEL 2) 各機能グループ（部長）の定義
│   └── monitoring-stack.yaml
│   └── ml-stack.yaml          # (将来用)
│
├── apps/                      # (LEVEL 3) 個別アプリ（チームメンバー）の定義
│   ├── monitoring/
│   │   ├── prometheus-app.yaml
│   │   └── grafana-app.yaml
│   │
│   └── ml/                    # (将来用)
│       └── jupyterhub-app.yaml
│
├── charts/                    # 各アプリのHelmラッパーチャート
│   ├── prometheus/
│   │   ├── Chart.yaml
│   │   └── values.yaml
│   └── grafana/
│       ├── Chart.yaml
│       └── values.yaml
│
└── manifests/                 # Helmを使わないアプリのマニフェスト (将来用)
    └── jupyterhub/
        ├── deployment.yaml
        └── service.yaml
``` -->
