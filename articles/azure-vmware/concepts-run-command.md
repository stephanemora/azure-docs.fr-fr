---
title: Concepts - Commande Exécuter dans Azure VMware Solution (préversion)
description: En savoir plus sur l’utilisation des commandes Exécuter dans Azure VMware Solution.
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: a4121f8479b22eb1c0666a1e7d7a23ece4fb3d20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668846"
---
# <a name="run-command-in-azure-vmware-solution-preview"></a>Commande Exécuter dans Azure VMware Solution (préversion)

Dans Azure VMware Solution, vCenter a un utilisateur local intégré nommé *cloudadmin* titulaire du rôle CloudAdmin. Le rôle CloudAdmin dispose de [privilèges](concepts-identity.md#view-the-vcenter-privileges) vCenter qui diffèrent de ceux d’autres solutions cloud et déploiements locaux VMware. La fonctionnalité Commande Exécuter (préversion) vous permet d’effectuer des opérations qui nécessiteraient normalement des privilèges élevés à l’aide d’une collection d’applets de commande PowerShell. 

Azure VMware Solution prend en charge les opérations suivantes :

- [Configurer une source d’identité externe](configure-identity-source-vcenter.md)

- [Afficher et définir des stratégies de stockage](configure-storage-policy.md) 

- [Déployer la reprise d’activité avec JetStream](deploy-disaster-recovery-using-jetstream.md)


>[!NOTE]
>Les commandes Exécuter sont exécutées successivement dans l’ordre de leur envoi.

## <a name="view-the-status-of-an-execution"></a>Afficher l’état d'une exécution

Vous pouvez afficher l’état de toute commande Exécuter exécutée, notamment la sortie, les erreurs, les avertissements et les journaux d’informations des applets de commande.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Commande Exécuter** > **État de l’exécution de Exécuter**.

   Vous pouvez trier les différentes colonnes en sélectionnant la colonne.  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="Capture d’écran montrant l’onglet État de l’exécution de Exécuter." lightbox="media/run-command/run-execution-status.png":::

1. Sélectionnez l’exécution que vous souhaitez afficher. Un volet s’ouvre avec des détails sur l’exécution et d’autres onglets pour les différents types de sortie générés par l’applet de commande.

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="Capture d’écran montrant un exemple d’exécution de Exécuter.":::

   Vous pouvez afficher plus de détails sur l’exécution, notamment la sortie, les erreurs, les avertissements et les informations.

   - **Détails** : synthèse des détails de l’exécution, tels que le nom, l’état, le package, le nom de l’applet de commande et l’erreur (en cas d’échec de la commande). 

   - **Sortie** : messages générés par l’applet de commande. Peut inclure la progression ou le résultat de l’opération. Toutes les cmdlets n’ont pas de sortie.

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="Capture d’écran montrant la sortie d’une exécution Exécuter.":::

   - **Erreur** : messages d’erreur générés lors de l’exécution de l’applet de commande. Cela s’ajoute au message d’erreur de fin dans le volet d’informations.    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="Capture d’écran montrant les erreurs détectées lors de l’exécution d’une exécution.":::

   - **Avertissement** : messages d’avertissement générés lors de l’exécution. 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="Capture d’écran montrant les avertissements détectés lors de l’exécution d’une exécution.":::

   - **Informations** : messages de progression et de diagnostic générés lors de l’exécution d’une applet de commande. 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="Capture d’écran montrant la progression en temps réel globale de la cmdlet lors de son exécution.":::



## <a name="cancel-or-delete-a-job"></a>Annuler ou supprimer un travail



### <a name="method-1"></a>Méthode 1

Cette méthode tente d’annuler l’exécution, puis la supprime lors de l’achèvement.

>[!IMPORTANT]
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

- [Configurer une stratégie de stockage](configure-storage-policy.md) : une stratégie de stockage de vSAN est associée à chaque machine virtuelle déployée sur un magasin de données vSAN. Vous pouvez attribuer une stratégie de stockage vSAN lors d’un déploiement initial de machine virtuelle ou lorsque vous effectuez d’autres opérations de machine virtuelle, telles que le clonage ou la migration.

- [Configurer une source d’identité externe pour vCenter (commande Exécuter)](configure-identity-source-vcenter.md) : configurez Active Directory sur LDAP ou LDAPS pour vCenter, ce qui permet l’utilisation d’une source d’identité externe comme Active Directory. Vous pouvez ensuite ajouter des groupes à partir de la source d’identité externe au rôle CloudAdmin.

- [Déployer la récupération d’urgence à l’aide de JetStream](deploy-disaster-recovery-using-jetstream.md) : stockez les données directement sur un cluster de récupération dans vSAN. Les données sont capturées via des filtres d’E/S qui s’exécutent dans vSphere. Le magasin de données sous-jacent peut être VMFS, VSAN, vVol ou n’importe quelle plateforme HCI. 