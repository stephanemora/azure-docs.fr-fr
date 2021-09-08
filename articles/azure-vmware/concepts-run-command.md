---
title: Concepts - Commandes Exécuter dans Azure VMware Solution
description: En savoir plus sur l’utilisation des commandes Exécuter dans Azure VMware Solution.
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 28cbf76dd035ce9b04909a61e3001063aa151162
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310678"
---
# <a name="run-commands-in-azure-vmware-solution"></a>Commandes Exécuter dans Azure VMware Solution

Dans Azure VMware Solution, vous obtiendrez un accès à vCenter avec le rôle CloudAdmin. Vous pouvez [afficher les privilèges accordés](concepts-identity.md#view-the-vcenter-privileges) au rôle CloudAdmin d’Azure VMware Solution sur votre instance vCenter de cloud privé Azure VMware Solution. Les commandes Exécuter sont une collection de cmdlets PowerShell pour laquelle vous effectuez certaines opérations sur vCenter, ce qui nécessite des privilèges élevés. 

Azure VMware Solution prend en charge les opérations suivantes :

- [Installer et désinstaller la solution de récupération d’urgence JetStream](deploy-disaster-recovery-using-jetstream.md)

- [Configurer une source d’identité externe](configure-identity-source-vcenter.md)

- [Afficher et modifier la stratégie de stockage](configure-storage-policy.md) 


>[!NOTE]
>Les commandes Exécuter sont exécutées successivement dans l’ordre de leur envoi.

## <a name="view-the-status-of-an-execution"></a>Afficher l’état d'une exécution

Vous pouvez afficher l’état des commandes Exécuter exécutées, notamment la sortie, les erreurs, les avertissements et les informations.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Commande Exécuter** > **État de l’exécution de Exécuter**.

   Vous pouvez trier sur le nom de l’exécution, le nom du package, la version du package, le nom de la commande, l’heure de début, l’heure de fin et l’état.  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="Capture d’écran montrant l’onglet État de l’exécution de Exécuter." lightbox="media/run-command/run-execution-status.png":::

1. Sélectionnez l’exécution que vous souhaitez afficher.

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="Capture d’écran montrant un exemple d’exécution de Exécuter.":::

   Vous pouvez afficher plus de détails sur l’exécution, notamment la sortie, les erreurs, les avertissements et les informations.

   - **Détails** : résumé des détails de l’exécution, tels que le nom, l’état, le package et le nom de la commande exécutée. 

   - **Sortie** : message à la fin de l’exécution réussie d’une cmdlet. Toutes les cmdlets n’ont pas de sortie.

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="Capture d’écran montrant la sortie d’une exécution Exécuter.":::

   - **Erreur** : exception de fin d’exécution qui a arrêté l’exécution d’une cmdlet.    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="Capture d’écran montrant les erreurs détectées lors de l’exécution d’une exécution.":::

   - **Avertissement** : une exception sans fin d’exécution s’est produite lors de l’exécution d’une cmdlet. 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="Capture d’écran montrant les avertissements détectés lors de l’exécution d’une exécution.":::

   - **Informations** : message de progression pendant l’exécution d’une cmdlet. 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="Capture d’écran montrant la progression en temps réel globale de la cmdlet lors de son exécution.":::



## <a name="cancel-or-delete-a-job"></a>Annuler ou supprimer un travail



### <a name="method-1"></a>Méthode 1

>[!NOTE]
>La méthode 1 est irréversible.

1. Sélectionnez **Commande Exécuter** > **État de l’exécution de Exécuter** puis sélectionnez le travail que vous souhaitez annuler.

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-1.png" alt-text="Capture d’écran montrant comment annuler et supprimer une commande Exécuter.":::

2. Sélectionnez **Oui** pour annuler et supprimer le travail pour tous les utilisateurs.



### <a name="method-2"></a>Méthode 2

1. Sélectionnez **Commande Exécuter** > **Packages** > **État de l’exécution de Exécuter**.

2. Sélectionnez **Plus** (...) pour le travail que vous souhaitez annuler et supprimer.

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-2.png" alt-text="Capture d’écran montrant comment annuler et supprimer une commande Exécuter à l’aide de l’ellipse.":::

3. Sélectionnez **Oui** pour annuler et supprimer le travail pour tous les utilisateurs.



## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert les concepts de la commande Exécuter, vous pouvez utiliser la fonctionnalité de commande Exécuter pour :

- [Configurer une stratégie de stockage](configure-storage-policy.md) : au moins une stratégie de stockage de machine virtuelle est associée à chaque machine virtuelle déployée sur un magasin de données vSAN. Vous pouvez attribuer une stratégie de stockage de machine virtuelle dans un déploiement initial de machine virtuelle ou lorsque vous effectuez d’autres opérations de machine virtuelle, telles que le clonage ou la migration.

- [Configurer une source d’identité externe pour vCenter](configure-identity-source-vcenter.md) : vCenter a un utilisateur local intégré appelé cloudadmin et affecté au rôle CloudAdmin. L’utilisateur local cloudadmin est utilisé pour configurer des utilisateurs dans Active Directory (AD). La fonctionnalité de commande Exécuter vous permet de configurer Active Directory via LDAP ou LDAP sécurisé pour vCenter en tant que source d’identité externe.

- [Déployer la récupération d’urgence à l’aide de JetStream](deploy-disaster-recovery-using-jetstream.md) : stockez les données directement sur un cluster de récupération dans vSAN. Les données sont capturées via des filtres d’E/S qui s’exécutent dans vSphere. Le magasin de données sous-jacent peut être VMFS, VSAN, vVol ou n’importe quelle plateforme HCI. 
