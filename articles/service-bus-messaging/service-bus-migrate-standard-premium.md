---
title: Migrer les espaces de noms standards existants Azure Service Bus vers le niveau premium | Microsoft Docs
description: Guide pour permettre la migration d’Azure Service Bus standard espaces de noms existants vers premium
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: darosa
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2019
ms.author: aschhab
ms.openlocfilehash: 65c207b4d03e7d156c8c871a3642601fd0489ead
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991415"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrer les espaces de noms standards existants Azure Service Bus vers le niveau premium
Précédemment, Azure Service Bus proposé des espaces de noms uniquement sur le niveau standard. Espaces de noms sont des configurations d’architecture mutualisées qui sont optimisées pour les environnements de développement et de faible débit. Le niveau premium offre des ressources dédiées par espace de noms pour la latence prévisible et un débit plus élevé à prix fixe. Le niveau premium est optimisé pour un débit élevé et les environnements de production qui nécessitent les fonctionnalités d’entreprise supplémentaires.

Cet article décrit comment migrer les espaces de noms de niveau standard existants vers le niveau premium.  

>[!WARNING]
> Migration est destinée aux espaces de noms standard Service Bus être mis à niveau vers le niveau premium. L’outil de migration ne prend pas en charge la rétrogradation.

Certains des points à noter : 
- Cette migration est censée se produire en place, ce qui signifie que déjà applications expéditeur et du récepteur **ne nécessitent aucune modification au code ou de configuration**. La chaîne de connexion existante pointent automatiquement vers le nouvel espace de noms premium.
- Le **premium** espace de noms doit avoir **aucune entité** qu’il contient pour la migration réussisse. 
- Tous les **entités** dans l’espace de noms standard sont **copié** à l’espace de noms premium pendant le processus de migration. 
- Prend en charge de la migration **1 000 entités par unité de messagerie** sur le niveau premium. Pour identifier le nombre d’unités de messagerie vous avez besoin, commencer par le nombre d’entités que vous disposez sur votre espace de noms standard actuel. 
- Vous ne pouvez pas migrer directement à partir de **niveau de base** à **premier niveau**, mais vous pouvez le faire indirectement en effectuant une migration de base d’abord standard, puis de la norme vers premium à l’étape suivante.

## <a name="migration-steps"></a>Étapes de la migration
Certaines conditions sont associées au processus de migration. Familiarisez-vous avec les étapes suivantes pour réduire le risque d’erreurs. Ces étapes décrivent le processus de migration, et les informations détaillées sont répertoriées dans les sections qui suivent.

1. Créer un nouvel espace de noms premium.
1. Associer les espaces de noms standard et premium à l’autre.
1. Entités de la synchronisation (copie pointage) à partir de la norme à l’espace de noms premium.
1. Valider la migration.
1. Vider les entités dans l’espace de noms standard en utilisant le nom après la migration de l’espace de noms.
1. Supprimer l’espace de noms standard.

>[!IMPORTANT]
> Une fois la migration a été validée, accédez à l’ancien espace de noms standard et vider les files d’attente et les abonnements. Une fois que les messages ont été purgées, ils peuvent être envoyés vers le nouvel espace de noms premium pour être traités par les applications de destinataire. Une fois que les files d’attente et les abonnements ont été purgées, nous vous recommandons de supprimer l’ancien espace de noms standard.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrer à l’aide de l’interface CLI ou PowerShell

Pour migrer votre espace de noms Service Bus standard vers premium à l’aide de l’interface CLI ou PowerShell, procédez comme suit.

1. Créer un nouvel espace de noms premium Service Bus. Vous pouvez référencer le [modèles Azure Resource Manager](service-bus-resource-manager-namespace.md) ou [utiliser le portail Azure](service-bus-create-namespace-portal.md). Veillez à sélectionner **premium** pour le **serviceBusSku** paramètre.

1. Définissez les variables d’environnement suivantes pour simplifier les commandes de migration.
   ```azurecli
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Après la migration/nom d’alias (post_migration_dns_name) sera utilisé pour accéder à l’espace de noms standard ancien après la migration. Utilisez-le pour vider les files d’attente et les abonnements et supprimez l’espace de noms.

1. Associer les espaces de noms standard et premium et démarrez la synchronisation à l’aide de la commande suivante :

    ```azurecli
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


