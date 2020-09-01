---
title: Configurer vRealize Operations pour Azure VMware Solution (AVS)
description: Découvrez comment configurer vRealize Operations pour le cloud privé de votre solution VMware Azure.
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 729ee5c64776d7d04f702af62451175f7c53421b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750406"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Configurer vRealize Operations pour Azure VMware Solution (AVS)


vRealize Operations Manager est une plateforme de gestion des opérations qui permet aux administrateurs de l’infrastructure VMware de surveiller les ressources système. Ces ressources système peuvent être des objets au niveau de l’application ou de l’infrastructure (tant physiques que virtuels). Historiquement, la plupart des administrateurs VMware ont utilisé vRealize Operations pour surveiller et gérer les composants de cloud privé VMware : vCenter, ESXi, NSX-T, vSAN et Hybrid Cloud Extension (HCX). Chaque cloud privé Azure VMware Solution est approvisionné avec un déploiement dédié de vCenter, NSX-T, vSAN et HCX. 

Commencez par passer en revue minutieusement les sections [Avant de commencer](#before-you-begin) et [Prérequis](#prerequisites). Ensuite, nous vous guiderons dans les deux topologies de déploiement classiques pour vRealize Operations Manager avec Azure VMware Solution :

> [!div class="checklist"]
> * [vRealize Operations en local gérant le déploiement d’Azure VMware Solution](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [vRealize Operations en cours d’exécution sur le déploiement d’Azure VMware Solution](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Avant de commencer
* Consultez la [documentation du produit vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) pour en savoir plus sur le déploiement de vRealize Operations. 
* Consultez la [série de tutoriels](tutorial-network-checklist.md) Azure VMware Solution Software Defined Datacenter (SDDC).
* Vous pouvez également consulter la documentation du produit [vRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) pour en savoir plus sur l’option de déploiement local de vRealize Operations gérant Azure VMware Solution. 



## <a name="prerequisites"></a>Prérequis
* Vous devez configurer un VPN ou Azure ExpressRoute entre le SDDC local et celui d’Azure VMware Solution.
* Un cloud privé Azure VMware Solution a été déployé dans Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>vRealize Operations en local gérant le déploiement d’Azure VMware Solution
La plupart des clients disposent d’un déploiement local de vRealize Operations utilisé pour gérer un ou plusieurs domaines vCenter locaux. Lorsque des clients approvisionnent un nouveau cloud privé Azure VMware Solution dans Azure, ils connectent généralement leur environnement local à Azure VMware Solution à l’aide d’une instance ExpressRoute Azure ou d’une solution VPN de couche 3, comme indiqué ci-dessous.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="vRealize Operations en local gérant le déploiement d’Azure VMware Solution"  border="false":::

Pour étendre les capacités de vRealize Operations au cloud privé Azure VMware Solution, vous créez un adaptateur [pour les ressources de cloud privé Azure VMware Solution](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html), afin de collecter des données à partir du cloud privé Azure VMware Solution et de les placer dans l’instance vRealize Operations locale. L’instance vRealize Operations Manager locale peut se connecter directement au gestionnaire de vCenter et NSX-T sur Azure VMware Solution, ou vous pouvez déployer un collecteur vRealize Operations Remote Collector sur le cloud privé Azure VMware Solution. vRealize Operations Remote Collector compresse et chiffre les données collectées à partir du cloud privé Azure VMware Solution avant qu’elles soient envoyées via le réseau VPN ou ExpressRoute à vRealize Operations Manager s’exécutant localement. 

> [!TIP]
> Pour obtenir des instructions pas à pas sur l’installation de vRealize Operations Manager, reportez-vous à la [documentation de VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html). 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>vRealize Operations en cours d’exécution sur un déploiement d’Azure VMware Solution

Une autre option de déploiement consiste à déployer une instance de vRealize Operations Manager sur l’un des clusters vSphere du cloud privé Azure VMware Solution, comme indiqué ci-dessous. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="vRealize Operations en cours d’exécution sur un Azure VMware Solution" border="false":::

Après le déploiement de l’instance Azure VMware Solution de vRealize Operations, vous pouvez configurer vRealize Operations pour collecter des données à partir de vCenter, ESXi, NSX-T, vSAN et HCX. 

> [!TIP]
> Pour obtenir des instructions pas à pas sur l’installation de vRealize Operations Manager, reportez-vous à la [documentation de VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html).




<!-- LINKS - external -->


<!-- LINKS - internal -->




