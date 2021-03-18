---
title: Mises à jour de plateforme pour Azure VMware Solution
description: Découvrez les mises à jour de plateforme pour Azure VMware Solution.
ms.topic: reference
ms.date: 03/05/2021
ms.openlocfilehash: 1f1a0c29ffde20b54abb9e4d1d1127fc93a712d9
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444012"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Mises à jour de plateforme pour Azure VMware Solution


## <a name="march-4-2021"></a>4 mars 2021

Des mises à jour importantes pour Azure VMware Solution seront appliquées à partir de mars 2021. Vous recevrez une notification via Azure Service Health qui indiquera le calendrier de maintenance. Cet article fournit des informations sur ce qui se passe au cours de cette opération de maintenance, ainsi que sur les modifications apportées à votre cloud privé.

- Azure VMware Solution appliquera des patchs ESXi dans les clouds privés existants ([VMware ESXi 6.7, version de correctif ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html)) jusqu’au 15 mars 2021.

- Les solutions de contournement documentées pour la pile Sphere, comme pour [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), seront également appliquées jusqu’au 15 mars 2021.

>[!NOTE]
>Cette solution n’est pas disruptive et ne doit pas impacter les services ou charges de travail Azure VMware. Pendant la maintenance, plusieurs alertes VMware, comme _Lost network connectivity on DVPorts_ (Perte de la connectivité réseau sur les DVPorts) et _Lost uplink redundancy on DVPorts_ (Perte de la redondance de la liaison montante sur les DVPorts), s’affichent dans vCenter et disparaissent toutes seules à mesure que la maintenance progresse.


## <a name="post-update"></a>Après la mise à jour
Une fois la mise à jour terminée, vous devez voir les nouvelles versions des composants VMware s’afficher. Si vous rencontrez des problèmes ou si vous avez des questions, contactez notre équipe de support technique en ouvrant un ticket de support.



