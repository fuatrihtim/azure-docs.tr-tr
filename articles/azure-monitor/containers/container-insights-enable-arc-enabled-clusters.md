---
title: Azure Arc etkin Kubernetes kümesini kapsayıcı öngörüleri ile yapılandırma | Microsoft Docs
description: Bu makalede, Azure Arc etkin Kubernetes kümelerinde kapsayıcı öngörüleri ile izlemenin nasıl yapılandırılacağı açıklanır.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 307f9d9928042410dc9b4443aba5c019c592980c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711306"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc özellikli Kubernetes kümesinin izlenmesini etkinleştirme

Kapsayıcı öngörüleri, Azure Kubernetes hizmeti (AKS) ve AKS motoru kümeleri için zengin izleme deneyimi sağlar. Bu makalede, Azure 'un dışında barındırılan Kubernetes kümelerinin, benzer bir izleme deneyimi elde etmek için Azure Arc ile etkinleştirilen izlemenin nasıl etkinleştirileceği açıklanır.

Bir PowerShell veya bash betiği kullanan bir veya daha fazla Kubernetes dağıtımı için kapsayıcı öngörüleri etkinleştirilebilir.

## <a name="supported-configurations"></a>Desteklenen yapılandırmalar

Kapsayıcı öngörüleri, aşağıdaki özellikler hariç olmak üzere [genel bakış](container-insights-overview.md) makalesinde açıklandığı gibi, Azure Arc etkinleştirilmiş Kubernetes 'in (Önizleme) izlenmesini destekler:

- Canlı veriler (Önizleme)

Kapsayıcı öngörüleri ile resmi olarak desteklenen aşağıdakiler aşağıda verilmiştir:

- Kubernetes ve destek ilkesi sürümleri [desteklenen aks](../../aks/supported-kubernetes-versions.md)sürümleriyle aynıdır.

- Şu kapsayıcı çalışma zamanları desteklenir: Docker, Moby, ve CRı ile uyumlu çalışma zamanları, örneğin CRı-O ve ContainerD.

- Desteklenen ana ve çalışan düğümleri için Linux işletim sistemi sürümü: Ubuntu (18,04 LTS ve 16,04 LTS).

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdakilere sahip olduğunuzdan emin olun:

- Log Analytics çalışma alanı.

    Kapsayıcı öngörüleri [bölgeye göre Azure ürünlerinde](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)listelenen bölgelerde bir Log Analytics çalışma alanını destekler. Kendi çalışma alanınızı oluşturmak için [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)aracılığıyla veya [Azure Portal](../logs/quick-create-workspace.md)aracılığıyla oluşturulabilir.

