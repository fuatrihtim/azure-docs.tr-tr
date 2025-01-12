---
title: Azure VM 'lerinde SAP MaxDB, liveCache ve Content Server dağıtımı | Microsoft Docs
description: Azure 'da SAP MaxDB, liveCache ve Content Server dağıtımı
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e15b592aaa780ea80b6931c6d44fbf99e2f3a730
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673060"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>Azure VM 'lerinde SAP MaxDB, liveCache ve Content Server dağıtımı

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




Bu belge, Azure IaaS 'de MaxDB, liveCache ve Content Server dağıtımında dikkate alınması gereken birkaç farklı alanı ele alır. Bu belgenin bir önkoşulu olarak, [SAP iş yükü Için Azure sanal MAKINELERI DBMS dağıtımı](dbms_guide_general.md) ve [Azure belgelerindeki SAP iş yükündeki](./get-started.md)diğer kılavuzlar hakkında belge konularını okuduğunuzdan de okumalısınız. 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Windows üzerinde SAP MaxDB dağıtımları için Ayrıntılar
### <a name="sap-maxdb-version-support-on-azure"></a>Azure 'da SAP MaxDB sürüm desteği
SAP, Azure 'da SAP NetWeaver tabanlı ürünlerle birlikte kullanılmak üzere SAP MaxDB sürüm 7,9 veya üstünü desteklemektedir. SAP NetWeaver tabanlı ürünlerle birlikte kullanılacak SAP MaxDB Server için tüm güncelleştirmeler veya JDBC ve ODBC sürücüleri yalnızca tarihinde SAP hizmeti Marketi aracılığıyla sağlanır <https://support.sap.com/swdc> .
SAP NetWeaver ile SAP MaxDB üzerinde çalışan genel bilgiler, adresinde bulunabilir <https://www.sap.com/community/topic/maxdb.html> .

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>SAP MaxDB DBMS için desteklenen Microsoft Windows sürümleri ve Azure VM türleri
Azure 'da SAP MaxDB DBMS için desteklenen Microsoft Windows sürümünü bulmak için bkz.:

* [SAP ürün kullanılabilirliği matrisi (PAM)][sap-pam]
* SAP Note [1928533]

