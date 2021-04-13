---
title: Mises à jour de plateforme pour Azure VMware Solution
description: Découvrez les mises à jour de plateforme pour Azure VMware Solution.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045210"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Mises à jour de plateforme pour Azure VMware Solution

Azure VMware Solution appliquera les mises à jour importantes à partir de mars 2021. Vous recevrez une notification via Azure Service Health qui indiquera le calendrier de maintenance. Pour plus d’informations, consultez [Mises à jour et mises à niveau des clouds privés Azure VMware Solution](concepts-upgrades.md).

## <a name="march-24-2021"></a>24 mars 2021
Tous les nouveaux clouds privés Azure VMware Solution sont déployés avec VMware vCenter version 6.7 U3l et NSX-T version 3.1.1. Tous les clouds privés existants seront mis à jour et mis à niveau **jusqu’en juin 2021** vers les versions mentionnées ci-dessus.

Vous recevrez un e-mail contenant la date et l’heure prévues de la maintenance. Vous pouvez replanifier une mise à niveau. L’e-mail fournit également des détails sur le composant mis à niveau, son effet sur les charges de travail, l’accès au cloud privé et d’autres services Azure.  Une heure avant la mise à niveau, vous recevrez une notification, puis une autre à la fin de l’opération.

## <a name="march-15-2021"></a>15 mars 2021 

- Le service Azure VMware Solution effectuera du travail de maintenance **jusqu’au 19 mars 2021** pour mettre à jour le serveur vCenter dans votre cloud privé vers vCenter Server 6.7 Mise à jour version 3l.

- vCenter VMware ne sera pas disponible pendant cette période.  Vous ne serez donc pas en mesure de gérer vos machines virtuelles (arrêter, démarrer, créer, supprimer) ou la mise à l’échelle du cloud privé (en ajoutant/supprimant des serveurs et des clusters). Toutefois, la haute disponibilité VMware (HA) continuera à fonctionner pour protéger les machines virtuelles existantes. 
 
Pour plus d’informations sur cette version de vCenter, consultez [Notes de mise à jour de VMware vCenter Server 6.7 Update 3l](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 mars 2021

- Azure VMware Solution appliquera [VMware ESXi 6.7, version de correctif ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) aux privés existants **jusqu’au 15 mars 2021**.

- Les solutions de contournement documentées pour la pile vSphere, comme pour [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), seront également appliquées **jusqu’au 15 mars 2021**.

>[!NOTE]
>Cette solution n’est pas disruptive et ne doit pas impacter les services ou charges de travail Azure VMware. Pendant la maintenance, plusieurs alertes VMware, comme _Lost network connectivity on DVPorts_ (Perte de la connectivité réseau sur les DVPorts) et _Lost uplink redundancy on DVPorts_ (Perte de la redondance de la liaison montante sur les DVPorts), s’affichent dans vCenter et disparaissent toutes seules à mesure que la maintenance progresse.

## <a name="post-update"></a>Après la mise à jour
Une fois la mise à jour terminée, vous devez voir les nouvelles versions des composants VMware s’afficher. Si vous rencontrez des problèmes ou si vous avez des questions, contactez notre équipe de support technique en ouvrant un ticket de support.