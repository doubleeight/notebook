
<!DOCTYPE html>
<html lang="zh-cn">
<body>
<div id="topics">
	<div class = "post">
		<h1 class = "postTitle">
			<a id="cb_post_title_url" class="postTitle2" href="https://www.cnblogs.com/RainingNight/p/using-kubeadm-to-create-a-cluster-1-13.html">使用Kubeadm(1.13+)快速搭建Kubernetes集群</a>
		</h1>
		<div class="clear"></div>
		<div class="postBody">
			<div id="cnblogs_post_body" class="blogpost-body cnblogs-markdown"><p>Kubeadm是管理集群生命周期的重要工具，从创建到配置再到升级，Kubeadm处理现有硬件上的生产集群的引导，并以最佳实践方式配置核心Kubernetes组件，以便为新节点提供安全而简单的连接流程并支持轻松升级。随着Kubernetes 1.13 的发布，现在Kubeadm正式成为GA。</p>
<h2 id="准备">准备</h2>
<p>首先准备2台虚拟机（CPU最少2核），我是使用Hyper-V创建的2台Ubuntu18.04虚拟机，IP和机器名如下：</p>
<p>172.17.20.210 master</p>
<p>172.17.20.211 node1</p>
<h3 id="禁用swap">禁用Swap</h3>
<p>Kubernetes 1.8开始要求必须禁用Swap，如果不关闭，默认配置下kubelet将无法启动。</p>
<p>编辑<code>/etc/fstab</code>文件：</p>
<pre class="bash"><code>sudo vim /etc/fstab

UUID=8be04efd-f7c5-11e8-be8b-00155d000500 / ext4 defaults 0 0
UUID=C0E3-6A72 /boot/efi vfat defaults 0 0
#/swap.img      none    swap    sw      0       0</code></pre>
<p>如上，将<code>/swap.img</code>所在的行注释掉，然后运行：</p>
<pre class="bash"><code>sudo swapoff -a</code></pre>
<h3 id="可选dns配置">(可选)DNS配置</h3>
<p>在Ubuntu18.04+版本中，DNS由<code>systemd</code>全面接管，接口监听在<code>127.0.0.53:53</code>，配置文件在<code>/etc/systemd/resolved.conf</code>中。</p>
<p>有时候会导致无法解析域名的问题，可使用如下2种方式来解决：</p>
<p>1.最简单的就是关闭systemd-resolvd服务</p>
<pre class="bash"><code>sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved</code></pre>
<p>然后手动修改<code>/etc/resolv.conf</code>文件就可以了。</p>
<p>2.更加推荐的做法是修改systemd-resolv的设置：</p>
<pre class="bash"><code>sudo vim /etc/systemd/resolved.conf

# 修改为如下
[Resolve]
DNS=1.1.1.1 1.0.0.1
#FallbackDNS=
#Domains=
LLMNR=no
#MulticastDNS=no
#DNSSEC=no
#Cache=yes
#DNSStubListener=yes</code></pre>
<p><strong>DNS=</strong>设置的是域名解析服务器的IP地址，这里分别设为1.1.1.1和1.0.0.1<br />
<strong>LLMNR=</strong>设置的是禁止运行LLMNR(Link-Local Multicast Name Resolution)，否则systemd-resolve会监听5535端口。</p>
<h3 id="安装docker">安装Docker</h3>
<p>Kubernetes从1.6开始使用CRI(Container Runtime Interface)容器运行时接口。默认的容器运行时仍然是Docker，是使用kubelet中内置<em>dockershim CRI</em>来实现的。</p>
<p>Docker的安装可以参考之前的博客：<a href="http://www.cnblogs.com/RainingNight/p/first-docker-note.html#安装">Docker初体验</a>。</p>
<blockquote>
<p>需要注意的是，Kubernetes 1.13已经针对Docker的1.11.1, 1.12.1, 1.13.1, 17.03, 17.06, 17.09, 18.06等版本做了验证，最低支持的Docker版本是1.11.1，最高支持是18.06，而Docker最新版本已经是<code>18.09</code>了，故我们安装时需要指定版本为<code>18.06.1-ce</code>：</p>
<pre class="bash"><code>sudo apt install docker-ce=18.06.1~ce~3-0~ubuntu</code></pre>
</blockquote>
<h3 id="安装kubeadm-kubelet-和-kubectl">安装kubeadm, kubelet 和 kubectl</h3>
<p>部署之前，我们需要安装三个包：</p>
<ul>
<li><p><strong>kubeadm</strong>: 引导启动k8s集群的命令行工具。</p></li>
<li><p><strong>kubelet</strong>: 在群集中所有节点上运行的核心组件, 用来执行如启动pods和containers等操作。</p></li>
<li><p><strong>kubectl</strong>: 操作集群的命令行工具。</p></li>
</ul>
<p>首先添加apt-key：</p>
<pre class="bash"><code>sudo apt update &amp;&amp; sudo apt install -y apt-transport-https curl
curl -s https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -</code></pre>
<p>添加kubernetes源：</p>
<pre class="bash"><code>sudo vim /etc/apt/sources.list.d/kubernetes.list

deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main</code></pre>
<p>安装：</p>
<pre class="bash"><code>sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl</code></pre>
<h2 id="使用kubeadm创建一个单master集群">使用kubeadm创建一个单Master集群</h2>
<h3 id="初始化master节点">初始化Master节点</h3>
<p>K8s的控制面板组件运行在Master节点上，包括etcd和API server（Kubectl便是通过API server与k8s通信）。</p>
<p>在执行初始化之前，我们还有一下3点需要注意：</p>
<p>1.选择一个网络插件，并检查它是否需要在初始化Master时指定一些参数，比如我们可能需要根据选择的插件来设置<code>--pod-network-cidr</code>参数。参考：<a href="https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/#pod-network">Installing a pod network add-on</a>。</p>
<p>2.kubeadm使用eth0的默认网络接口（通常是内网IP）做为Master节点的advertise address，如果我们想使用不同的网络接口，可以使用<code>--apiserver-advertise-address=&lt;ip-address&gt;</code>参数来设置。如果适应IPv6，则必须使用IPv6d的地址，如：<code>--apiserver-advertise-address=fd00::101</code>。</p>
<p>3.使用<code>kubeadm config images pull</code>来预先拉取初始化需要用到的镜像，用来检查是否能连接到Kubenetes的Registries。</p>
<p>Kubenetes默认Registries地址是<code>k8s.gcr.io</code>，很明显，在国内并不能访问gcr.io，因此在kubeadm v1.13之前的版本，安装起来非常麻烦，但是在<code>1.13</code>版本中终于解决了国内的痛点，其增加了一个<code>--image-repository</code>参数，默认值是<code>k8s.gcr.io</code>，我们将其指定为国内镜像地址：<code>registry.aliyuncs.com/google_containers</code>，其它的就可以完全按照官方文档来愉快的玩耍了。</p>
<p>其次，我们还需要指定<code>--kubernetes-version</code>参数，因为它的默认值是<code>stable-1</code>，会导致从<code>https://dl.k8s.io/release/stable-1.txt</code>下载最新的版本号，我们可以将其指定为固定版本（最新版：v1.13.1）来跳过网络请求。</p>
<p>现在，我们就来试一下：</p>
<pre class="bash"><code># 使用calico网络 --pod-network-cidr=192.168.0.0/16
sudo kubeadm init --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.13.1 --pod-network-cidr=192.168.0.0/16