- Kapsayıcı öngörülerine yönelik özellikleri etkinleştirmek ve bu özelliklere erişmek için, en azından Azure aboneliğinde Azure *katkıda* bulunan rolünün bir üyesi olmanız ve kapsayıcı öngörüleri ile yapılandırılmış Log Analytics çalışma alanının [*Log Analytics katkıda*](../logs/manage-access.md#manage-access-using-azure-permissions) bulunan rolünün bir üyesi olmanız gerekir.

- Azure Arc küme kaynağında [katkıda](../../role-based-access-control/built-in-roles.md#contributor) bulunan rolünün bir üyesidir.

- İzleme verilerini görüntülemek için, kapsayıcı öngörüleri ile yapılandırılmış Log Analytics çalışma alanına sahip [*Log Analytics okuyucu*](../logs/manage-access.md#manage-access-using-azure-permissions) rolü izninin bir üyesidir.

- Belirtilen Kubernetes kümesi için kapsayıcı öngörüleri grafiğini eklemek için [helk istemcisi](https://helm.sh/docs/using_helm/) .

- Linux için Log Analytics aracısının Kapsayıcılı sürümünün Azure Izleyici ile iletişim kurması için aşağıdaki proxy ve güvenlik duvarı yapılandırma bilgileri gereklidir:

    |Aracı Kaynağı|Bağlantı noktaları |
    |------|---------|
    |`*.ods.opinsights.azure.com` |Bağlantı noktası 443 |
    |`*.oms.opinsights.azure.com` |Bağlantı noktası 443 |
    |`*.dc.services.visualstudio.com` |Bağlantı noktası 443 |

- Kapsayıcılı Aracı, `cAdvisor secure port: 10250` `unsecure port :10255` performans ölçümlerini toplamak için, kümedeki tüm düğümlerde Kubelet 'in ve açık olmasını gerektirir. `secure port: 10250`Zaten yapılandırılmamışsa Kubelet 'In Cadvizörü üzerinde yapılandırmanızı öneririz.

- Kapsayıcılı Aracı, envanter verilerini toplamak için kümedeki Kubernetes API hizmetiyle iletişim kurmak üzere kapsayıcıda aşağıdaki çevresel değişkenlerin belirtilmesini gerektirir- `KUBERNETES_SERVICE_HOST` ve `KUBERNETES_PORT_443_TCP_PORT` .

    >[!IMPORTANT]
    >Yay etkin Kubernetes kümelerini izlemek için desteklenen en düşük aracı sürümü ciprod04162020 veya üzeri bir sürüm.

- PowerShell komut dosyalı metodunu kullanarak izlemeyi etkinleştirirseniz [PowerShell Core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true) gereklidir.

- Bash komut dosyası yöntemini kullanarak izlemeyi etkinleştirirseniz [Bash sürüm 4](https://www.gnu.org/software/bash/) gerekir.

## <a name="identify-workspace-resource-id"></a>Çalışma alanı kaynak KIMLIĞINI tanımla

Daha önce indirdiğiniz PowerShell veya bash betiğini kullanarak kümenizin izlenmesini etkinleştirmek ve mevcut bir Log Analytics çalışma alanıyla bütünleştirmek için, Log Analytics çalışma alanınızın tam kaynak KIMLIĞINI belirlemek üzere aşağıdaki adımları gerçekleştirin. `workspaceResourceId`Belirtilen çalışma alanına karşı izleme eklentisini etkinleştirmek için komutunu çalıştırdığınızda parametresi için bu gereklidir. Belirtmek için bir çalışma alanınız yoksa, parametresini de atlayabilirsiniz `workspaceResourceId` ve betiğin sizin için yeni bir çalışma alanı oluşturmasına izin verebilirsiniz.

1. Aşağıdaki komutu kullanarak erişiminiz olan tüm abonelikleri listeleyin:

    ```azurecli
    az account list --all -o table
    ```

    Çıktı aşağıdakine benzeyecektir:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    **SubscriptionID** değerini kopyalayın.

2. Aşağıdaki komutu kullanarak Log Analytics çalışma alanını barındıran aboneliğe geçin:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Aşağıdaki örnek, aboneliklerinizdeki çalışma alanlarının listesini varsayılan JSON biçiminde görüntüler.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Çıktıda, çalışma alanı adını bulun ve alan **kimliği** altında bu Log Analytics çalışma alanının tam kaynak kimliğini kopyalayın.

## <a name="enable-monitoring-using-powershell"></a>PowerShell 'i kullanarak izlemeyi etkinleştir

1. Aşağıdaki komutları kullanarak betiği, izleme eklentisi ile kümenizi yapılandıran yerel bir klasöre yükleyin ve kaydedin:

    ```powershell
    Invoke-WebRequest https://aka.ms/enable-monitoring-powershell-script -OutFile enable-monitoring.ps1
    ```

2. `$azureArcClusterResourceId`İçin karşılık gelen değerleri ayarlayarak `subscriptionId` `resourceGroupName` ve `clusterName` Azure Arc özellikli Kubernetes küme kaynağınızın kaynak kimliğini temsil ederek değişkeni yapılandırın.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `$kubeContext`Komutunu çalıştırarak değişkeni, kümenizin **kuin bağlamı** ile yapılandırın `kubectl config get-contexts` . Geçerli bağlamı kullanmak istiyorsanız, değerini olarak ayarlayın `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Mevcut Azure Izleyici Log Analytics çalışma alanını kullanmak istiyorsanız, değişkeni, `$logAnalyticsWorkspaceResourceId` çalışma alanının kaynak kimliğini temsil eden karşılık gelen değerle yapılandırın. Aksi takdirde, değişkenini olarak ayarlayın `""` ve komut dosyası bölgede yoksa, küme aboneliğinin varsayılan kaynak grubunda varsayılan bir çalışma alanı oluşturur. Oluşturulan varsayılan çalışma alanı, *defaultworkspace- \<SubscriptionID> - \<Region>* biçimine benzer.

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Arc etkin Kubernetes kümeniz bir ara sunucu üzerinden iletişim kuruyorsa, değişkeni `$proxyEndpoint` ara sunucunun URL 'siyle yapılandırın. Küme bir ara sunucu üzerinden iletişim kurmadığından, değerini olarak ayarlayabilirsiniz `""` .  Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [proxy uç noktasını yapılandırma](#configure-proxy-endpoint) konusuna bakın.

6. İzlemeyi etkinleştirmek için aşağıdaki komutu çalıştırın.

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

İzlemeyi etkinleştirdikten sonra, küme için sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir.

### <a name="using-service-principal"></a>Hizmet sorumlusu kullanma
Betik *enable-monitoring.ps1* etkileşimli cihaz oturum açma bilgilerini kullanır. Etkileşimli olmayan oturum açma tercih ediyorsanız, mevcut bir hizmet sorumlusunu kullanabilir veya [Önkoşullar](#prerequisites)bölümünde açıklandığı gibi gerekli izinlere sahip yeni bir tane oluşturabilirsiniz. Hizmet sorumlusu kullanmak için $servicePrincipalClientId, $servicePrincipalClientSecret ve $tenantId parametrelerini, komut dosyası *enable-monitoring.ps1* için kullanmayı amaçladığınız hizmet sorumlusu değerleriyle geçirmeniz gerekir.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

Aşağıdaki rol ataması yalnızca, Arc K8s bağlı küme kaynağına göre farklı bir Azure aboneliğinde mevcut Log Analytics çalışma alanını kullanıyorsanız geçerlidir.

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Örnek:

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>Bash betiğini kullanmayı etkinleştir

Belirtilen Bash betiğini kullanarak izlemeyi etkinleştirmek için aşağıdaki adımları gerçekleştirin.

1. Aşağıdaki komutları kullanarak betiği, izleme eklentisi ile kümenizi yapılandıran yerel bir klasöre yükleyin ve kaydedin:

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. `azureArcClusterResourceId`İçin karşılık gelen değerleri ayarlayarak `subscriptionId` `resourceGroupName` ve `clusterName` Azure Arc özellikli Kubernetes küme kaynağınızın kaynak kimliğini temsil ederek değişkeni yapılandırın.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `kubeContext`Komutunu çalıştırarak değişkeni, kümenizin **kuin bağlamı** ile yapılandırın `kubectl config get-contexts` . Geçerli bağlamı kullanmak istiyorsanız, değerini olarak ayarlayın `""` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Mevcut Azure Izleyici Log Analytics çalışma alanını kullanmak istiyorsanız, değişkeni, `logAnalyticsWorkspaceResourceId` çalışma alanının kaynak kimliğini temsil eden karşılık gelen değerle yapılandırın. Aksi takdirde, değişkenini olarak ayarlayın `""` ve komut dosyası bölgede yoksa, küme aboneliğinin varsayılan kaynak grubunda varsayılan bir çalışma alanı oluşturur. Oluşturulan varsayılan çalışma alanı, *defaultworkspace- \<SubscriptionID> - \<Region>* biçimine benzer.

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Arc etkin Kubernetes kümeniz bir ara sunucu üzerinden iletişim kuruyorsa, değişkeni `proxyEndpoint` ara sunucunun URL 'siyle yapılandırın. Küme bir ara sunucu üzerinden iletişim kurmadığından, değerini olarak ayarlayabilirsiniz `""` . Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [proxy uç noktasını yapılandırma](#configure-proxy-endpoint) konusuna bakın.

6. Kümenizde izlemeyi etkinleştirmek için, dağıtım senaryonuz temelinde sunulan farklı komutlar vardır.

    Geçerli kuas-Context kullanma, varsayılan bir Log Analytics çalışma alanı oluşturma ve bir ara sunucu belirtmeden, varsayılan seçeneklerle izlemeyi etkinleştirmek için aşağıdaki komutu çalıştırın:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Varsayılan bir Log Analytics çalışma alanı oluşturmak ve proxy sunucusu belirtmeden aşağıdaki komutu çalıştırın:

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    Mevcut bir Log Analytics çalışma alanını ve bir ara sunucu belirtmeden kullanmak için aşağıdaki komutu çalıştırın:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    Mevcut bir Log Analytics çalışma alanını kullanmak ve bir proxy sunucusu belirtmek için aşağıdaki komutu çalıştırın:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

İzlemeyi etkinleştirdikten sonra, küme için sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir.

### <a name="using-service-principal"></a>Hizmet sorumlusu kullanma
Bash betiği *Enable-Monitoring.sh* etkileşimli cihaz oturum açma bilgilerini kullanır. Etkileşimli olmayan oturum açma tercih ediyorsanız, mevcut bir hizmet sorumlusunu kullanabilir veya [Önkoşullar](#prerequisites)bölümünde açıklandığı gibi gerekli izinlere sahip yeni bir tane oluşturabilirsiniz. Hizmet sorumlusu 'nı kullanmak için, *Enable-Monitoring.sh* Bash betiğine kullanmayı amaçladığı hizmet sorumlusu için--istemci kimliği,--istemci-gizli ve--Kiracı kimliği değerlerini geçirmeniz gerekir.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

Aşağıdaki rol ataması yalnızca, Arc K8s bağlı küme kaynağına göre farklı bir Azure aboneliğinde mevcut Log Analytics çalışma alanını kullanıyorsanız geçerlidir.

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Örnek:

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>Proxy uç noktasını yapılandırma

Kapsayıcı içgörüleri için Kapsayıcılı aracı sayesinde, proxy sunucusu üzerinden iletişim kurmasına izin vermek için bir proxy uç noktası yapılandırabilirsiniz. Kapsayıcılı aracı ve Azure Izleyici arasındaki iletişim bir HTTP veya HTTPS proxy sunucusu olabilir ve hem anonim hem de temel kimlik doğrulaması (Kullanıcı adı/parola) desteklenir.

Ara sunucu yapılandırma değeri aşağıdaki sözdizimine sahiptir: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Proxy sunucunuz kimlik doğrulaması gerektirmiyorsa, yine de bir psuedo Kullanıcı adı/parolası belirtmeniz gerekir. Bu, herhangi bir Kullanıcı adı veya parola olabilir.

|Özellik| Açıklama |
|--------|-------------|
|Protokol | http veya https |
|kullanıcı | Proxy kimlik doğrulaması için isteğe bağlı Kullanıcı adı |
|password | Proxy kimlik doğrulaması için isteğe bağlı parola |
|proxyhost | Proxy sunucusunun adresi veya FQDN 'si |
|port | Proxy sunucusu için isteğe bağlı bağlantı noktası numarası |

Örnek: `http://user01:password@proxy01.contoso.com:3128`

Protokolü **http** olarak BELIRTIRSENIZ, http istekleri SSL/TLS güvenli bağlantı kullanılarak oluşturulur. Ara sunucunuzun SSL/TLS protokollerini desteklemesi gerekir.

### <a name="configure-using-powershell"></a>PowerShell’i kullanarak yapılandırma

Proxy sunucusu için Kullanıcı adı ve parola, IP adresi veya FQDN ve bağlantı noktası numarasını belirtin. Örnek:

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>Bash kullanarak yapılandırma

Proxy sunucusu için Kullanıcı adı ve parola, IP adresi veya FQDN ve bağlantı noktası numarasını belirtin. Örnek:

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>Sonraki adımlar

- İzleme etkinken, Arc etkin Kubernetes kümeniz ve üzerinde çalışan iş yüklerinizin sistem durumunu ve kaynak kullanımını toplamak için, kapsayıcı öngörülerini [nasıl](container-insights-analyze.md) kullanacağınızı öğrenin.

- Varsayılan olarak, Kapsayıcılı Aracı, Kuto-System hariç tüm ad alanlarında çalışan tüm kapsayıcıların stdout/stderr kapsayıcı günlüklerini toplar. Belirli bir ad alanı veya ad alanına özgü kapsayıcı günlüğü koleksiyonunu yapılandırmak için, istenen veri koleksiyonu ayarlarını ConfigMap yapılandırmaları dosyanıza yapılandırmak üzere [kapsayıcı öngörüleri aracı yapılandırmasını](container-insights-agent-config.md) gözden geçirin.

- Kümelediğiniz Prometheus ölçümlerini hurdaya almak ve analiz etmek için bkz. [Prometheus ölçümleri koruması](container-insights-prometheus-integration.md) 'nı inceleyin

- Arc etkin Kubernetes kümenizi kapsayıcı öngörüleri ile izlemeyi durdurmayı öğrenmek için bkz. [karma kümenizi izlemeyi durdurma](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes).