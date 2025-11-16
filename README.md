# rustfs-charts

Modified from the official one, and make it more common.  
从官网版本进行修改，让其变得更加实用。

This one provides a Single-Node-Single-Disk installation or a Multi-Node-Single-Disk installation, via setting `replicaCount` field.  
本 Chart 提供单机单盘安装和多机单盘安装。通过修改字段`replicaCount`的值，您可以控制节点的数量。

> WIP: This chart will provide a way to control the number of data disks per rustfs node.  
> 进行中：本 Chart 后续将提供配置每节点数据盘数量的方式。

You can configure your rustfs installation more accurately. However, there's something you should notice that:

1. Make sure the secret used by the TLS ingress existing, because this chart removed the `secret-tls.yaml` and its related files offered by the official one.
2. This chart changes the default `imagePullPolicy` into `IfNotPresent`.
3. This chart changes the rustfs environment into `production` and its log-level into `info`.
4. This chart changes the default `service.type` into `ClusterIP` and it will NOT create `Ingress` object until configured.
5. This chart increases the default resources requested, which will make your rustfs instance run more happily.
6. Do NOT forget to assign a `StorageClass` for your rustfs instance.

通过本 Chart，你可以更加精确地控制你的 rustfs 安装。但是，有些值得你留意的点：

1. 确保你为你的 TLS Ingress 提供了证书。官方 Chart 中的 `secret-tls.yaml`及其相关文件在此被移除。
2. 默认的 `imagePullPolicy` 被设置为 `IfNotPresent`。
3. rustfs 的运行配置被修改为 `production`， 同时日志等级也被调整为 `info`。
4. `Service` 对象的默认类型被修改为 `ClusterIP`，并且在你手动配置之前，不会自行创建 `Ingress` 对象。
5. 稍稍增加了默认的资源请求量，更方便你体验 rustfs。
6. 请为你的 rustfs 实例指定 `StorageClass`。

Finally, here's an example for deployment:  
最后，这里是一个部署命令示例：

```bash
# we are in the `helm` dir and here lies a `rustfs` folder
helm install rustfs ./rustfs -n rustfs --create-namespace \
--set replicaCount=16 \
--set initStep.repository="my.docker.registry.io/docker.io/library/busybox" \
--set secret.rustfs.secret_key="idontwantusethedefaultone" \
--set service.type="NodePort" \
--set ingress.enabled="true" \
--set ingress.className="nginx" \
--set ingress.hosts[0].host="rustfs.example.io" \
--set ingress.hosts[0].paths[0].path="/" \
--set ingress.hosts[0].paths[0].pathType="Prefix" \
--set ingress.tls[0].secretName="my-rustfs-ingress-secret" \
--set ingress.tls[0].hosts[0]="rustfs.example.io" \
--set storageclass.name="local-lvmpv"
# You can configure more
# You can also explicitly edit the value.yaml, or even the templates
```

By the way, if you are troubled by local storage management, you can give a look at [OpenEBS](https://openebs.io/)  
顺带一提，如果你需要一个工具将帮你把本地存储管理起来，不妨看看[OpenEBS](https://openebs.io/)

Happy helming.
