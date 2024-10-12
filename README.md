### 第一題以及第二題
1. kubectl 和 Docker 原本就已经有了，因此需要安装 KIND。
   ```bash
    go install sigs.k8s.io/kind@v0.24.0
    chmod +x ./kind
    sudo mv ./kind /usr/local/bin/kind
2. 建立yaml: [Config](./kubernetes/cluster-config.yaml)。並且用Label標示出worker的功用，及infra以及application，以方便用nodeSelector決定pod要部署在哪個node。
3. 啟動cluster。
  ![啟用圖](./image/create-cluster.png)
   ```bash
    kind create cluster --config ./kubernetes/cluster-config.yaml