1. Vérifiez l’état de la migration à l’aide de la commande suivante :
    ```azurecli
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    La migration est considérée comme terminée lorsque vous voyez les valeurs suivantes :
    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = « Réussi »

    Cette commande affiche également la configuration de la migration. Vérifiez que les valeurs sont définies correctement. Vérifiez également l’espace de noms premium dans le portail pour vous assurer toutes les files d’attente et rubriques ont été créés et qu’ils correspondent aux valeurs existantes dans l’espace de noms standard.

1. Validez la migration en exécutant la commande suivante :
   ```azurecli
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrer à l’aide du portail Azure

Migration à l’aide du portail Azure a le même flux logique en tant que la migration en utilisant les commandes. Suivez ces étapes pour migrer à l’aide du portail Azure.

1. Sur le **Navigation** menu dans le volet gauche, sélectionnez **migrer vers premium**. Cliquez sur le **prise en main** pour continuer à la page suivante.
    ![Page d’accueil de migration][]

1. Complète **le programme d’installation**.
   ![Espace de noms le programme d’installation][]
   1. Créez et affectez l’espace de noms premium pour migrer l’espace de noms standard existant vers.
        ![Configurer l’espace de noms : créer l’espace de noms premium][]
   1. Choisissez un **nom de la Migration de Post**. Vous utiliserez ce nom d’accès à l’espace de noms standard après que la migration est terminée.
        ![Configurer l’espace de noms : choisir le nom de la migration post][]
   1. Sélectionnez **'Next'** pour continuer.
1. Entités de synchronisation entre les espaces de noms standard et premium.
    ![Le programme d’installation de l’espace de noms - synchronisation entités - début][]

   1. Sélectionnez **démarrer la synchronisation** pour commencer la synchronisation d’entités.
   1. Sélectionnez **Oui** dans la boîte de dialogue pour confirmer et démarrez la synchronisation.
   1. Attendez que la synchronisation se termine. L’état est disponible sur la barre d’état.
        ![Espace de noms - synchronisation entités - progression de l’installation][]
        >[!IMPORTANT]
        > Si vous avez besoin annuler la migration pour une raison quelconque, veuillez consulter le flux d’abandon dans la section Forum aux questions de ce document.
   1. Une fois la synchronisation terminée, sélectionnez **suivant** en bas de la page.

1. Passez en revue les modifications sur la page de résumé. Sélectionnez **terminer la Migration** pour passer des espaces de noms et pour terminer la migration.
    ![Basculer l’espace de noms - commutateur menu][] la page de confirmation s’affiche lorsque la migration est terminée.
    ![Espace de noms de commutateur - réussite][]

## <a name="faqs"></a>FAQ

### <a name="what-happens-when-the-migration-is-committed"></a>Que se passe-t-il lorsque la migration est validée ?

Une fois la migration est validée, la chaîne de connexion qui pointe vers l’espace de noms standard pointera vers l’espace de noms premium.

Les applications de l’expéditeur et le destinataire seront Déconnectez le Namespace standard et se reconnecter automatiquement à l’espace de noms premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Que faire faire une fois terminée la norme pour la migration premium ?

La norme pour la migration premium permet de s’assurer que les métadonnées d’entité tels que des rubriques, abonnements et les filtres sont copiés à partir de l’espace de noms standard dans l’espace de noms premium. Les données du message qui a été validées dans l’espace de noms standard n’est pas copiées à partir de l’espace de noms standard pour l’espace de noms premium.

L’espace de noms standard peut avoir des messages qui ont été envoyés et validées lors de la migration en cours d’exécution. Purger manuellement ces messages à partir de la Namespace standard et les envoyer manuellement à la prime Namespace. Pour purger manuellement les messages, utilisez une application console ou un script qui draine les entités de l’espace de noms standard en utilisant le nom DNS de Migration de billet que vous avez spécifié dans les commandes de migration. Envoyer ces messages à l’espace de noms premium afin qu’ils peuvent être traités par les destinataires.

Une fois que les messages ont été purgées, supprimez l’espace de noms standard.

>[!IMPORTANT]
> Une fois que les messages à partir de l’espace de noms standard ont été purgées, supprimez l’espace de noms standard. Ceci est important, car la chaîne de connexion initialement appelée à l’espace de noms standard fait référence à l’espace de noms premium. Vous n’avez plus besoin du Namespace standard. Suppression de l’espace de noms standard que vous avez migrés permet de réduire la confusion ultérieure.

### <a name="how-much-downtime-do-i-expect"></a>Le temps d’inactivité attendre ?
Le processus de migration est destiné à réduire le temps d’arrêt attendus pour les applications. Temps d’arrêt est réduite à l’aide de la chaîne de connexion que les applications de l’expéditeur et récepteur utilisent pour pointer vers le nouvel espace de noms premium.

