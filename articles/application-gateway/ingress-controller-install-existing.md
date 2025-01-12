---
title: Mevcut bir Application Gateway giriş denetleyicisi oluşturma
description: Bu makalede, var olan bir Application Gateway Application Gateway giriş denetleyicisinin nasıl dağıtılacağı hakkında bilgi verilmektedir.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2d64766c754c0ea104ae83fde799a514e9da6d68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693740"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Mevcut bir Application Gateway kullanarak bir Application Gateway Ingcontroller (AGıC) yüklemesi

Application Gateway giriş denetleyicisi (AGIC), Kubernetes kümenizin içindeki bir pod.
AGIC, Kubernetes giriş [kaynaklarını izler](https://kubernetes.io/docs/concepts/services-networking/ingress/) ve Kubernetes kümesinin durumuna göre Application Gateway yapılandırması oluşturur ve uygular.

## <a name="outline"></a>Kod
- [Önkoşullar](#prerequisites)
- [Azure Resource Manager kimlik doğrulaması (ARM)](#azure-resource-manager-authentication)
    - Seçenek 1: [AAD-Pod kimliği ayarlama](#set-up-aad-pod-identity) ve kolda Azure kimliği oluşturma
    - 2. seçenek: [hizmet sorumlusu kullanma](#using-a-service-principal)
- [Held kullanarak giriş denetleyicisini yükler](#install-ingress-controller-as-a-helm-chart)
- [Çoklu küme/paylaşılan Application Gateway](#multi-cluster--shared-application-gateway): bir ortamda (Application Gateway bir veya daha fazla aks kümesi ve/veya diğer Azure bileşenleri arasında paylaşıldığında) agic 'yi yükler.

## <a name="prerequisites"></a>Önkoşullar
Bu belgede aşağıdaki araçların ve altyapının yüklü olduğu varsayılır:
- [Gelişmiş ağ](../aks/configure-azure-cni.md) özellikli [aks](https://azure.microsoft.com/services/kubernetes-service/) 'lar
- [Application Gateway v2](./tutorial-autoscale-ps.md) 'yi aks ile aynı sanal ağda
- AKS kümenizde yüklü [AAD Pod kimliği](https://github.com/Azure/aad-pod-identity)
- [Cloud Shell](https://shell.azure.com/) , `az` CLI, `kubectl` ve yüklü olan Azure kabuk ortamıdır `helm` . Aşağıdaki komutlar için bu araçlar gereklidir.

AGIC 'i yüklemeden önce lütfen __Application Gateway yapılandırmanızı yedekleyin__ :
  1. [Azure Portal](https://portal.azure.com/) kullanarak `Application Gateway` örneğinize gidin
  2. `Export template`tıklama`Download`

İndirdiğiniz ZIP dosyası, uygulama ağ geçidini geri yüklemek için kullanabileceğiniz JSON şablonlarına, bash ve PowerShell betiklerine sahip olur

## <a name="install-helm"></a>Helm'i yükleme
[Held](../aks/kubernetes-helm.md) , Kubernetes için bir paket yöneticisidir. Paketi yüklemek için bunu kullanacağız `application-gateway-kubernetes-ingress` .
Held 'yi yüklemek için [Cloud Shell](https://shell.azure.com/) kullanın:

1. [Held](../aks/kubernetes-helm.md) 'yi yükleyip hele paketi eklemek için aşağıdakileri çalıştırın `application-gateway-kubernetes-ingress` :

    - *Kubernetes RBAC etkin* AKS kümesi

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *Kubernetes RBAC devre dışı* AKS kümesi

    ```bash
    helm init
    ```

1. AGIC Held deposunu ekleyin:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager kimlik doğrulaması

AGIC, Kubernetes API sunucusu ve Azure Resource Manager ile iletişim kurar. Bu API 'Lere erişmek için bir kimlik gerektirir.

## <a name="set-up-aad-pod-identity"></a>AAD Pod kimliği ayarlama

[AAD Pod kimliği](https://github.com/Azure/aad-pod-identity) , aks 'de de çalışan agic 'e benzer bir denetleyicidir. Azure Active Directory kimliklerini Kubernetes pods 'nize bağlar. Kubernetes Pod içindeki bir uygulamanın diğer Azure bileşenleriyle iletişim kurabilmesi için kimlik gereklidir. Burada belirli bir durumda, [ARM](../azure-resource-manager/management/overview.md)için http istekleri yapmak üzere agic pod için yetkilendirmemiz gerekir.

Bu bileşeni AKS uygulamanıza eklemek için [AAD Pod kimlik yükleme yönergelerini](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) izleyin.

Bir sonraki adımda Azure kimliği oluşturmanız ve BT izinleri Kolonu vermeniz gerekir.
Aşağıdaki komutların tümünü çalıştırmak ve bir kimlik oluşturmak için [Cloud Shell](https://shell.azure.com/) kullanın:

1. **AKS düğümleri ile aynı kaynak grubunda** bir Azure kimliği oluşturun. Doğru kaynak grubunu seçmek önemlidir. Aşağıdaki komutta gerekli olan kaynak grubu, AKS portalı bölmesinde başvurulan bir *değildir* . Bu, `aks-agentpool` sanal makinelerin kaynak grubudur. Genellikle bu kaynak grubu ile başlar `MC_` ve AKS 'nizin adını içerir. Örneğin: `MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Aşağıdaki rol atama komutları için `principalId` Yeni oluşturulan kimliği edinmeniz gerekir:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. `Contributor`Application Gateway kimlik erişimi verin. Bunun için Application Gateway KIMLIĞI gerekir ve şuna benzer şekilde görünecektir: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    Aboneliğinizdeki Application Gateway kimliklerinin listesini ile alın: `az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. `Reader`Application Gateway kaynak grubuna kimlik erişimi verin. Kaynak grubu KIMLIĞI şöyle görünür: `/subscriptions/A/resourceGroups/B` . Tüm kaynak gruplarını şu ile edinebilirsiniz: `az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Hizmet sorumlusu kullanma
Ayrıca, bir Kubernetes gizli dizisi aracılığıyla ARM 'ye AGIC erişimi sağlamak mümkündür.

1. Active Directory hizmet sorumlusu oluşturun ve Base64 ile kodlayın. JSON blobunun Kubernetes 'e kaydedilmesi için Base64 kodlaması gereklidir.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Base64 ile kodlanmış JSON blobu `helm-config.yaml` dosyaya ekleyin. Hakkında daha fazla bilgi, `helm-config.yaml` sonraki bölümde yer alır.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>Giriş denetleyicisini Held grafiği olarak yükler
İlk birkaç adımda, Kubernetes kümenize helk 'in Tiller yüklersiniz. AGIC helk paketini yüklemek için [Cloud Shell](https://shell.azure.com/) kullanın:

1. `application-gateway-kubernetes-ingress`Held deposunu ekleme ve Held güncelleştirme gerçekleştirme

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. AGC-config. YAML 'yi indirin ve bu da AGIC 'yi yapılandırır:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Veya aşağıdaki YAML dosyasını kopyalayın: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Held-config. YAML 'yi düzenleyin ve ve değerlerini girin `appgw` `armAuth` .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>`Ve, `<identity-client-id>` önceki bölümde kurulum YAPTıĞıNıZ Azure AD kimliğinin özellikleridir. Şu komutu çalıştırarak bu bilgileri alabilirsiniz: `az identity show -g <resourcegroup> -n <identity-name>` , burada, `<resourcegroup>` en üst düzey aks küme nesnesinin, Application Gateway ve yönetilen tanımlanmasının dağıtıldığı kaynak grubudur.

1. `application-gateway-kubernetes-ingress`Önceki adımdan yapılandırma Ile Held grafiğini yükler `helm-config.yaml`

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    Alternatif olarak, `helm-config.yaml` ve Held komutunu tek bir adımda birleştirebilirsiniz:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Doğru şekilde başlatıldığını doğrulamak için yeni oluşturulan Pod 'un günlüğünü denetleyin

Azure Application Gateway kullanarak bir AKS hizmetini HTTP veya HTTPS üzerinden Internet 'te nasıl kullanıma sunabileceğiniz hakkında bilgi edinmek için [Bu nasıl yapılır kılavuzuna](ingress-controller-expose-service-over-http-https.md) bakın.



## <a name="multi-cluster--shared-application-gateway"></a>Çoklu küme/paylaşılan Application Gateway
Varsayılan olarak AGIC, bağlandığı Application Gateway tam sahipliğini varsayar. AGIC sürüm 0.8.0 ve üzeri, diğer Azure bileşenleriyle tek bir Application Gateway paylaşabilir. Örneğin, sanal makine ölçek kümesinde barındırılan bir uygulamanın yanı sıra AKS kümesi için de aynı Application Gateway kullanabiliriz.

Bu ayarı etkinleştirmeden önce lütfen __Application Gateway yapılandırmanızı yedekleyin__ :
  1. [Azure Portal](https://portal.azure.com/) kullanarak `Application Gateway` örneğinize gidin
  2. `Export template`tıklama`Download`

İndirdiğiniz ZIP dosyası, geri yüklemek için kullanabileceğiniz JSON şablonlarına, bash ve PowerShell betiklerine sahip olacaktır Application Gateway

### <a name="example-scenario"></a>Örnek Senaryo
İki Web sitesi için trafiği yöneten bir sanal Application Gateway göz atalım:
  - `dev.contoso.com` -Application Gateway ve AGIC kullanarak yeni bir AKS 'de barındırılan
  - `prod.contoso.com`-bir [Azure sanal makine ölçek kümesi](https://azure.microsoft.com/services/virtual-machine-scale-sets/) üzerinde barındırılan

Varsayılan ayarlarla AGIC, işaret ettiği Application Gateway %100 sahipliğini üstlenir. AGIC, tüm uygulama ağ geçidi yapılandırmasının üzerine yazar. `prod.contoso.com`(Application Gateway) için, Kubernetes girişi ile tanımlamadan el ile bir dinleyici oluştururuz, AGIC `prod.contoso.com` yapılandırmayı Saniyeler içinde silecek.

AGIC 'yi yüklemek ve ayrıca `prod.contoso.com` sanal makine ölçek kümesi makinelerimizden de hizmeti sağlamak IÇIN AGC 'yi yalnızca yapılandırmayla sınırlandırmamız gerekir `dev.contoso.com` . Bu, aşağıdaki [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)'yi oluşturarak kolaylaştıralınmıştır:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

Yukarıdaki komutu bir nesnesi oluşturur `AzureIngressProhibitedTarget` . Bu, (sürüm 0.8.0 ve üzeri) Application Gateway config 'in varlığını algılar `prod.contoso.com` ve bu ana bilgisayar adıyla ilgili herhangi bir yapılandırmanın değiştirilmesini önlemeye neden olur.


### <a name="enable-with-new-agic-installation"></a>Yeni AGIC yüklemesiyle etkinleştir
AGIC 'i (Version 0.8.0 ve üzeri) Application Gateway yapılandırmasının bir alt kümesiyle sınırlamak için `helm-config.yaml` şablonu değiştirin.
Bölümünün altına `appgw:` `shared` anahtar ekleyin ve öğesini olarak ayarlayın `true` .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

Held değişikliklerini Uygula:
  1. CRD 'nin yüklü olduğundan emin olun `AzureIngressProhibitedTarget` :
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Held 'yi güncelleştir:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Sonuç olarak, AKS 'ler adlı yeni bir örneğe sahip olur `AzureIngressProhibitedTarget` `prohibit-all-targets` :
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Adından da `prohibit-all-targets` anlaşılacağı gibi, *bir* ana bilgisayar ve yol IÇIN, agic 'in yapılandırmayı değiştirmesini yasaklar.
İle helk yüklemesi `appgw.shared=true` AGIC 'yi dağıtır, ancak Application Gateway hiçbir değişiklik yapmayacak.


### <a name="broaden-permissions"></a>İzinleri genişletin
Helk ile `appgw.shared=true` ve varsayılan `prohibit-all-targets` blokları hiçbir yapılandırmayı uygulamaktan itibaren.

İle AGIC izinlerini genişletin:
1. Özel kurulumla yeni bir oluşturma `AzureIngressProhibitedTarget` :
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Yalnızca kendi özel bir dileklerinizi oluşturduktan sonra, bu, çok geniş olan varsayılan olanı silebilirsiniz:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Mevcut bir AGIC yüklemesi için etkinleştirme
Zaten bir çalışan AKS, Application Gateway ve kümizdeki AGIC 'ler olduğunu varsayalım. İçin bir giriş yaptık `prod.contoso.com` ve AKS 'ten gelen trafiğe başarıyla hizmet veriyor. Mevcut Application Gateway eklemek istiyoruz `staging.contoso.com` , ancak bunu bir [VM](https://azure.microsoft.com/services/virtual-machines/)'de barındırmamız gerekiyor. Var olan Application Gateway yeniden kullanacağız ve için bir dinleyici ve arka uç havuzlarını el ile yapılandıracağız `staging.contoso.com` . Ancak Application Gateway config ( [Portal](https://portal.azure.com), [ARM API 'leri](/rest/api/resources/) veya [terkform](https://www.terraform.io/)aracılığıyla) için el ile davaklik, tam sahiplik varsayımlarıyla çakışır. Değişiklikler uygulandıktan sonra, AGIC onları üzerine yazar veya siler.

AGC 'nin bir yapılandırma alt kümesinde değişiklik yapmasını yasaklayabiliriz.

1. Bir `AzureIngressProhibitedTarget` nesne oluşturun:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Yeni oluşturulan nesneyi görüntüleyin:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Portal aracılığıyla Application Gateway yapılandırmasını değiştirme-dinleyicileri, yönlendirme kurallarını, arka uçları vb. ekleyin. Oluşturduğumuz yeni nesne ( `manually-configured-staging-environment` ), AGC 'nin ile ilgili yapılandırma Application Gateway üzerine yazılmasına izin vermez `staging.contoso.com` .
