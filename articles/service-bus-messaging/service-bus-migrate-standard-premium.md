---
title: Migrer d’Azure Service Bus Standard espaces de noms existants au niveau Premium | Microsoft Docs
description: Guide pour permettre la migration d’Azure Service Bus Standard espaces de noms existants vers Premium
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
ms.date: 02/18/2019
ms.author: aschhab
ms.openlocfilehash: 7b153c36e10f1d4e2be2a0cf42f998c31cb6473a
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896423"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-premium-tier"></a>Migrer d’Azure Service Bus Standard espaces de noms existants au niveau Premium

Précédemment, Azure Service Bus proposé des espaces de noms uniquement sur le niveau Standard. Il s’agissait de configurations d’architecture mutualisées qui ont été optimisées pour les environnements de développement et de faible débit.

Récemment, Azure Service Bus a développé pour offrir le niveau Premium qui offre des ressources dédiées par espace de noms pour la latence prévisible et un débit plus élevé à un prix fixe qui est optimisé pour un débit élevé et les environnements de production nécessitant des fonctionnalités d’entreprise supplémentaires.

Le ci-dessous outils permet existantes des espaces de noms de niveau Standard pour être migrés vers le niveau Premium.

>[!WARNING]
> Migration est destinée à l’espace de noms Service Bus Standard pour être ***mis à niveau*** vers le niveau Premium.
>
> Les outils de migration ***pas*** vers une version antérieure.
>[!NOTE]
> Cette migration est censée se produire ***en place***.
>
> Cela implique que l’expéditeur existant et les applications de destinataire ne nécessitent pas toute modification de code ou la configuration.
>
> La chaîne de connexion existante pointent automatiquement vers le nouvel espace de noms premium.
>
> En outre, toutes les entités dans l’espace de noms Standard sont **copiées** dans l’espace de noms Premium pendant le processus de migration.
>
>
> Nous prenons en charge ***1 000 entités par unité de messagerie*** sur Premium, vous devez effectuer les opérations suivantes pour identifier le nombre d’unités de messagerie, démarrez avec le nombre d’entités que vous disposez sur votre espace de noms Standard actuel.

## <a name="migration-steps"></a>Étapes de la migration

>[!IMPORTANT]
> Il existe certains inconvénients associés au processus de migration. Nous vous demandons d’entièrement vous familiariser avec les étapes réduire les possibilités d’erreurs.

Le processus de migration d’étape par étape concret est détaillé dans les guides ci-dessous.

Les étapes logiques sont-

1. Créer un nouvel espace de noms Premium.
2. Associer l’espace de noms Standard et Premium à l’autre.
3. Entités de synchronisation (copie pointage) à partir de Standard à l’espace de noms Premium
4. Validez la migration
5. Vider les entités dans l’espace de noms Standard en utilisant le nom après la migration de l’espace de noms
6. Supprimer l’espace de noms Standard

>[!NOTE]
> Une fois la migration a été validée, il est extrêmement important pour accéder à l’ancien espace de noms Standard et vider les files d’attente et les abonnements.
>
> Une fois que les messages ont été purgées qu’ils peuvent être envoyés vers le nouvel espace de noms premium pour être traités par les applications de destinataire.
>
> Une fois que les files d’attente et les abonnements ont été purgées, nous vous recommandons de supprimer l’ancien espace de noms Standard. Vous n’être qui en ont besoin !

### <a name="migrate-using-azure-cli-or-powershell"></a>Migrer à l’aide d’Azure CLI ou PowerShell

Pour migrer votre espace de noms Service Bus Standard vers Premium à l’aide de l’outil Azure CLI ou PowerShell, reportez-vous à la ci-dessous guide.

1. Créer un nouvel espace de noms Service Bus Premium. Vous pouvez référencer le [modèles Azure Resource Manager](service-bus-resource-manager-namespace.md) ou [utiliser le portail Azure](service-bus-create-namespace-portal.md). Veillez à sélectionner « Premium » pour le **serviceBusSku** paramètre.

