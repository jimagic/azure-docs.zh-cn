---
title: 快速入门 - 在 Azure 容器实例中运行应用程序 - CLI
description: 本快速入门介绍如何使用 Azure CLI 部署 Docker 容器应用程序，以在 Azure 容器实例中的独立容器中运行
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 70d1bc9003d98f0154b9f38738f1b8e82b0c506d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189602"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-with-the-azure-cli"></a>快速入门：使用 Azure CLI 在 Azure 容器实例中运行容器应用程序

使用 Azure 容器实例在 Azure 中快速方便地运行 Docker 容器。 不需要部署虚拟机或使用 Kubernetes 之类的完整容器业务流程平台。 在本快速入门中，我们将使用 Azure CLI 在 Azure 中创建一个容器，并使其应用程序可通过完全限定的域名 (FQDN) 使用。 在执行单个部署命令几秒钟之后，可以浏览到正在运行的应用程序：

![在浏览器中显示的已部署到 Azure 容器实例的应用][aci-app-browser]

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][azure-account]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成本快速入门。 如果想要在本地使用它，则需要版本 2.0.27 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli-install]。

## <a name="create-a-resource-group"></a>创建资源组

Azure 容器实例（例如所有 Azure 资源）都必须部署到资源组中。 使用资源组，可以组织和管理相关的 Azure 资源。

首先，使用以下 [az group create][az-group-create] 命令在 *eastus* 位置中创建一个名为 *myResourceGroup* 的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>创建容器

创建资源组后，可在 Azure 中运行容器。 若要使用 Azure CLI 创建容器实例，请向 [az container create][az-container-create] 命令提供一个资源组名称、容器实例名称和 Docker 容器映像。 可以通过指定要打开的一个或多个端口、一个 DNS 名称标签（或同时指定两者）来向 Internet 公开你的容器。 在本快速入门中，你将部署一个具有 DNS 名称标签的容器，该容器承载着一个使用 Node.js 编写的小型 Web 应用。

执行以下命令以启动容器实例。 在创建实例的 Azure 区域中，`--dns-name-label` 值必须是唯一的。 如果收到“DNS 名称标签不可用”错误消息，请尝试使用一个不同的 DNS 名称标签。

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

在几秒钟内，你应当会从 Azure CLI 收到响应，它指出部署已完成。 使用 [az container show][az-container-show] 命令检查它的状态：

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

运行此命令时，会显示容器的完全限定域名 (FQDN) 及其预配状态。

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

如果容器的 `ProvisioningState` 为 **Succeeded**，则在浏览器中导航到其 FQDN。 如果看到一个与下图类似的网页，那么恭喜你了！ 现已成功将 Docker 容器中运行的应用程序部署到 Azure。

![浏览器屏幕截图，显示应用程序在 Azure 容器实例中运行][aci-app-browser]

如果应用程序起初未显示，你可能需要在 DNS 传播时等待几秒钟，然后刷新浏览器。

## <a name="pull-the-container-logs"></a>拉取容器日志

当需要对容器或它运行的应用程序进行故障排除时（或者只是查看其输出），请首先查看容器实例的日志。

使用 [az container logs][az-container-logs] 命令拉取容器实例日志：

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

此输出显示容器的日志，并应显示在浏览器中查看应用程序时生成的 HTTP GET 请求。

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.105 - - [01/Oct/2018:18:25:51 +0000] "GET / HTTP/1.0" 200 1663 "-" "-"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
```

## <a name="attach-output-streams"></a>附加输出流

除了查看日志之外，还可将本地标准输出和标准错误流附加到容器的数据流中。

首先，请执行 [az container attach][az-container-attach] 命令，将本地控制台输出附加到容器的输出流：

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

附加后，刷新浏览器数次，以生成其他一些输出。 完成后，使用 `Control+C` 分离控制台。 应该会看到与下面类似的输出：

```console
$ az container attach --resource-group myResourceGroup -n mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-15 21:17:59+00:00) pulling image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Successfully pulled image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Created container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45
(count: 1) (last timestamp: 2018-03-15 21:18:06+00:00) Started container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45

Start streaming logs:
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:44 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:47 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="clean-up-resources"></a>清理资源

完成容器的操作后，可使用 [az container delete][az-container-delete] 命令将其删除：

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

若要验证已删除该容器，请执行 [az container list](/cli/azure/container#az-container-list) 命令：

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

mycontainer 容器不应出现在命令的输出中。 如果资源组中没有任何其他容器，则不会显示任何输出。

如果已使用完 *myResourceGroup* 资源组及其包含的所有资源，请使用 [az group delete][az-group-delete] 命令将其删除：

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用公共 Docker 中心注册表中的映像创建了一个 Azure 容器实例。 若要基于专用 Azure 容器注册表生成容器映像并部署它，请继续学习 Azure 容器实例教程。

> [!div class="nextstepaction"]
> [Azure 容器实例教程](./container-instances-tutorial-prepare-app.md)

若要 Azure 上尝试用于运行业务流程系统中的容器的选项，请参阅 [Service Fabric][service-fabric] 或 [Azure Kubernetes Service (AKS)][container-service] 快速入门。

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
