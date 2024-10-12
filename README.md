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

### 第三題 (沒有完成)
1. 因為不熟悉metalLB，故安裝時使用[官方文檔](https://metallb.universe.tf/installation/)的方式進行操作。
2. 執行以下進行安裝
   ```bash
    kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.8/config/manifests/metallb-native.yaml
3. 執行以下抓取internal ip 來設置L2設定所需要的ip pool range
   ```bash
    kubectl get nodes -o wide
4. 建立[IPAddressPool](./metallb/IPAddressPool.yaml)後執行apply進行套用
   ```bash
    kubectl apply -f ./metallb/IPAddressPool.yaml
5. 建立[L2Advertisement](./metallb/L2Advertisement.yaml)後執行apply進行套用
   ```bash
    kubectl apply -f ./metallb/L2Advertisement.yaml
6. 雖然都有部署成功，有嘗試用nginx進行嘗試，或是用prometheus service改為LoadBalancer進行嘗試，但都無法成功用external ip進行連線
  ![pod狀態圖](./image/metalLB.png)
7. 關於只部署在infra node上，因為不熟悉從manifast部署怎麼會入設定檔，故改用準備[patch file](./metallb/metallb-patch.yaml)後執行apply進行套用
   ```bash
    kubectl patch daemonset speaker -n metallb-system --patch-file ./metallb/metallb-patch.yaml

### 第四題
1. 因為沒有從0建置Prometheus的經驗，所以安裝Prometheus是用helm已經包裝好的版本進行安裝。安裝前因為需要把特定pod建立在某個node，所以要寫[values.yaml](./prometheus/values/prometheus-values.yaml)進行設定
2. 執行以下進行helm安裝並套用values的設定
   ```bash
    helm install prometheus prometheus-community/prometheus -f prometheus/values/prometheus-values.yaml
3. 安裝完後可以執行下可以確定只能部署在infra上面的pod是否只有在infra node上
  ![node label](./image/node-label.png)
  ![Prometheus installation](./image/prometheus-installation.png)
   ```bash
    kubectl get nodes -o custom-columns='NAME:.metadata.name,NODE_TYPE:.metadata.labels.node-type'
    kubectl get pods -n default -o wide