Le temps d’arrêt est rencontré par l’application est limitée au temps que nécessaire pour mettre à jour l’entrée DNS pour pointer vers l’espace de noms premium. Temps d’arrêt est environ 5 minutes.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Je dois apporter des modifications de configuration lors de la migration ?
Non, il n’existe aucune modification de code ou de configuration nécessaires pour effectuer la migration. La chaîne de connexion que les applications de l’expéditeur et récepteur utilisent pour accéder le Namespace standard est automatiquement mappée pour agir en tant qu’alias pour l’espace de noms premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Que se passe-t-il lorsque j’ai abandonner la migration ?
La migration peut être annulée à l’aide de la `Abort` commande ou en utilisant le portail Azure. 

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Portail Azure

![Abandonner le flux - abandonner la synchronisation][]
![abandonner flux - abandonner terminée][]

Lorsque le processus de migration est abandonné, elle interrompt le processus de copie les entités (rubriques, abonnements et les filtres) à partir de la norme à l’espace de noms premium et rompt l’association.

La chaîne de connexion n’est pas mis à jour pour pointer vers l’espace de noms premium. Vos applications existantes continuent de fonctionner comme avant de commencer la migration.

Toutefois, il ne supprimer les entités dans l’espace de noms premium ou l’espace de noms premium. Supprimez manuellement les entités si vous avez décidé de ne pas à poursuivre la migration.

>[!IMPORTANT]
> Si vous décidez d’abandonner la migration, supprimez la prime Namespace vous aviez configuré pour la migration afin que vous n’êtes pas facturé pour les ressources.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Je ne veux pas obligé de vider les messages. Que faire ?

Des messages peuvent être envoyés par les applications de l’expéditeur et validées vers le stockage sur le Namespace standard, tandis que la migration se déroule et juste avant la migration est validée.

Pendant la migration, la message réel/charge utile de données ne seront pas copiée à partir de la norme à l’espace de noms premium. Les messages doivent être purgées manuellement et ensuite envoyées à l’espace de noms premium.

Toutefois, si vous pouvez migrer pendant une fenêtre de maintenance planifiée/tâches de nettoyage, et vous ne souhaitez pas vider manuellement et d’envoyer les messages, procédez comme suit :

1. Arrêtez les applications de l’expéditeur. Les applications de destinataire traite les messages qui sont actuellement dans l’espace de noms standard et permet de vider la file d’attente.
1. Une fois que les files d’attente et les abonnements dans le Namespace standard sont vides, suivez la procédure décrite précédemment pour exécuter la migration à partir de la norme à l’espace de noms premium.
1. Une fois la migration terminée, vous pouvez redémarrer les applications de l’expéditeur.
1. Les expéditeurs et les destinataires seront désormais se connecter automatiquement avec l’espace de noms premium.

    >[!NOTE]
    > Il est inutile d’arrêter les applications de destinataire pour la migration.
    >
    > Une fois la migration terminée, les applications de récepteur vous déconnecter de l’espace de noms standard et se connecter automatiquement à l’espace de noms premium.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [différences entre standard et premium Messaging](./service-bus-premium-messaging.md).
* En savoir plus sur la [aspects de la récupération d’urgence et de haute disponibilité pour le Bus de Service premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Page d’accueil de migration]: ./media/service-bus-standard-premium-migration/1.png
[Espace de noms le programme d’installation]: ./media/service-bus-standard-premium-migration/2.png
[Configurer l’espace de noms : créer l’espace de noms premium]: ./media/service-bus-standard-premium-migration/3.png
[Configurer l’espace de noms : choisir le nom de la migration post]: ./media/service-bus-standard-premium-migration/4.png
[Le programme d’installation de l’espace de noms - synchronisation entités - début]: ./media/service-bus-standard-premium-migration/5.png
[Espace de noms - synchronisation entités - progression de l’installation]: ./media/service-bus-standard-premium-migration/8.png
[Espace de noms de commutateur - menu du commutateur]: ./media/service-bus-standard-premium-migration/9.png
[Espace de noms de commutateur - réussite]: ./media/service-bus-standard-premium-migration/12.png

[Abandonner le flux - abandonner la synchronisation]: ./media/service-bus-standard-premium-migration/abort1.png
[Abandonner le flux - abandonner terminée]: ./media/service-bus-standard-premium-migration/abort3.png