# 输出
[init] Using Kubernetes version: v1.13.1
[preflight] Running pre-flight checks
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using &#39;kubeadm config images pull&#39;
[kubelet-start] Writing kubelet environment file with flags to file &quot;/var/lib/kubelet/kubeadm-flags.env&quot;
[kubelet-start] Writing kubelet configuration to file &quot;/var/lib/kubelet/config.yaml&quot;
[kubelet-start] Activating the kubelet service
[certs] Using certificateDir folder &quot;/etc/kubernetes/pki&quot;
[certs] Generating &quot;ca&quot; certificate and key
[certs] Generating &quot;apiserver&quot; certificate and key
[certs] apiserver serving cert is signed for DNS names [master kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 172.17.20.210]
[certs] Generating &quot;apiserver-kubelet-client&quot; certificate and key
[certs] Generating &quot;front-proxy-ca&quot; certificate and key
[certs] Generating &quot;front-proxy-client&quot; certificate and key
[certs] Generating &quot;etcd/ca&quot; certificate and key
[certs] Generating &quot;etcd/peer&quot; certificate and key
[certs] etcd/peer serving cert is signed for DNS names [master localhost] and IPs [172.17.20.210 127.0.0.1 ::1]
[certs] Generating &quot;etcd/server&quot; certificate and key
[certs] etcd/server serving cert is signed for DNS names [master localhost] and IPs [172.17.20.210 127.0.0.1 ::1]
[certs] Generating &quot;etcd/healthcheck-client&quot; certificate and key
[certs] Generating &quot;apiserver-etcd-client&quot; certificate and key
[certs] Generating &quot;sa&quot; key and public key
[kubeconfig] Using kubeconfig folder &quot;/etc/kubernetes&quot;
[kubeconfig] Writing &quot;admin.conf&quot; kubeconfig file
[kubeconfig] Writing &quot;kubelet.conf&quot; kubeconfig file
[kubeconfig] Writing &quot;controller-manager.conf&quot; kubeconfig file
[kubeconfig] Writing &quot;scheduler.conf&quot; kubeconfig file
[control-plane] Using manifest folder &quot;/etc/kubernetes/manifests&quot;
[control-plane] Creating static Pod manifest for &quot;kube-apiserver&quot;
[control-plane] Creating static Pod manifest for &quot;kube-controller-manager&quot;
[control-plane] Creating static Pod manifest for &quot;kube-scheduler&quot;
[etcd] Creating static Pod manifest for local etcd in &quot;/etc/kubernetes/manifests&quot;
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory &quot;/etc/kubernetes/manifests&quot;. This can take up to 4m0s
[kubelet-check] Initial timeout of 40s passed.
[apiclient] All control plane components are healthy after 42.003645 seconds
[uploadconfig] storing the configuration used in ConfigMap &quot;kubeadm-config&quot; in the &quot;kube-system&quot; Namespace
[kubelet] Creating a ConfigMap &quot;kubelet-config-1.13&quot; in namespace kube-system with the configuration for the kubelets in the cluster
[patchnode] Uploading the CRI Socket information &quot;/var/run/dockershim.sock&quot; to the Node API object &quot;master&quot; as an annotation
[mark-control-plane] Marking the node master as control-plane by adding the label &quot;node-role.kubernetes.io/master=&#39;&#39;&quot;
[mark-control-plane] Marking the node master as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: 6pkrlg.8glf2fqpuf3i489m
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstraptoken] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstraptoken] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstraptoken] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstraptoken] creating the &quot;cluster-info&quot; ConfigMap in the &quot;kube-public&quot; namespace
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes master has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run &quot;kubectl apply -f [podnetwork].yaml&quot; with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join 172.17.20.210:6443 --token 6pkrlg.8glf2fqpuf3i489m --discovery-token-ca-cert-hash sha256:eebfe256113bee397b218ba832f412273ae734bd4686241fb910885d26efd222</code></pre>
<p>这次非常顺利的就部署成功了，如果我们想使用非root用户操作<code>kubectl</code>，可以使用以下命令，这也是<code>kubeadm init</code>输出的一部分：</p>
<pre class="bash"><code>mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config</code></pre>
<h3 id="安装网络插件">安装网络插件</h3>
<p>为了让Pods间可以相互通信，我们必须安装一个网络插件，并且必须在部署任何应用之前安装，<strong>CoreDNS</strong>也是在网络插件安装之后才会启动的。</p>
<p>网络的插件完整列表，请参考 <a href="https://kubernetes.io/docs/concepts/cluster-administration/addons/">Networking and Network Policy</a>。</p>
<p>在安装之前，我们先查看一下当前Pods的状态：</p>
<pre class="bash"><code>kubectl get pods --all-namespaces

