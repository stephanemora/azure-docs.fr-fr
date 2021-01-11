---
title: Versions des logiciels VMware
description: Versions logicielles de VMware prises en charge pour Azure VMware Solution.
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825092"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Les versions actuelles des logiciels VMware utilisés dans des clusters de cloud privé Azure VMware Solution sont les suivantes :

| Logiciel              |    Version   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>NSX-T est la seule version prise en charge de NSX.

Pour tout nouveau cluster dans un cloud privé, la version du logiciel correspond à ce qui est en cours d’exécution. Pour tout nouveau cloud privé dans un abonnement, la dernière version de la pile logicielle est installée. Pour plus d’informations, consultez la [configuration requise pour la version du logiciel VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Les mises à niveau de l’offre logicielle groupée de cloud privé conservent dans la version de l’offre logicielle groupée la plus récente de VMware. Les versions de logiciels de cloud privé peuvent différer des versions les plus récentes des composants logiciels individuels (ESXi, NSX-T, vCenter, vSAN). Les stratégies et processus généraux de mise à niveau pour le logiciel de plateforme Azure VMware Solution sont décrits dans l’article [Mises à jour et mises à niveau des clouds privés](../concepts-upgrades.md).

