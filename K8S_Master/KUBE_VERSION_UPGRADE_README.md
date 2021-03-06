# kubeadm 클러스터 업그레이드 가이드

## 구성 요소 및 버전
* kubeadm, kubelet, kubectl

## Prerequisites
* upgrade 할 kubeadm version 선택
	```bash
	yum list --showduplicates kubeadm --disableexcludes=kubernetes
	```
* 하나의 MINOR 버전에서 다음 MINOR 버전으로, 또는 동일한 MINOR의 PATCH 버전 사이에서만 업그레이드할 수 있다. 
* 즉, 업그레이드할 때 MINOR 버전을 건너 뛸 수 없다. 예를 들어, 1.y에서 1.y+1로 업그레이드할 수 있지만, 1.y에서 1.y+2로 업그레이드할 수는 없다.
* ex) 1.15 버전에서 1.17 버전으로 한번에 업그레이드는 불가능 하다. 1.15 -> 1.16 -> 1.17 스텝을 진행 해야 한다.

## Steps
0. [master upgrade](https://github.com/tmax-cloud/hypercloud-install-guide/blob/master/K8S_Master/KUBE_VERSION_UPGRADE_README.md#step0-kubernetes-master-upgrade)
1. [node upgrade](https://github.com/tmax-cloud/hypercloud-install-guide/blob/master/K8S_Master/KUBE_VERSION_UPGRADE_README.md#step1-kubernetes-node-upgrade)

## Step0. kubernetes master upgrade
* master에서 kubeadm을 upgrade 한다.
	```bash
	yum install -y kubeadm-설치버전 --disableexcludes=kubernetes
	
	ex) yum install -y kubeadm-1.16.0-0 --disableexcludes=kubernetes
	
	ex) yum install -y kubeadm-1.17.6-0 --disableexcludes=kubernetes
	```
* 버전 확인
	```bash
	kubeadm version
	```
* 업그레이드 plan 변경
	```bash
	sudo kubeadm upgrade plan 
	```
   * 업그레이드 시 kubeadm config 변경이 필요할 경우
	```bash
	sudo kubeadm upgrade plan --config=kubeadm_config.yaml
	```
	```bash
	[upgrade/config] Making sure the configuration is correct:
	[upgrade/config] Reading configuration from the cluster...
	[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
	[preflight] Running pre-flight checks.
	[upgrade] Running cluster health checks
	[upgrade] Fetching available versions to upgrade to
	[upgrade/versions] Cluster version: v1.15.3
	[upgrade/versions] kubeadm version: v1.16.0
	[upgrade/versions] Latest stable version: v1.16.0
	[upgrade/versions] Latest version in the v1.15 series: v1.16.0

	Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
	COMPONENT   CURRENT             AVAILABLE
	Kubelet     1 x v1.15.3   v1.16.0

	Upgrade to the latest version in the v1.17 series:

	COMPONENT            CURRENT   AVAILABLE
	API Server           v1.15.3   v1.16.0
	Controller Manager   v1.15.3   v1.16.0
	Scheduler            v1.15.3   v1.16.0
	Kube Proxy           v1.15.3   v1.16.0
	CoreDNS              1.6.5     1.6.7
	Etcd                 3.4.3     3.4.3-0

	You can now apply the upgrade by executing the following command:

    	kubeadm upgrade apply v1.16.0

	_____________________________________________________________________
	```
* 업그레이드 실행
	```bash
	(1.15.x-> 1.16.x) sudo kubeadm upgrade apply v1.16.x
	
	(1.16.x-> 1.17.x) sudo kubeadm upgrade apply v1.17.x
	```
	```bash
	[upgrade/config] Making sure the configuration is correct:
	[upgrade/config] Reading configuration from the cluster...
	[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
	[preflight] Running pre-flight checks.
	[upgrade] Running cluster health checks
	[upgrade/version] You have chosen to change the cluster version to "v1.16.0"
	[upgrade/versions] Cluster version: v1.15.3
	[upgrade/versions] kubeadm version: v1.16.0
	[upgrade/confirm] Are you sure you want to proceed with the upgrade? [y/N]: y
	[upgrade/prepull] Will prepull images for components [kube-apiserver kube-controller-manager kube-scheduler etcd]
	[upgrade/prepull] Prepulling image for component etcd.
	[upgrade/prepull] Prepulling image for component kube-apiserver.
	[upgrade/prepull] Prepulling image for component kube-controller-manager.
	[upgrade/prepull] Prepulling image for component kube-scheduler.
	[apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-controller-manager
	[apiclient] Found 0 Pods for label selector k8s-app=upgrade-prepull-etcd
	[apiclient] Found 0 Pods for label selector k8s-app=upgrade-prepull-kube-scheduler
	[apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-apiserver
	[apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-etcd
	[apiclient] Found 1 Pods for label selector k8s-app=upgrade-prepull-kube-scheduler
	[upgrade/prepull] Prepulled image for component etcd.
	[upgrade/prepull] Prepulled image for component kube-apiserver.
	[upgrade/prepull] Prepulled image for component kube-controller-manager.
	[upgrade/prepull] Prepulled image for component kube-scheduler.
	[upgrade/prepull] Successfully prepulled the images for all the control plane components
	[upgrade/apply] Upgrading your Static Pod-hosted control plane to version "v1.16.0"...
					
						......
						
	[apiclient] Found 1 Pods for label selector component=kube-scheduler
	[upgrade/staticpods] Component "kube-scheduler" upgraded successfully!
	[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
	[kubelet] Creating a ConfigMap "kubelet-config-1.16" in namespace kube-system with the configuration for the kubelets in the cluster
	[kubelet-start] Downloading configuration for the kubelet from the "kubelet-config-1.16" ConfigMap in the kube-system namespace
	[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
	[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
	[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
	[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
	[addons] Applied essential addon: CoreDNS
	[addons] Applied essential addon: kube-proxy

	[upgrade/successful] SUCCESS! Your cluster was upgraded to "v1.16.0". Enjoy!

	[upgrade/kubelet] Now that your control plane is upgraded, please proceed with upgrading your kubelets if you haven't already done so.
	```
* 적용된 cordon을 해제한다.
	```bash
	kubectl uncordon <cp-node-name>
	
	ex) kubectl uncordon k8s-master
	```
* master와 node에 kubelet 및 kubectl을 업그레이드한다.
	```bash
	(1.15.x-> 1.16.x) yum install -y kubelet-1.16.x-0 kubectl-1.16.x-0 --disableexcludes=kubernetes
	
	(1.16.x-> 1.17.x) yum install -y kubelet-1.17.x-0 kubectl-1.17.x-0 --disableexcludes=kubernetes
	```
* kubelet을 재시작 한다.
	```bash
	sudo systemctl daemon-reload
	sudo systemctl restart kubelet
	```		
* 비고 : 
    * master 다중화 구성 클러스터 업그레이드 시에는 다음과 같은 명령어를 실행한다.
	```bash
	sudo kubeadm upgrade node
	sudo kubeadm upgrade apply
	``` 	
    * 1.16.x -> 1.17.x로 업그레이드시 버전에 맞추어 위에 작업을 실행한다.
	
## Step1. kubernetes node upgrade
* 워커 노드의 업그레이드 절차는 워크로드를 실행하는 데 필요한 최소 용량을 보장하면서, 한 번에 하나의 노드 또는 한 번에 몇 개의 노드로 실행해야 한다.
* 모든 worker node에서 kubeadm을 업그레이드한다.
	```bash
	yum install -y kubeadm-설치버전 --disableexcludes=kubernetes
	
	ex) (1.15.x-> 1.16.x) yum install -y kubeadm-1.16.x-0 --disableexcludes=kubernetes
	
	ex) (1.15.x-> 1.16.x) yum install -y kubeadm-1.17.x-0 --disableexcludes=kubernetes
	```
* 스케줄 불가능(unschedulable)으로 표시하고 워크로드를 축출하여 유지 보수할 노드를 준비한다.
	```bash
	kubectl drain <node name> --ignore-daemonsets
	```
* kubelet 구성 업그레이드
	```bash
	sudo kubeadm upgrade node
	```
* kubelet과 kubectl 업그레이드
	```bash
	(1.15.x-> 1.16.x) yum install -y kubelet-1.16.x-0 kubectl-1.16.x-0 --disableexcludes=kubernetes
	
	(1.16.x-> 1.17.x) yum install -y kubelet-1.17.x-0 kubectl-1.17.x-0 --disableexcludes=kubernetes
	
	sudo systemctl daemon-reload
	sudo systemctl restart kubelet	
	```
* 적용된 cordon을 해제한다.
	```bash
	kubectl uncordon <cp-node-name>
	
	ex) kubectl uncordon k8s-node
	```
* 비고 : 	
    * 1.16.x -> 1.17.x로 업그레이드시 버전에 맞추어 위에 작업을 실행한다.