# 输出
NAMESPACE     NAME                             READY   STATUS    RESTARTS   AGE
kube-system   coredns-78d4cf999f-6pgfr         0/1     Pending   0          87s
kube-system   coredns-78d4cf999f-m9kgs         0/1     Pending   0          87s
kube-system   etcd-master                      1/1     Running   0          47s
kube-system   kube-apiserver-master            1/1     Running   0          38s
kube-system   kube-controller-manager-master   1/1     Running   0          55s
kube-system   kube-proxy-mkg24                 1/1     Running   0          87s
kube-system   kube-scheduler-master            1/1     Running   0          41s</code></pre>
<p>如上，可以看到<strong>CoreDND</strong>的状态是<code>Pending</code>，这是因为我们还没有安装网络插件。</p>
<p><strong>Calico</strong>是一个纯三层的虚拟网络方案，Calico 为每个容器分配一个 IP，每个 host 都是 router，把不同 host 的容器连接起来。与 VxLAN 不同的是，Calico 不对数据包做额外封装，不需要 NAT 和端口映射，扩展性和性能都很好。</p>
<p>默认情况下，Calico网络插件使用的的网段是<code>192.168.0.0/16</code>，在<code>init</code>的时候，我们已经通过<code>--pod-network-cidr=192.168.0.0/16</code>来适配Calico，当然你也可以修改<code>calico.yml</code>文件来指定不同的网段。</p>
<p>可以使用如下命令命令来安装<code>Canal</code>插件：</p>
<pre class="bash"><code>kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
kubectl apply -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml

# 上面的calico.yaml会去quay.io拉取镜像，如果无法拉取，可使用下面的国内镜像
kubectl apply -f http://mirror.faasx.com/k8s/calico/v3.3.2/rbac-kdd.yaml
kubectl apply -f http://mirror.faasx.com/k8s/calico/v3.3.2/calico.yaml</code></pre>
<p>关于更多<code>Canal</code>的信息可以查看Calico官方文档：<a href="https://docs.projectcalico.org/v3.3/getting-started/kubernetes">kubeadm quickstart</a>。</p>
<p>稍等片刻，再使用<code>kubectl get pods --all-namespaces</code>命令来查看网络插件的安装情况：</p>
<pre class="text"><code>kubectl get pods --all-namespaces

# 输出
NAMESPACE     NAME                             READY   STATUS    RESTARTS   AGE
kube-system   calico-node-x96gn                2/2     Running   0          47s
kube-system   coredns-78d4cf999f-6pgfr         1/1     Running   0          54m
kube-system   coredns-78d4cf999f-m9kgs         1/1     Running   0          54m
kube-system   etcd-master                      1/1     Running   3          53m
kube-system   kube-apiserver-master            1/1     Running   3          53m
kube-system   kube-controller-manager-master   1/1     Running   3          53m
kube-system   kube-proxy-mkg24                 1/1     Running   2          54m
kube-system   kube-scheduler-master            1/1     Running   3          53m</code></pre>
<p>如上，STATUS全部变为了<code>Running</code>，表示安装成功，接下来就可以加入其他节点以及部署应用了。</p>
<h3 id="master隔离">Master隔离</h3>
<p>默认情况下，由于安全原因，集群并不会将pods部署在Master节点上。但是在开发环境下，我们可能就只有一个Master节点，这时可以使用下面的命令来解除这个限制：</p>
<pre class="bash"><code>kubectl taint nodes --all node-role.kubernetes.io/master-

## 输出
node/master untainted</code></pre>
<h3 id="加入工作节点">加入工作节点</h3>
<p>要为群集添加工作节点，需要为每台计算机执行以下操作：</p>
<ul>
<li>SSH到机器</li>
<li>成为root用户，(如: sudo su -)</li>
<li>运行上面的<code>kubeadm init</code>命令输出的：<code>kubeadm join --token &lt;token&gt; &lt;master-ip&gt;:&lt;master-port&gt; --discovery-token-ca-cert-hash sha256:&lt;hash&gt;</code></li>
</ul>
<p>如果我们忘记了Master节点的加入token，可以使用如下命令来查看：</p>
<pre class="bash"><code>kubeadm token list

