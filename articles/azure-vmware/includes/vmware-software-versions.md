---
title: Versions des logiciels VMware
description: Versions logicielles de VMware prises en charge pour Azure VMware Solution.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 9a1781d163e67c2f9b6a4f756a616e59a969696f
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462510"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Les versions du logiciel VMware utilisées dans les nouveaux déploiements de clusters de clouds privés Azure VMware Solution sont les suivantes :

| Logiciel              |    Version   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T <br />**Remarque :** NSX-T est la seule version prise en charge de NSX.               |      2.5     |


De nouveaux clusters ont été ajoutés à un cloud privé existant, la version du logiciel en cours d’exécution est appliquée. Pour plus d’informations, consultez la [configuration requise pour la version du logiciel VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Les mises à niveau de l’offre logicielle groupée de cloud privé conservent dans la version de l’offre logicielle groupée la plus récente de VMware. Les versions de logiciels de cloud privé peuvent différer des versions les plus récentes des composants logiciels individuels (ESXi, NSX-T, vCenter, vSAN). Les stratégies et processus généraux de mise à niveau pour le logiciel de plateforme Azure VMware Solution sont décrits dans l’article [Mises à jour et mises à niveau des clouds privés](../concepts-upgrades.md).

