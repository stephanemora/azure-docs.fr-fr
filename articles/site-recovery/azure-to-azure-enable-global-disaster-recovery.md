---
title: Activer la récupération d’urgence sur l’ensemble des régions Azure dans le monde entier
description: Cet article décrit la fonctionnalité de récupération d’urgence globale d’Azure Site Recovery.
author: JYOTHIRMAISURI
manager: evansma
ms.service: site-recovery
ms.topic: article
ms.date: 08/09/2021
ms.author: v-jysur
ms.openlocfilehash: 1179558e01ff23c66db652395e10c7d6259af09b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563005"
---
# <a name="enable-global-disaster-recovery-using-azure-site-recovery"></a>Activer la récupération d’urgence globale avec Azure Site Recovery

Cet article explique comment répliquer, basculer et restaurer automatiquement des machines virtuelles Azure d’une zone de disponibilité à une autre au sein de la même région Azure ou d’une région Azure différente.

>[!NOTE]
> Azure Site Recovery ne déplace pas et ne stocke pas de données hors de la région source et de la région cible que vous avez choisies. Si vous le souhaitez, vous pouvez sélectionner un coffre Recovery Services dans une autre région (tierce). Le coffre Recovery Services contient des métadonnées, mais pas de données client réelles.

Le service Azure Site Recovery contribue à votre stratégie de continuité d’activité et reprise d’activité en veillant à ce que vos applications métier restent opérationnelles lors d’interruptions planifiées ou non. Il s’agit de l’option de récupération d’urgence recommandée pour maintenir vos applications opérationnelles en cas de pannes régionales.

## <a name="disaster-recovery-for-global-azure-regions"></a>Récupération d’urgence pour les régions Azure globales

Azure a été le premier grand fournisseur de cloud public à lancer une solution de récupération d’urgence native cloud de premier ordre avec Reprise d’activité depuis Azure vers Azure. Cette offre vous permettait de répliquer et de basculer vos applications d’une région Azure vers une autre, sur le même continent. prend également en charge la récupération d’urgence de zone à zone.   

Azure Site Recovery prend désormais en charge la récupération d’urgence globale. Vous pouvez désormais répliquer et faire basculer vos applications à partir de n’importe quelle région Azure, sur plusieurs continents.

Un grand nombre d’entre vous dirigent des entreprises d’envergure mondiale et souhaitent héberger et répliquer leurs applications dans le monde entier en tirant parti de la présence mondiale d’Azure. C’est là que vous pouvez tirer parti de la récupération d’urgence mondiale via Azure Site Recovery. Cette nouvelle offre supprime les limites continentales de la récupération d’urgence d’Azure à Azure. Grâce à notre principale infrastructure de mise en réseau mondiale, vous pouvez répliquer et faire basculer vos applications vers n’importe quelle région Azure de votre choix, dans le monde entier.  

Cette offre vient compléter le portefeuille de services de récupération d’urgence dans le cloud public natif de la récupération d’urgence de zone à zone, de la récupération d’urgence à l’échelle du continent et de la récupération d’urgence globale.

>[!NOTE]
>Le choix d’une région de récupération d’urgence sur un continent distant n’a pas d’impact mineur sur l’objectif de point de récupération (RPO), mais n’est pas suffisamment important pour avoir un impact sur les contrats SLA Azure Site Recovery.

Cette offre vient compléter le portefeuille de services de récupération d’urgence dans le cloud public natif de la récupération d’urgence de zone à zone, de la récupération d’urgence à l’échelle du continent et de la récupération d’urgence globale.  

## <a name="before-you-begin"></a>Avant de commencer
Cet article suppose que vous vous êtes préparé pour le déploiement d’Azure Site Recovery, tel que décrit dans le [didacticiel pour la récupération d’urgence d’Azure vers Azure](azure-to-azure-tutorial-enable-replication.md).

Vérifiez que les conditions préalables sont remplies et que vous avez créé un coffre Recovery Services.

>[!NOTE]
> Consultez [matrice de prise en charge](azure-to-azure-support-matrix.md) avant d’activer la réplication de vos machines virtuelles.

## <a name="supported-platform-features"></a>Fonctionnalités de plateforme prises en charge

| **Fonctionnalité** | **Déclaration de support** |
| --- | --- |
| les machines virtuelles Classic, | No |
| Machines virtuelles ARM |  Yes |
| Azure Disk Encryption v1 (deux passes, avec Azure Active Directory [Azure AD]) |  Yes |
| Azure Disk Encryption v2 (passage unique, sans Azure AD) |  Oui |
|     |  Non |
| Disques managés |  Oui |
| Clés gérées par le client |   Yes |
| Groupes de placements de proximité |  Yes |
| Interopérabilité de la sauvegarde | La sauvegarde et la restauration au niveau fichier sont prises en charge. La sauvegarde et la restauration au niveau du disque et de la machine virtuelle ne sont pas prises en charge. |
| Ajout/suppression à chaud | Il est possible d’ajouter des disques après activation de la réplication de zone à zone. La suppression de disques après l’activation de la réplication de zone à zone n’est pas prise en charge. |

## <a name="enable-replication"></a>Activer la réplication

Pour répliquer rapidement vos appliances, procédez comme suit :

1. Sur le portail, accédez à la machine virtuelle.

2. Sur la gauche, sous **Opérations**, sélectionnez **Récupération d’urgence**

3. Pour **De base**, sélectionnez la **Région cible**.

   Toutes les régions Azure disponibles dans le monde entier sont affichées dans le menu déroulant. Choisissez la région cible en fonction de vos préférences.  

   ![Paramètres pour la récupération d’urgence globale](./media/azure-to-azure-enable-global-disaster-recovery/enable-global-disaster-recovery.png)

   Vous pouvez apporter d’autres modifications à la configuration au besoin.

4. Cliquez sur **Paramètres avancés** pour d’autres paramètres, tels qu’**Abonnement cible**, **Réseau virtuel cible**, **Disponibilité cible**, **Groupe de placement de proximité cible**.

5. Cliquez sur **Réviser + lancer la réplication**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [récupération d’urgence de zone à zone](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).
- En savoir plus sur l’[architecture pour la reprise d’activité d’Azure vers Azure](azure-to-azure-architecture.md)