# 输出
TOKEN                     TTL       EXPIRES                USAGES                   DESCRIPTION                                                EXTRA GROUPS
6pkrlg.8glf2fqpuf3i489m   22h       2018-12-07T13:46:33Z   authentication,signing   The default bootstrap token generated by &#39;kubeadm init&#39;.   system:bootstrappers:kubeadm:default-node-token</code></pre>
<p>默认情况下，token的有效期是24小时，如果我们的token已经过期的话，可以使用以下命令重新生成：</p>
<pre class="bash"><code>kubeadm token create

# 输出
u2mt59.tyqpo0v5wf05lx2q</code></pre>
<p>如果我们也没有<code>--discovery-token-ca-cert-hash</code>的值，可以使用以下命令生成：</p>
<pre class="bash"><code>openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2&gt;/dev/null | openssl dgst -sha256 -hex | sed &#39;s/^.* //&#39;

# 输出
eebfe256113bee397b218ba832f412273ae734bd4686241fb910885d26efd222</code></pre>
<p>现在，我们登录到工作节点服务器，然后运行如下命令加入集群（这也是上面<code>init</code>输出的一部分）：</p>
<pre class="bash"><code>sudo kubeadm join 172.17.20.210:6443 --token 6pkrlg.8glf2fqpuf3i489m --discovery-token-ca-cert-hash sha256:eebfe256113bee397b218ba832f412273ae734bd4686241fb910885d26efd222

# 输出
[sudo] password for raining: 
[preflight] Running pre-flight checks
[discovery] Trying to connect to API Server &quot;172.17.20.210:6443&quot;
[discovery] Created cluster-info discovery client, requesting info from &quot;https://172.17.20.210:6443&quot;
[discovery] Requesting info from &quot;https://172.17.20.210:6443&quot; again to validate TLS against the pinned public key
[discovery] Cluster info signature and contents are valid and TLS certificate validates against pinned roots, will use API Server &quot;172.17.20.210:6443&quot;
[discovery] Successfully established connection with API Server &quot;172.17.20.210:6443&quot;
[join] Reading configuration from the cluster...
[join] FYI: You can look at this config file with &#39;kubectl -n kube-system get cm kubeadm-config -oyaml&#39;
[kubelet] Downloading configuration for the kubelet from the &quot;kubelet-config-1.13&quot; ConfigMap in the kube-system namespace
[kubelet-start] Writing kubelet configuration to file &quot;/var/lib/kubelet/config.yaml&quot;
[kubelet-start] Writing kubelet environment file with flags to file &quot;/var/lib/kubelet/kubeadm-flags.env&quot;
[kubelet-start] Activating the kubelet service
[tlsbootstrap] Waiting for the kubelet to perform the TLS Bootstrap...
[patchnode] Uploading the CRI Socket information &quot;/var/run/dockershim.sock&quot; to the Node API object &quot;node1&quot; as an annotation

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run &#39;kubectl get nodes&#39; on the master to see this node join the cluster.</code></pre>
<p>等待一会，我们可以在Master节点上使用<code>kubectl get nodes</code>命令来查看节点的状态：</p>
<pre class="bash"><code>kubectl get nodes

# 输出
NAME     STATUS   ROLES    AGE   VERSION
master   Ready    master   17m   v1.13.1
node1    Ready    &lt;none&gt;   15m   v1.13.1</code></pre>
<p>如上全部<code>Ready</code>，大功告成，我们可以运行一些命令来测试一下集群是否正常。</p>
<h3 id="测试">测试</h3>
<p>首先验证<em>kube-apiserver</em>, <em>kube-controller-manager</em>, <em>kube-scheduler</em>, <em>pod network</em> 是否正常：</p>
<pre class="bash"><code># 部署一个 Nginx Deployment，包含两个Pod
# https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
kubectl create deployment nginx --image=nginx:alpine
kubectl scale deployment nginx --replicas=2

# 验证Nginx Pod是否正确运行，并且会分配192.168.开头的集群IP
kubectl get pods -l app=nginx -o wide

