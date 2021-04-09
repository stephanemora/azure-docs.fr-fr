---
title: Mises à jour de plateforme pour Azure VMware Solution
description: Découvrez les mises à jour de plateforme pour Azure VMware Solution.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: ce25df2cb221c032f6dd430f292522fe86e69ceb
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771768"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Mises à jour de plateforme pour Azure VMware Solution

Des mises à jour importantes pour Azure VMware Solution seront appliquées à partir de mars 2021. Vous recevrez une notification via Azure Service Health qui indiquera le calendrier de maintenance. Pour plus d’informations sur les processus et fonctionnalités de mise à niveau clés dans Azure VMware Solution, consultez [Mises à jour et mises à niveau des clouds privés Azure VMware Solution](concepts-upgrades.md).

## <a name="march-15-2021"></a>15 mars 2021 

- Le service Azure VMware Solution effectuera du travail de maintenance jusqu’au 19 mars 2021 pour mettre à jour le serveur vCenter dans votre cloud privé vers vCenter Server 6.7 Mise à jour version 3l.

- Pendant ce temps, VMware vCenter n’est pas disponible et vous ne pouvez pas gérer les machines virtuelles (arrêter, démarrer, créer, supprimer). La mise à l’échelle du Cloud privé (ajout/suppression de serveurs et de clusters) n’est pas non plus disponible. La haute disponibilité VMware (HA) continue à fonctionner pour assurer la protection des machines virtuelles existantes. 
 
Pour plus d’informations sur cette version de vCenter, consultez [Notes de mise à jour de VMware vCenter Server 6.7 Update 3l](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 mars 2021

- Azure VMware Solution appliquera des correctifs jusqu’au 15 mars 2021, à ESXi dans des clouds privés existants à [VMware ESXi 6.7, version de correctif ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html).

- Les solutions de contournement documentées pour la pile Sphere, comme pour [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), seront également appliquées jusqu’au 15 mars 2021.

>[!NOTE]
>Cette solution n’est pas disruptive et ne doit pas impacter les services ou charges de travail Azure VMware. Pendant la maintenance, plusieurs alertes VMware, comme _Lost network connectivity on DVPorts_ (Perte de la connectivité réseau sur les DVPorts) et _Lost uplink redundancy on DVPorts_ (Perte de la redondance de la liaison montante sur les DVPorts), s’affichent dans vCenter et disparaissent toutes seules à mesure que la maintenance progresse.

## <a name="post-update"></a>Après la mise à jour
Une fois la mise à jour terminée, vous devez voir les nouvelles versions des composants VMware s’afficher. Si vous rencontrez des problèmes ou si vous avez des questions, contactez notre équipe de support technique en ouvrant un ticket de support.





