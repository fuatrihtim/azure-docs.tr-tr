---
title: Azure Red Hat OpenShift kümesinde Prometheus örneğini dağıtma
description: Uygulamanızın ölçümlerini izlemek için bir Azure Red Hat OpenShift kümesinde bir Prometheus örneği oluşturun.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/17/2019
keywords: Prometheus, Aro, OpenShift, ölçümler, Red Hat
ms.openlocfilehash: 974096e515401ab5e292dbad4c9beba7f1611a8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633453"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift kümesine tek başına Prometheus örneği dağıtma

> [!IMPORTANT]
> Azure Red Hat Openshıft 3,11, 30 Haziran 2022 tarihinde kullanımdan kaldırılacaktır. Yeni Azure Red Hat OpenShift 3,11 kümelerinin oluşturulmasına yönelik destek, 30 Kasım 2020 ile devam eder. Aşağıdaki kullanımdan kaldırma, kalan Azure Red Hat Openshıft 3,11 kümeleri güvenlik açıklarını engellemek için kapatılacak.
> 
> [Azure Red Hat OpenShift 4 kümesi oluşturmak](tutorial-create-cluster.md)için bu kılavuzu izleyin.
> Belirli sorularınız varsa [lütfen bizimle iletişime geçin](mailto:arofeedback@microsoft.com).

Bu makalede, Azure Red Hat OpenShift kümesinde hizmet bulmayı kullanan tek başına bir Prometheus örneğinin nasıl yapılandırılacağı açıklanır.

> [!NOTE]
> Azure Red Hat OpenShift kümesine müşteri yöneticisi erişimi gerekli değildir.

Hedef Kurulum:

- Prometheus ve Alertmanager içeren bir proje (Prometheus-Project).
- İzlenecek uygulamaları içeren iki proje (App-Project1 ve App-Project2).

Bazı Prometheus yapılandırma dosyalarını yerel olarak hazırlarsınız. Depolamak için yeni bir klasör oluşturun. Yapılandırma dosyaları, daha sonra kümeye daha sonra eklenmeleri durumunda, kümede gizli dizi olarak depolanır.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>OC aracını kullanarak kümede oturum açma

1. Bir Web tarayıcısı açın ve ardından kümenizin web konsoluna gidin ( https://openshift .*Rastgele kimlik*. *Region*. azmosa.io).
2. Azure kimlik bilgilerinizle oturum açın.
3. Sağ üst köşedeki Kullanıcı adınızı seçin ve ardından **oturumu Kopyala komutunu** seçin.
4. Kullanıcı adınızı kullanacağınız terminale yapıştırın.

> [!NOTE]
> Doğru kümede oturum açmış olup olmadığınız görmek için `oc whoami -c` komutunu çalıştırın.

## <a name="prepare-the-projects"></a>Projeleri Hazırlama

Projeleri oluşturmak için aşağıdaki komutları çalıştırın:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Ya da parametresini kullanabilir ya `-n` da `--namespace` komutunu çalıştırarak etkin bir proje seçebilirsiniz `oc project` .

## <a name="prepare-the-prometheus-configuration-file"></a>Prometheus yapılandırma dosyasını hazırlama
Aşağıdaki içeriği girerek bir Prometheus. yml dosyası oluşturun:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Aşağıdaki yapılandırmayı girerek, senet adlı bir gizli dizi oluşturun:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Prometheus. yıml dosyası, temel bir Prometheus yapılandırma dosyasıdır. Aralıkları ayarlar ve üç projede otomatik bulmayı yapılandırır (Prometheus-Project, App-Project1, App-Project2). Önceki yapılandırma dosyasında, otomatik keşfedilen uç noktalar kimlik doğrulaması olmadan HTTP üzerinden engellenir.

Scraping uç noktaları hakkında daha fazla bilgi için bkz. [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Alertmanager yapılandırma dosyasını hazırlama
Aşağıdaki içeriği girerek bir alertmanager. yml dosyası oluşturun:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Aşağıdaki yapılandırmayı girerek Prom-Alerts adlı bir gizli dizi oluşturun:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager. yıml, uyarı Yöneticisi yapılandırma dosyasıdır.

> [!NOTE]
> Önceki iki adımı doğrulamak için `oc get secret -n prometheus-project` komutunu çalıştırın.

## <a name="start-prometheus-and-alertmanager"></a>Prometheus ve Alertmanager 'ı başlatma
[OpenShift/Origin deposuna](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) gidin ve [Prometheus-standalone. YAML](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) şablonunu indirin. Aşağıdaki yapılandırmayı girerek şablonu Prometheus-Project ' e uygulayın:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Prometheus-standalone. YAML dosyası bir OpenShift şablonudur. Bunun önünde OAuth-proxy ve ayrıca, OAuth-proxy ile güvenliği sağlanmış bir Alertmanager örneği ile bir Prometheus örneği oluşturur. Bu şablonda, OAuth-proxy, Prometheus-Project ad alanını "alabilirler" olan tüm kullanıcılara izin verecek şekilde yapılandırılmıştır ( `-openshift-sar` bayrağa bakın).

> [!NOTE]
> Senet 'nin Istenen ve geçerlI sayı çoğaltmalarının eşit olup olmadığını doğrulamak için `oc get statefulset -n prometheus-project` komutunu çalıştırın. Projedeki tüm kaynakları denetlemek için `oc get all -n prometheus-project` komutunu çalıştırın.

## <a name="add-permissions-to-allow-service-discovery"></a>Hizmet bulmaya izin vermek için izinler ekleme

Aşağıdaki içeriği girerek bir Prometheus-sdrole. yml dosyası oluşturun:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Şablonu, hizmet bulmaya izin vermek istediğiniz tüm projelere uygulamak için aşağıdaki komutları çalıştırın:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Rol ve RoleBinding 'in doğru bir şekilde oluşturulduğunu doğrulamak için `oc get role` ve komutlarını çalıştırın `oc get rolebinding` .

## <a name="optional-deploy-example-application"></a>İsteğe bağlı: örnek uygulama dağıtma

Her şey çalışıyor ancak ölçüm kaynağı yok. Prometheus URL 'sine gidin ( https://prom-prometheus-project.apps .*Rastgele kimlik*. *Region*. azmosa.io/). Aşağıdaki komutu kullanarak bulabilirsiniz:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Ana bilgisayar adının başına https://önekini eklemeyi unutmayın.

**Durum > hizmeti bulma** sayfası, 0/0 etkin hedefleri gösterecektir.

/Ölçümler uç noktası altında temel Python ölçümleri sunan örnek bir uygulama dağıtmak için aşağıdaki komutları çalıştırın:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Yeni uygulamalar, dağıtımdan sonra 30 saniye içinde hizmet bulma sayfasında geçerli hedef olarak görünmelidir.

Daha fazla ayrıntı için **durum**  >  **hedefleri**' ni seçin.

> [!NOTE]
> Prometheus, başarıyla her başarılı bir veri noktası ekler. Sol üst köşedeki **Prometheus** ' ı seçin, **ifade olarak yazın** ve ardından **Yürüt**' ü seçin.

## <a name="next-steps"></a>Sonraki adımlar

Uygulamalarınıza özel Prometheus izleme ekleyebilirsiniz. Prometheus ölçüm hazırlanmasını kolaylaştıran Prometheus Istemci kitaplığı, farklı programlama dilleri için hazırlayın.

Daha fazla bilgi için bkz. aşağıdaki GitHub kitaplıkları:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Git](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