# 输出如下：
NAME                     READY   STATUS    RESTARTS   AGE   IP            NODE    NOMINATED NODE   READINESS GATES
nginx-54458cd494-p8jzs   1/1     Running   0          31s   192.168.1.2   node1   &lt;none&gt;           &lt;none&gt;
nginx-54458cd494-v2m4b   1/1     Running   0          24s   192.168.1.3   node1   &lt;none&gt;           &lt;none&gt;</code></pre>
<p>再验证一下<code>kube-proxy</code>是否正常：</p>
<pre class="bash"><code># 以 NodePort 方式对外提供服务 https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/
kubectl expose deployment nginx --port=80 --type=NodePort

# 查看集群外可访问的Port
kubectl get services nginx

# 输出
NAME    TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
nginx   NodePort   10.110.49.49   &lt;none&gt;        80:31899/TCP   4s

# 可以通过任意 NodeIP:Port 在集群外部访问这个服务，本示例中部署的2台集群IP分别是172.17.20.210和172.17.20.211
curl http://172.17.20.210:31899
curl http://172.17.20.211:31899</code></pre>
<p>最后验证一下<em>dns</em>, <em>pod network</em>是否正常：</p>
<pre class="bash"><code># 运行Busybox并进入交互模式
kubectl run -it curl --image=radial/busyboxplus:curl

# 输入`nslookup nginx`查看是否可以正确解析出集群内的IP，已验证DNS是否正常
[ root@curl-66959f6557-6sfqh:/ ]$ nslookup nginx

# 输出
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local

Name:      nginx
Address 1: 10.110.49.49 nginx.default.svc.cluster.local

# 通过服务名进行访问，验证kube-proxy是否正常
[ root@curl-66959f6557-6sfqh:/ ]$ curl http://nginx/

# 输出如下：
# &lt;!DOCTYPE html&gt; ---省略

# 分别访问一下2个Pod的内网IP，验证跨Node的网络通信是否正常
[ root@curl-66959f6557-6sfqh:/ ]$ curl http://192.168.1.2/
[ root@curl-66959f6557-6sfqh:/ ]$ curl http://192.168.1.3/</code></pre>
<p>验证通过，集群搭建成功，接下来我们就可以参考<a href="https://kubernetes.io/docs/home">官方文档</a>来部署其他服务，愉快的玩耍了。</p>
<h2 id="卸载集群">卸载集群</h2>
<p>想要撤销kubeadm执行的操作，首先要<a href="https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#drain">排除节点</a>，并确保该节点为空, 然后再将其关闭。</p>
<p>在Master节点上运行：</p>
<pre class="bash"><code>kubectl drain &lt;node name&gt; --delete-local-data --force --ignore-daemonsets
kubectl delete node &lt;node name&gt;</code></pre>
<p>然后在需要移除的节点上，重置kubeadm的安装状态：</p>
<pre class="bash"><code>sudo kubeadm reset</code></pre>
<p>如果你想重新配置集群，使用新的参数重新运行<code>kubeadm init</code>或者<code>kubeadm join</code>即可。</p>
<h2 id="参考资料">参考资料</h2>
<ul>
<li><a href="https://kubernetes.io/docs/setup/independent/create-cluster-kubeadm/">Creating a single master cluster with kubeadm</a></li>
<li><a href="https://www.cnblogs.com/RainingNight/p/using-kubeadm-to-create-a-cluster-1-12.html">使用Kubeadm搭建Kubernetes(1.12.2)集群</a></li>
</ul>
</div><div id="MySignature"></div>
<div class="clear"></div>
<div id="blog_post_info_block">
<div id="BlogPostCategory"></div>
<div id="EntryTag"></div>
<div id="blog_post_info">
</div>
<div class="clear"></div>
<div id="post_next_prev"></div>
</div>


		</div>
		<div class = "postDesc">posted @ <span id="post-date">2018-12-07 13:27</span> <a href='https://www.cnblogs.com/RainingNight/'>雨夜朦胧</a> 阅读(<span id="post_view_count">...</span>) 评论(<span id="post_comment_count">...</span>)  <a href ="https://i.cnblogs.com/EditPosts.aspx?postid=10080461" rel="nofollow">编辑</a> <a href="#" onclick="AddToWz(10080461);return false;">收藏</a></div>
	</div>
	<script src="//common.cnblogs.com/highlight/9.12.0/highlight.min.js"></script><script>markdown_highlight();</script><script type="text/javascript">var allowComments=true,cb_blogId=100878,cb_entryId=10080461,cb_blogApp=currentBlogApp,cb_blogUserGuid='037655d2-6f05-e111-b7b9-842b2b196315',cb_entryCreatedDate='2018/12/7 13:27:00';loadViewCount(cb_entryId);var cb_postType=1;var isMarkdown=true;</script>
	
