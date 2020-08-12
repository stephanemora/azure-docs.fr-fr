---
title: Configurer vRealize Operations pour Azure VMware Solution (AVS)
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475831"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>Configurer vRealize Operations pour Azure VMware Solution (AVS)


vRealize Operations Manager est une plateforme de gestion des opérations qui permet aux administrateurs de l’infrastructure VMware de surveiller les ressources système. Ces ressources système peuvent être des objets au niveau de l’application ou de l’infrastructure (tant physiques que virtuels). Historiquement, la plupart des administrateurs VMware ont utilisé vRealize Operations pour surveiller et gérer les composants de cloud privé VMware : vCenter, ESXi, NSX-T, vSAN et Hybrid Cloud Extension (HCX). Chaque cloud privé AVS est approvisionné par un déploiement vCenter, NSX-T, vSAN et HCX dédié. 

Commencez par passer en revue minutieusement les sections [Avant de commencer](#before-you-begin) et [Prérequis](#prerequisites). Ensuite, nous vous guiderons à travers les deux topologies de déploiement classiques pour vRealize Operations Manager avec AVS :

> [!div class="checklist"]
> * [Déploiement local de vRealize Operations gérant AVS](#on-premises-vrealize-operations-managing-avs-deployment)
> * [Déploiement de vRealize Operations s’exécutant sur AVS](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>Avant de commencer
* Consultez la [documentation du produit vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) pour en savoir plus sur le déploiement de vRealize Operations. 
* Passez en revue la [série de didacticiels](tutorial-network-checklist.md) de base relative au centre de données défini par logiciel (SDDC) d’AVS.
* Si vous le souhaitez, examinez la documentation du produit [vRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) pour en savoir plus sur l’option Déploiement local de vRealize Operations gérant AVS. 



## <a name="prerequisites"></a>Prérequis
* Un VPN ou Azure ExpressRoute doit être configuré entre le SDDC local et celui d’AVS.
* Un cloud privé AVS a été déployé dans Azure.



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>Déploiement local de vRealize Operations gérant AVS
La plupart des clients disposent d’un déploiement local de vRealize Operations utilisé pour gérer un ou plusieurs domaines vCenter locaux. Lorsque les clients approvisionnent un nouveau cloud privé AVS dans Azure, ils connectent généralement leur environnement local à AVS à l’aide d’une instance ExpressRoute Azure ou d’une solution VPN de couche 3, comme indiqué ci-dessous.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Déploiement local de vRealize Operations gérant AVS"  border="false":::

Pour étendre les capacités de vRealize Operations au cloud privé AVS, vous créez un adaptateur [pour les ressources de cloud privé AVS](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html), afin de collecter des données à partir du cloud privé AVS et de les placer dans l’instance vRealize Operations locale. L’instance vRealize Operations Manager locale peut se connecter directement au gestionnaire de vCenter et NSX-T sur AVS, ou vous pouvez déployer un collecteur vRealize Operations Remote Collector sur le cloud privé AVS. vRealize Operations Remote Collector compresse et chiffre les données collectées à partir du cloud privé AVS avant qu’elles ne soient envoyées via le réseau VPN ou ExpressRoute à vRealize Operations Manager s’exécutant localement. 

> [!TIP]
> Pour obtenir des instructions pas à pas sur l’installation de vRealize Operations Manager, reportez-vous à la [documentation de VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html). 



## <a name="vrealize-operations-running-on-avs-deployment"></a>Déploiement de vRealize Operations s’exécutant sur AVS

Une autre option de déploiement consiste à déployer une instance de vRealize Operations Manager sur l’un des clusters vSphere du cloud privé AVS, comme indiqué ci-dessous. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Déploiement de vRealize Operations s’exécutant sur AVS" border="false":::

Après le déploiement de l’instance AVS de vRealize Operations, vous pouvez configurer vRealize Operations pour collecter des données à partir de vCenter, ESXi, NSX-T, vSAN et HCX. 

> [!TIP]
> Pour obtenir des instructions pas à pas sur l’installation de vRealize Operations Manager, reportez-vous à la [documentation de VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html).




<!-- LINKS - external -->


<!-- LINKS - internal -->




