---
title: Versions des logiciels VMware
description: Versions logicielles de VMware prises en charge pour Azure VMware Solution.
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: a6441b55bbc6a8f694c50bbf022a6a2ae52d60bf
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097022"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Les versions du logiciel VMware utilisées dans les nouveaux déploiements de clusters de clouds privés Azure VMware Solution sont les suivantes :

| Logiciel              |    Version   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U3l    | 
| ESXi                  |    6.7 U3l    | 
| vSAN                  |    6.7 U3l    |
| NSX-T <br />**Remarque :** NSX-T est la seule version prise en charge de NSX.               |      3.1.1     |


De nouveaux clusters ont été ajoutés à un cloud privé existant, la version du logiciel en cours d’exécution est appliquée. Pour plus d’informations, consultez la [configuration requise pour la version du logiciel VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Les mises à niveau de l’offre logicielle groupée de cloud privé conservent dans la version de l’offre logicielle groupée la plus récente de VMware. Les versions de logiciels de cloud privé peuvent différer des versions les plus récentes des composants logiciels individuels (ESXi, NSX-T, vCenter, vSAN). Les stratégies et processus généraux de mise à niveau pour le logiciel de plateforme Azure VMware Solution sont décrits dans l’article [Mises à jour et mises à niveau des clouds privés](../concepts-upgrades.md).