</div><!--end: topics 文章、评论容器-->
</div><a name="!comments"></a><div id="blog-comments-placeholder"></div><script type="text/javascript">var commentManager = new blogCommentManager();commentManager.renderComments(0);</script>
<div id='comment_form' class='commentform'>
<a name='commentform'></a>
<div id='divCommentShow'></div>
<div id='comment_nav'><span id='span_refresh_tips'></span><a href='javascript:void(0);' onclick='return RefreshCommentList();' id='lnk_RefreshComments' runat='server' clientidmode='Static'>刷新评论</a><a href='#' onclick='return RefreshPage();'>刷新页面</a><a href='#top'>返回顶部</a></div>
<div id='comment_form_container'></div>
<div class='ad_text_commentbox' id='ad_text_under_commentbox'></div>
<div id='ad_t2'></div>
<div id='opt_under_post'></div>
<script async='async' src='https://www.googletagservices.com/tag/js/gpt.js'></script>
<script>
  var googletag = googletag || {};
  googletag.cmd = googletag.cmd || [];
</script>
<script>
  googletag.cmd.push(function() {
        googletag.defineSlot('/1090369/C1', [300, 250], 'div-gpt-ad-1546353474406-0').addService(googletag.pubads());
        googletag.defineSlot('/1090369/C2', [468, 60], 'div-gpt-ad-1539008685004-0').addService(googletag.pubads());
        googletag.pubads().enableSingleRequest();
        googletag.enableServices();
  });
</script>
<div id='cnblogs_c1' class='c_ad_block'>
    <div id='div-gpt-ad-1546353474406-0' style='height:250px; width:300px;'></div>
</div>
<div id='under_post_news'></div>
<div id='cnblogs_c2' class='c_ad_block'>
    <div id='div-gpt-ad-1539008685004-0' style='height:60px; width:468px;'></div>
</div>
<div id='under_post_kb'></div>
<div id='HistoryToday' class='c_ad_block'></div>
<script type='text/javascript'>
 if(enablePostBottom()) {
    codeHighlight();
    fixPostBody();
    setTimeout(function () { incrementViewCount(cb_entryId); }, 50);
    deliverT2();
    deliverC1();
    deliverC2();    
    loadNewsAndKb();
    loadBlogSignature();
    LoadPostInfoBlock(cb_blogId, cb_entryId, cb_blogApp, cb_blogUserGuid);
    GetPrevNextPost(cb_entryId, cb_blogId, cb_entryCreatedDate, cb_postType);
    loadOptUnderPost();
    GetHistoryToday(cb_blogId, cb_blogApp, cb_entryCreatedDate);  
}
</script>
</div>

    
	</div><!--end: forFlow -->
	</div><!--end: mainContent 主体内容容器-->

	<div id="sideBar">
		<div id="sideBarMain">
			
<!--done-->
<div class="newsItem">
<h3 class="catListTitle">公告</h3>
	<div id="blog-news"></div><script type="text/javascript">loadBlogNews();</script>
</div>

			<div id="calendar"><div id="blog-calendar" style="display:none"></div><script type="text/javascript">loadBlogDefaultCalendar();</script></div>
			
			<div id="leftcontentcontainer">
				<div id="blog-sidecolumn"></div><script type="text/javascript">loadBlogSideColumn();</script>
			</div>
			
		</div><!--end: sideBarMain -->
	</div><!--end: sideBar 侧边栏容器 -->
	<div class="clear"></div>
	</div><!--end: main -->
	<div class="clear"></div>
	<div id="footer">
		
<!--done-->
Copyright &copy;2019 雨夜朦胧
	</div><!--end: footer -->
</div><!--end: home 自定义的最大容器 -->

</body>
</html>