Microsoft Windows 2016 ' deki en yeni işletim sistemi Microsoft Windows sürümünün kullanılması kesinlikle önerilir.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>MaxDB için kullanılabilir SAP MaxDB belgeleri
SAP MaxDB belgelerinin güncelleştirilmiş listesini şu SAP notunda bulabilirsiniz [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM 'lerinde SAP yüklemeleri için SAP MaxDB yapılandırma yönergeleri
#### <a name="storage-configuration"></a><a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Depolama yapılandırması
SAP MaxDB için Azure Storage en iyi uygulamaları, [RDBMS dağıtımları için bir sanal makinenin Bölüm depolama yapısında](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)bahsedilen genel önerileri izler.

> [!IMPORTANT]
> Diğer veritabanları gibi SAP MaxDB de veri ve günlük dosyaları içerir. Ancak, SAP MaxDB terimlerinde doğru terim "Volume" ("dosya" değil) olarak ayarlanır. Örneğin, SAP MaxDB veri birimleri ve günlük birimleri vardır. Bunları işletim sistemi disk birimleriyle karıştırmayın. 
> 
> 

Kısa bir süre içinde şunları yapmanız gerekir:

* Azure depolama hesapları kullanıyorsanız, SAP MaxDB verilerini ve günlük birimlerini (veri ve günlük dosyaları) tutan Azure Depolama hesabını, [SAP iş yükü Için Azure sanal MAKINELERI DBMS dağıtımı hakkında önemli noktalar](dbms_guide_general.md)bölümünde belirtildiği gibi **Yerel yedekli depolama (LRS)** olarak ayarlayın.
* SAP MaxDB veri birimleri (veri dosyaları) için GÇ yolunu, günlük birimleri (günlük dosyaları) için GÇ yolundan ayırın. SAP MaxDB veri birimlerinin (veri dosyaları) bir mantıksal sürücüde yüklü olması ve SAP MaxDB günlük birimlerinin (günlük dosyaları) başka bir mantıksal sürücüde yüklü olması gerektiği anlamına gelir.
* SAP MaxDB verileri veya günlük birimleri (veri ve günlük dosyaları) için kullanıp kullanmayacağınızı ve Azure Standard ya da Azure Premium depolamayı kullanıp kullanmayacağınızı, [SAP iş yükü Için Azure sanal MAKINELERI DBMS dağıtımı hakkında önemli noktalar](dbms_guide_general.md)bölümünde açıklandığı gibi, her bir disk için uygun önbelleğe alma türünü ayarlayın.
* Her disk için geçerli ıOPS kotası gereksinimleri karşılıyorsa, tüm veri birimlerinin tek bir bağlı diskte depolanması ve ayrıca tüm veritabanı günlük hacimlerini tek bir bağlı diskte depolamak mümkündür.
* Daha fazla ıOPS ve/veya Space gerekliyse, birden çok bağlı disk üzerinde bir büyük mantıksal cihaz oluşturmak için Microsoft pencere depolama havuzlarını (yalnızca Microsoft Windows Server 2012 ve üzeri sürümlerde bulunur) kullanmanız önerilir. Daha ayrıntılı bilgi için bkz. [SAP iş yükü Için Azure sanal MAKINELER DBMS dağıtımı hakkında konular](dbms_guide_general.md). Bu yaklaşım, disk alanını yönetmek için yönetim yükünü basitleştirir ve dosyaları birden çok bağlı diskte el ile dağıtmanın çabasını önler.
* MaxDB dağıtımları için Azure Premium Storage kullanılması önemle önerilir. 

![SAP MaxDB DBMS için Azure IaaS VM 'sinin başvuru yapılandırması](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="backup-and-restore"></a><a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Yedekleme ve geri yükleme
SAP MaxDB 'yi Azure 'a dağıttığınızda, yedekleme yöntemlerinizi gözden geçirmeniz gerekir. Sistem, bir üretken sistem olmasa bile SAP MaxDB tarafından barındırılan SAP veritabanının düzenli aralıklarla yedeklenmesi gerekir. Azure depolama üç görüntü koruduğundan, artık sisteminizi depolama hatalarına karşı korumak ve daha önemli işlemler veya yönetim hataları bakımından bir yedekleme daha az önemlidir. Doğru bir yedekleme ve geri yükleme planının korunmasının birincil nedeni, bir noktadan noktaya kurtarma özelliği sağlayarak mantıksal veya el ile hatalar için telafi sağlayabilmenizi sağlamaktır. Böylece hedef, veritabanını belirli bir noktaya geri yüklemek ya da mevcut veritabanını kopyalayarak başka bir sistemi temel almak için Azure 'daki yedeklemeleri kullanmak üzere yedeklemeleri kullanmaktır. 

Azure 'da bir veritabanını yedekleme ve geri yükleme, şirket içi sistemler için yaptığı gibi çalışır. böylece, SAP Note [767598]' de listelenen SAP MaxDB belge belgelerinden birinde AÇıKLANAN standart sap MaxDB yedekleme/geri yükleme araçları kullanabilirsiniz. 

#### <a name="performance-considerations-for-backup-and-restore"></a><a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Yedekleme ve geri yükleme için performans konuları
Çıplak dağıtımlarda olduğu gibi, yedekleme ve geri yükleme performansı, kaç birimin paralel olarak okunmayacağına ve bu birimlerin üretilen iş hızına bağlıdır. Bu nedenle, biri şunları varsayabilir:

* Veritabanı cihazlarını depolamak için kullanılan disk sayısı ne kadar az olursa, genel okuma performansı düşüktür
* Yedeklemenin yazılacağı daha az hedef (dizili dizinler, diskler), aktarım hızı düşük

Yazılacak hedef sayısını artırmak için, gereksinimlerinize bağlı olarak, büyük olasılıkla birlikte kullanabileceğiniz iki seçenek vardır:

* Yedekleme için ayrı birimleri ayırma
* Bu şeritli disk biriminde ıOPS verimini artırmak için birden çok bağlı disk üzerinde yedekleme hedefi birimini şeritli şekilde sıralama
* İçin ayrı ayrılmış mantıksal disk cihazları vardır:
  * SAP MaxDB yedekleme birimleri (örn. dosyalar)
  * SAP MaxDB veri birimleri (örn. dosyalar)
  * SAP MaxDB günlük birimleri (örn. dosyalar)

Birden çok bağlı disk üzerinde bir birimin şeridi oluşturma, daha önce [SAP iş yükü Için Azure sanal MAKINELERI DBMS dağıtımı açısından önemli](dbms_guide_general.md)bir şekilde ele alınmıştır. 

#### <a name="other-considerations"></a><a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Diğer önemli noktalar
Azure kullanılabilirlik kümeleri veya SAP izleme gibi diğer tüm genel alanlarda Ayrıca, [SAP iş yükü Için Azure sanal MAKINELER DBMS dağıtımı hakkında önemli noktalar](dbms_guide_general.md)açıklanmaktadır.  SAP MaxDB veritabanına sahip VM dağıtımları için.
Diğer SAP MaxDB 'ye özgü ayarlar Azure VM 'lerine saydamdır ve SAP Note [767598] ve bu SAP notlarında listelenen farklı belgelerde açıklanmıştır:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Windows üzerinde SAP liveCache dağıtımları için Ayrıntılar
### <a name="sap-livecache-version-support"></a>SAP liveCache sürüm desteği
Azure sanal makinelerinde desteklenen SAP liveCache 'in en düşük sürümü SAP **LC/LCAPPS 10,0 SP 25** ve **livecache 7.9.08.31** ve **LCA-Build 25**, sap scm 7,0 ve üzeri sürümler için **EHP 2** ' de kullanıma sunuldu.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>SAP liveCache DBMS için desteklenen Microsoft Windows sürümleri ve Azure VM türleri
Azure 'da SAP liveCache için desteklenen Microsoft Windows sürümünü bulmak için, bkz.:

* [SAP ürün kullanılabilirliği matrisi (PAM)][sap-pam]
* SAP Note [1928533]

İşletim sisteminin Microsoft Windows Server 'ın en yeni sürümünü kullanmanız önemle önerilir. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM 'lerinde SAP yüklemeleri için SAP liveCache yapılandırma yönergeleri
#### <a name="recommended-azure-vm-types-for-livecache"></a>LiveCache için önerilen Azure VM türleri
SAP liveCache çok büyük hesaplamalar gerçekleştiren bir uygulama olduğundan, RAM ve CPU 'nun miktarı ve hızı SAP liveCache performansı üzerinde önemli bir etki içerir. 

SAP tarafından desteklenen Azure VM türleri için (SAP Note [1928533]), VM 'ye ayrılan tüm sanal CPU kaynakları hiper yöneticinin ADANMıŞ fiziksel CPU kaynakları tarafından desteklenir. Aşırı sağlama yok (ve bu nedenle CPU kaynakları için yarışma yok).

Benzer şekilde, SAP tarafından desteklenen tüm Azure VM örnek türleri için VM belleği, %100, örneğin, kaynak üzerinde sağlama (daha fazla taahhüt) ile fiziksel belleğe eşlendi.

Bu perspektiften en son dv2, Dv3, Ev3 ve d serisi VM 'Lerin kullanılması kesinlikle önerilir. Farklı VM türlerinin seçimi, liveCache ve ihtiyacınız olan CPU kaynakları için gereken belleğe bağlıdır. Diğer tüm DBMS dağıtımlarında olduğu gibi, performans açısından kritik birimlerde Azure Premium Storage 'ın kullanılması önerilir.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Azure 'da liveCache için depolama yapılandırması
SAP liveCache SAP MaxDB teknolojisini temel aldığı için, bu belgede açıklanan SAP MaxDB için belirtilen tüm Azure depolama en iyi yöntem önerileri SAP liveCache için de geçerlidir. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>LiveCache için adanmış Azure VM senaryosu
SAP liveCache yoğun bir şekilde hesaplama gücü kullandığından, üretken bir kullanım için adanmış bir Azure sanal makinesine dağıtılması önemle önerilir. 

![Üretken kullanım durumu için liveCache için adanmış Azure VM](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Azure 'da liveCache için yedekleme ve geri yükleme
performans konuları da dahil olmak üzere yedekleme ve geri yükleme bu belgedeki ilgili SAP MaxDB bölümünde zaten açıklanmıştır. 

#### <a name="other-considerations"></a>Diğer önemli noktalar
Diğer tüm genel bölgeler, ilgili SAP MaxDB bölümünde zaten açıklanmıştır. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Azure 'da Windows üzerinde SAP Içerik sunucusu dağıtımının özellikleri
SAP Içerik sunucusu, elektronik belgeler gibi içeriği farklı biçimlerde depolamak için ayrı, sunucu tabanlı bir bileşendir. SAP Içerik sunucusu teknoloji geliştirmesi tarafından sağlanır ve tüm SAP uygulamaları için çapraz uygulama kullanılır. Ayrı bir sisteme yüklenir. Tipik içerik, bilgi ambarı veya mySAP PLM belge yönetim sisteminden kaynaklanan teknik çizimlerden alınan eğitim malzemeleridir. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Azure VM 'Ler için SAP Content Server sürüm desteği
SAP Şu anda şunları destekler:

* Sürüm **6,50 (ve üzeri)** Ile **SAP içerik sunucusu**
* **SAP MaxDB sürüm 7,9**
* **Microsoft IIS (Internet Information Server) sürüm 8,0 (ve üzeri)**

En yeni SAP Içerik sunucusu sürümünün ve en yeni **MICROSOFT IIS** sürümünün kullanılması önemle önerilir. 

SAP [ürün kullanılabilirliği matrisinde (Pam)][sap-pam]SAP içerik sunucusu ve Microsoft IIS 'nin desteklenen son sürümlerini denetleyin.

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>SAP Içerik sunucusu için desteklenen Microsoft Windows ve Azure VM türleri
Azure 'da SAP Içerik sunucusu için desteklenen Windows sürümü hakkında bilgi edinmek için bkz.:

* [SAP ürün kullanılabilirliği matrisi (PAM)][sap-pam]
* SAP Note [1928533]

En yeni Microsoft Windows Server sürümünü kullanmanız önemle önerilir.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM 'lerinde SAP yüklemeleri için SAP Içerik sunucusu yapılandırma yönergeleri
#### <a name="storage-configuration-for-content-server-in-azure"></a>Azure 'da Içerik sunucusu için depolama yapılandırması
SAP Içerik sunucusunu dosyaları SAP MaxDB veritabanında depolamak üzere yapılandırırsanız, bu belgede SAP MaxDB için belirtilen tüm Azure depolama en iyi yöntemler önerisi de SAP Içerik sunucusu senaryosu için geçerlidir. 

Dosya sistemindeki dosyaları depolamak için SAP Içerik sunucusunu yapılandırırsanız, ayrılmış bir mantıksal sürücü kullanmanız önerilir. Windows depolama alanları 'nın kullanılması, [SAP iş yükü Için Azure sanal MAKINELER DBMS dağıtımı hakkında konular](dbms_guide_general.md)bölümünde açıklandığı gibi mantıksal disk boyutunu ve IOPS verimini de artırmanıza olanak sağlar. 

#### <a name="sap-content-server-location"></a>SAP Içerik sunucusu konumu
SAP Içerik sunucusunun, SAP sisteminin dağıtıldığı aynı Azure bölgesinde ve Azure VNET 'te dağıtılması gereklidir. SAP Içerik sunucusu bileşenlerini adanmış bir Azure VM 'de veya SAP sisteminin çalıştığı sanal makinede dağıtmak isteyip istemediğinize karar verebilirsiniz. 

![SAP Içerik sunucusu için adanmış Azure VM](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>SAP önbellek sunucusu konumu
SAP önbellek sunucusu, (önbelleğe alınmış) belgelere yerel olarak erişim sağlamak için ek bir sunucu tabanlı bileşendir. SAP önbellek sunucusu, SAP Içerik sunucusunun belgelerini önbelleğe alır. Bu, belgelerin farklı konumlardan birden çok kez alınması gerekiyorsa ağ trafiğini iyileştirmelidir. Genel kural SAP önbellek sunucusunun SAP önbellek sunucusuna erişen istemciye fiziksel olarak yakın olması gereken bir uygulamadır. 

Burada iki seçeneğiniz vardır:

1. **İstemci bir arka uç SAP sistemidir** SAP Içerik sunucusuna erişmek için bir arka uç SAP sistemi yapılandırılmışsa, bu SAP sistemi bir istemcdir. Hem SAP sistemi hem de SAP Içerik sunucusu aynı Azure bölgesinde dağıtıldığından aynı Azure veri merkezinde aynı Azure bölgesinde dağıtılır. Bunlar birbirlerine fiziksel olarak yakınlardır. Bu nedenle, adanmış bir SAP önbellek sunucusuna sahip olmanız gerekmez. SAP kullanıcı arabirimi istemcileri (SAP GUI veya Web tarayıcısı) SAP sistemine doğrudan erişir ve SAP sistemi, belgeleri SAP Içerik sunucusundan alır.
2. **İstemci, şirket içi bir web tarayıcısıdır** SAP Içerik sunucusuna Web tarayıcısı tarafından doğrudan erişilecek şekilde yapılandırılabilir. Bu durumda, şirket içinde çalışan bir Web tarayıcısı SAP Içerik sunucusunun bir istemcsahiptir. Şirket içi veri merkezi ve Azure veri merkezi, farklı fiziksel konumlara yerleştirilir (ideal olarak birbirlerine yakın). Şirket içi veri merkeziniz, Azure 'dan siteye VPN veya ExpressRoute aracılığıyla Azure 'a bağlanır. Her iki seçenek de Azure 'a güvenli VPN ağı bağlantısı sunmakla birlikte, siteden siteye ağ bağlantısı, şirket içi veri merkezi ve Azure veri merkezi arasında bir ağ bant genişliği ve gecikme SLA sunmamaktadır. Belgelere erişimi hızlandırmak için aşağıdakilerden birini yapabilirsiniz:
   1. SAP önbellek sunucusunu şirket içinde yükler, şirket içi Web tarayıcısına kapatın (aşağıdaki şekilde seçeneğe gidin)
   2. Şirket içi veri merkezi ve Azure veri merkezi arasında yüksek hızlı ve düşük gecikmeli bir ayrılmış ağ bağlantısı sunan Azure ExpressRoute 'u yapılandırın.

![Şirket içi SAP önbellek sunucusu yüklemek için seçenek](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Yedekleme/geri yükleme
SAP Içerik sunucusunu, dosyaları SAP MaxDB veritabanında depolayacak şekilde yapılandırırsanız, yedekleme/geri yükleme yordamı ve performans konuları bu belgenin SAP MaxDB bölümleri bölümünde zaten açıklanmıştır. 

Dosya sistemindeki dosyaları depolamak için SAP Içerik sunucusunu yapılandırırsanız, tek bir seçenek, belgelerin bulunduğu tüm dosya yapısının el ile yedeklenmesini/geri yüklenmesini yürütmaktır. SAP MaxDB yedekleme/geri yükleme işlemine benzer şekilde, yedekleme amacı için özel bir disk biriminiz olması önerilir. 

#### <a name="other"></a>Diğer
Diğer SAP Içerik sunucusuna özgü ayarlar Azure VM 'lerine saydamdır ve çeşitli belgelerde ve SAP notlarında açıklanmıştır:

* <https://service.sap.com/contentserver> 
* SAP Note [1619726]  
