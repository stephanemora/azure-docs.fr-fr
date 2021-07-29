---
title: Mises à jour de plateforme pour Azure VMware Solution
description: Découvrez les mises à jour de plateforme pour Azure VMware Solution.
ms.topic: reference
ms.date: 05/26/2021
ms.openlocfilehash: 38cd183b783989f6a682b68e92e490354b032750
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110538061"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Mises à jour de plateforme pour Azure VMware Solution

Azure VMware Solution appliquera les mises à jour importantes à partir de mars 2021. Vous recevrez une notification via Azure Service Health qui indiquera le calendrier de maintenance. Pour plus d’informations, consultez [Maintenance de l’hôte et gestion du cycle de vie](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management).

## <a name="may-25-2021"></a>25 mai 2021
Selon le bulletin de sécurité VMware [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html), de multiples vulnérabilités ont été signalées dans VMware ESXi et vSphere Client (HTML5). 

Afin de corriger les vulnérabilités ([CVE-2021-21985](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21985) et [CVE-2021-21986](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21986)) signalées dans le bulletin de sécurité VMware [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html), vCenter Server a été mis à jour dans tous les clouds privés Azure VMware Solution.

Aucune action supplémentaire n’est requise.

## <a name="may-21-2021"></a>21 mai 2021
 
Le service Azure VMware Solution effectuera des travaux de maintenance jusqu'au 23 mai 2021 pour appliquer des mises à jour importantes au serveur vCenter dans votre cloud privé.  Vous recevrez une notification via Azure Service Health qui indiquera le calendrier de maintenance de votre cloud privé.
 
Pendant ce temps, VMware vCenter sera indisponible et vous ne pourrez pas gérer les machines virtuelles (arrêter, démarrer, créer ou supprimer). Pendant cette période, il est recommandé de ne pas planifier d'autres activités telles que la mise à l'échelle du cloud privé ou la création de nouveaux réseaux dans votre cloud privé.
 
Il n'y aura aucun impact sur les charges de travail exécutées dans votre cloud privé.


## <a name="april-26-2021"></a>26 avril 2021
Tous les nouveaux clouds privés Azure VMware Solution sont désormais déployés avec VMware vCenter version 6.7 U3l et NSX-T version 2.5.2. Nous n’utilisons pas NSX-T 3.1.1 pour les nouveaux clouds privés en raison d’un problème identifié dans NSX-T 3.1.1 qui a un impact sur la connectivité des machines virtuelles clientes. 

L’atténuation recommandée par VMware a été appliquée à tous les clouds privés existants qui exécutent actuellement NSX-T 3.1.1 sur Azure VMware Solution. Il a été confirmé que la solution de contournement n’a aucun impact sur la connectivité de machines virtuelles clientes.

## <a name="march-24-2021"></a>24 mars 2021
Tous les nouveaux clouds privés Azure VMware Solution sont déployés avec VMware vCenter version 6.7 U3l et NSX-T version 3.1.1. Tous les clouds privés existants seront mis à jour et mis à niveau **jusqu’en juin 2021** vers les versions mentionnées ci-dessus.

Vous recevrez un e-mail contenant la date et l’heure prévues de la maintenance. Vous pouvez replanifier une mise à niveau. L’e-mail fournit également des détails sur le composant mis à niveau, son effet sur les charges de travail, l’accès au cloud privé et d’autres services Azure.  Une heure avant la mise à niveau, vous recevrez une notification, puis une autre à la fin de l’opération.

## <a name="march-15-2021"></a>15 mars 2021 

- Le service Azure VMware Solution effectuera du travail de maintenance **jusqu’au 19 mars 2021** pour mettre à jour le serveur vCenter dans votre cloud privé vers vCenter Server 6.7 Mise à jour version 3l.

- VMware vCenter sera indisponible pendant cette période. Vous ne pourrez donc pas gérer vos machines virtuelles (arrêter, démarrer, créer, supprimer) ou la mise à l’échelle du cloud privé (ajout/suppression de serveurs et de clusters). Toutefois, la haute disponibilité VMware (HA) continuera à fonctionner pour protéger les machines virtuelles existantes. 
 
Pour plus d’informations sur cette version de vCenter, consultez [Notes de mise à jour de VMware vCenter Server 6.7 Update 3l](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html).

## <a name="march-4-2021"></a>4 mars 2021

- Azure VMware Solution appliquera [VMware ESXi 6.7, version de correctif ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) aux privés existants **jusqu’au 15 mars 2021**.

- Les solutions de contournement documentées pour la pile vSphere, comme pour [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), seront également appliquées **jusqu’au 15 mars 2021**.

>[!NOTE]
>Cette solution n’est pas disruptive et ne doit pas impacter les services ou charges de travail Azure VMware. Pendant la maintenance, plusieurs alertes VMware, comme _Lost network connectivity on DVPorts_ (Perte de la connectivité réseau sur les DVPorts) et _Lost uplink redundancy on DVPorts_ (Perte de la redondance de la liaison montante sur les DVPorts), s’affichent dans vCenter et disparaissent toutes seules à mesure que la maintenance progresse.

## <a name="post-update"></a>Après la mise à jour
Une fois la mise à jour terminée, vous devez voir les nouvelles versions des composants VMware s’afficher. Si vous rencontrez des problèmes ou si vous avez des questions, contactez notre équipe de support technique en ouvrant un ticket de support.