2. Définir le ci-dessous les variables d’environnement pour simplifier les commandes de migration.
   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the Premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the Standard namespace>
   ```

    >[!IMPORTANT]
    > Le nom de la post-migration (post_migration_dns_name) sera utilisé pour accéder à l’espace de noms Standard ancien après la migration. Vous devez utiliser cela pour vider les files d’attente et les abonnements, puis supprimez l’espace de noms.

3. **Paire** les espaces de noms Standard et Premium et **démarrer la synchronisation** à l’aide de la commande - ci-dessous

    ```
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```


4. Vérifier l’état de la migration à l’aide de la commande - ci-dessous
    ```
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    La migration est considérée comme terminée quand
    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = « Réussi »

    Cette commande affiche également la configuration de la migration. Veuillez Vérifiez par deux fois pour vous assurer que les valeurs sont définies comme précédentes est déclaré.

    En outre, vérifiez également l’espace de noms Premium dans le portail pour vous assurer que toutes les files d’attente et rubriques ont été créés et qu’ils correspondent à ce qui existe sur l’espace de noms Standard.

5. Validez la migration en exécutant la commande ci-dessous
   ```
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-using-azure-portal"></a>Migrer à l’aide du portail Azure

Migration via le portail Azure a le même flux logique en tant que la migration à l’aide des commandes. Reportez-vous à la ci-dessous un guide pour le processus étape par étape pour migrer à l’aide du portail.

1. Choisir le **'Migrer vers Premium'** option de menu dans le menu de Navigation dans le volet gauche. Cliquez sur le **'Démarrer'** bouton pour passer à la page suivante.
    ![Page d’accueil de migration][]

2. Complète **le programme d’installation**.
   ![Espace de noms le programme d’installation][]
   1. Créez et affectez l’espace de noms Premium pour migrer l’espace de noms Standard existant vers.
        ![Configurer l’espace de noms : créer l’espace de noms premium][]
   2. Choisir le **'Nom après la Migration'** accéder à l’espace de noms Standard en une fois la migration est terminée.
        ![Configurer l’espace de noms : choisir le nom de la migration post][]
   3. Cliquez sur **'Next'** pour continuer.
3. **Synchronisation** entités entre l’espace de noms Standard et Premium.
    ![Le programme d’installation de l’espace de noms - synchronisation entités - début][]

   1. Cliquez sur **'Démarrer la synchronisation'** pour commencer la synchronisation d’entités.
   2. Cliquez sur **'Yes'** dans la fenêtre contextuelle confirmant pour démarrer la synchronisation.
   3. Attendez que le **synchronisation** est terminée. L’état est mis à disposition sur la barre d’état.
        ![Espace de noms - synchronisation entités - progression de l’installation][]
        >[!IMPORTANT]
        > Si vous avez besoin pour **abandonner** pour une raison quelconque, veuillez consulter le flux d’abandon dans la section Forum aux questions de ce document.
   4. Une fois la synchronisation est terminée, cliquez sur le **'Next'** bouton en bas de la page.

4. Passez en revue les modifications sur la page de résumé.
    ![Espace de noms de commutateur - menu du commutateur][]

5. Cliquez sur **« Terminer la Migration »** pour basculer des espaces de noms et terminer la migration.
    ![Espace de noms de commutateur - réussite][]

## <a name="faqs"></a>FAQ

### <a name="what-happens-when-the-migration-is-committed"></a>Que se passe-t-il lorsque la migration est validée ?

Une fois la migration est validée, la chaîne de connexion qui pointe vers l’espace de noms Standard pointera vers l’espace de noms Premium.

Les applications de l’expéditeur et le destinataire seront Déconnectez le Namespace Standard et se reconnecter automatiquement à l’espace de noms Premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Que faire faire une fois terminée la norme pour la migration Premium ?

La norme pour la migration Premium permet de s’assurer que les métadonnées d’entité (rubriques, abonnements, filtres, et al.) sont copiés à partir de la norme dans l’espace de noms Premium. Les données du message qui a été validées dans l’espace de noms Standard ne sont pas copiées à partir de la norme à l’espace de noms Premium.

Pour cette raison, l’espace de noms Standard peut avoir des messages qui ont été envoyés et validées lors de la migration en cours d’exécution. Ces messages doivent être purgées à partir de la norme Namespace manuellement et envoyés sur le Namespace Premium manuellement.

Pour ce faire, vous ***doit*** utiliser une application console ou un script qui drainer les entités de l’espace de noms Standard en utilisant le **nom DNS de Migration Post** spécifié dans les commandes de migration et d’envoyer ces messages sur le Namespace Premium, afin qu’ils peuvent être traités par les destinataires.

Une fois que les messages ont été purgées, passez à supprimer l’espace de noms Standard.

>[!IMPORTANT]
> Notez qu’une fois que les messages à partir de l’espace de noms Standard ont été purgées, vous **doit** supprimer l’espace de noms Standard.
>
> Ceci est important, car la chaîne de connexion initialement appelée à l’espace de noms Standard fait en fait référence à l’espace de noms Premium. Vous ne besoin cette Namespace Standard plus.
>
> Suppression de l’espace de noms Standard que vous avez migré permet réduit la confusion à une date ultérieure. 

### <a name="how-much-downtime-do-i-expect"></a>Le temps d’inactivité attendre ?
Le processus de migration décrit ci-dessus est destiné à réduire le temps d’arrêt attendus pour les applications. Cela permet à l’aide de la chaîne de connexion que les applications de l’expéditeur et récepteur utilisent pour pointer vers le nouvel espace de noms Premium.

Le temps d’arrêt rencontrés par l’application est limitée à la quantité de temps que nécessaire pour mettre à jour l’entrée DNS pour pointer vers l’espace de noms Premium.

Cela peut être considéré comme pour être ***environ 5 minutes***.

### <a name="do-i-have-to-make-any-configuration-changes-while-performing-the-migration"></a>Je dois apporter des modifications de configuration lors de l’exécution de la migration ?
Non, il n’existe aucune modification de code/configuration nécessaires pour effectuer cette migration. La chaîne de connexion que les applications de l’expéditeur et récepteur utilisent pour accéder le Namespace Standard est automatiquement mappée pour agir comme un **alias** pour le Namespace Premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Que se passe-t-il lorsque j’ai abandonner la migration ?
Migration peut être annulée à l’aide de la commande « Abandonner » ou via le portail Azure. 

#### <a name="azure-cli-or-powershell"></a>Azure CLI ou PowerShell

    az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace

#### <a name="azure-portal"></a>Portail Azure

![Abandonner le flux - abandonner la synchronisation][]
![abandonner flux - abandonner terminée][]

Lorsque le processus de migration est abandonné, elle réellement interrompt le processus de copie sur les entités (rubriques, abonnements et les filtres) à partir de Standard à l’espace de noms Premium et rompt l’association.

La chaîne de connexion **n’est pas** mis à jour pour pointer vers l’espace de noms Premium. Vos applications existantes continuent de fonctionner comme avant de commencer la migration.

Toutefois, il **pas** supprimer les entités dans l’espace de noms Premium ou de supprimer l’espace de noms Premium lui-même. Cela doit être effectuée manuellement si vous ayez décidé de ne pas entamer la migration après tout.

>[!IMPORTANT]
> Si vous décidez d’abandonner la migration, supprimez le Namespace Premium que vous aviez configuré pour la migration, afin que vous n’êtes pas facturé pour les ressources.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Je ne veux pas obligé de vider les messages. Que faire ?

Il peut y avoir des messages qui sont envoyés par les applications de l’expéditeur et validées vers le stockage sur le Namespace Standard pendant que la migration est en cours, et juste avant la migration est validée.

Étant donné que pendant la migration, la message réel/charge utile de données ne seront pas copiée à partir de la norme dans Premium, ceux-ci doivent être purgées manuellement et ensuite envoyées à l’espace de noms Premium.

Toutefois, si vous pouvez migrer pendant une fenêtre de maintenance planifiée/ménage et que vous ne souhaitez pas vider manuellement et d’envoyer les messages, les applications, suivez les étapes - ci-dessous

1. Arrêtez les applications de l’expéditeur, et autoriser les destinataires traiter les messages qui sont actuellement dans l’espace de noms Standard et vider la file d’attente.
2. Une fois que les files d’attente et les abonnements dans le Namespace Standard sont vides, suivez la procédure décrite ci-dessus pour exécuter la migration à partir de Standard à l’espace de noms Premium.
3. Une fois la migration terminée, vous pouvez redémarrer les applications de l’expéditeur.
4. Les expéditeurs et les destinataires seront désormais se connecter automatiquement avec l’espace de noms Premium.

    >[!NOTE]
    > Le récepteur ne doive pas être arrêté pour la migration.
    >
    > Une fois la migration terminée, les destinataires seront vous déconnecter de l’espace de noms Standard et se connecter automatiquement à l’espace de noms Premium.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [différences entre Standard et Premium Messaging](./service-bus-premium-messaging.md)
* En savoir plus sur les aspects de récupération géo-préparation et de haute disponibilité pour Service Bus Premium [ici](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium)

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
