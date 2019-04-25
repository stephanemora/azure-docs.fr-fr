---
title: Intégrer les journaux d’activité d’Azure Key Vault à l’aide d’Event Hubs | Microsoft Docs
description: Didacticiel décrivant la procédure requise pour rendre accessibles les journaux d’activité Key Vault à un système SIEM (Security Information and Event Management, système de gestion des événements et des informations de sécurité) grâce à la solution d’intégration des journaux d’activité Azure
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 01/14/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 7e70920e806b3d9838d693ff1fc74a3e9371319d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60445218"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Tutoriel Azure Log Integration : Traiter les événements Azure Key Vault avec Event Hubs

>[!IMPORTANT]
> La fonctionnalité d’intégration des journaux Azure sera déconseillée à partir du 01/06/2019. Les téléchargements AzLog ont été désactivés le 27 juin 2018. Pour obtenir des conseils pour évoluer, consultez la publication [Utiliser Azure Monitor pour intégrer avec des outils SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/). 

Vous pouvez utiliser la solution d’intégration des journaux Azure pour récupérer des événements journalisés et les rendre accessibles à votre système de gestion des événements et des informations de sécurité (SIEM, Security Information and Event Management). Ce didacticiel montre comment utiliser Azure Log Integration pour traiter les fichiers journaux d’activité qui sont acquis par le service Azure Event Hubs.

La méthode recommandée pour intégrer des journaux d’activité Azure consiste à utiliser le connecteur Azure Monitor de votre fournisseur SIEM et à suivre les [instructions](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) ci-après. Toutefois, si votre fournisseur SIEM ne propose pas de connecteur pour Azure Monitor, vous pouvez utiliser le service Azure Log Integration de façon temporaire (s’il prend en charge votre système SIEM) jusqu’à ce qu’un tel connecteur soit disponible.

 
Ce didacticiel aide à mieux comprendre comment Azure Log Integration et Event Hubs peuvent être utilisés conjointement, et explique chacune des étapes impliquées. Vous pouvez ensuite vous appuyer sur ce que vous avez appris ici pour répondre aux besoins spécifiques de votre entreprise.

> [!WARNING]
> Les étapes et commandes de ce didacticiel ne sont pas destinées à être copiées et collées. Elles sont uniquement indiquées à titre d’exemple. N’utilisez pas les commandes PowerShell telles quelles dans votre environnement de production. Vous devez en effet les personnaliser en fonction de votre environnement.


Ce didacticiel vous guide tout au long de la procédure qui consiste à récupérer des activités Azure Key Vault journalisées dans un Event Hub et à mettre ces activités à la disposition de votre système SIEM sous la forme de fichiers JSON. Vous pouvez ensuite configurer votre système SIEM pour qu’il procède au traitement de ces fichiers JSON.

>[!NOTE]
>La plupart des étapes de ce didacticiel impliquent la configuration de coffres de clé, de comptes de stockage et d’Event Hubs. La procédure d’intégration des journaux Azure est décrite à la fin de ce didacticiel. N’effectuez pas ces étapes dans un environnement de production, car elles sont uniquement destinées à un environnement lab. Vous devez les adapter avant de les utiliser dans un environnement de production.

Les informations fournies tout au long de la procédure vous expliquent la finalité de chaque étape. Les liens d’accès à d’autres articles offrent des détails complémentaires sur certains sujets.

Pour plus d’informations sur les services mentionnés par ce didacticiel, consultez les articles suivants : 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Intégration des journaux Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Configuration initiale

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pour exécuter la procédure décrite dans cet article, vous devez disposer des éléments suivants :

* Un abonnement Azure et un compte dans cet abonnement avec des droits d’administrateur. Si vous ne disposez d’aucun abonnement, vous pouvez créer [un compte gratuitement](https://azure.microsoft.com/free/).
 
* Un système doté d’un accès à Internet qui présente la configuration requise pour l’installation d’Azure Log Integration. Ce système peut se trouver dans un service cloud ou être hébergé localement.

* Azure Log Integration installée. Pour l’installer :

   a. Utilisez la fonctionnalité Bureau à distance pour vous connecter au système mentionné à l’étape 2.   
   b. Copiez le programme d’installation de l’intégration des journaux Azure sur le système. c. Démarrez le programme d’installation et acceptez les Termes du contrat de licence logiciel Microsoft.

* Si vous souhaitez fournir des informations de télémétrie, laissez la case cochée. Si vous préférez ne pas envoyer d’informations d’utilisation à Microsoft, décochez la case.

   Pour plus d’informations sur Azure Log Integration et sur son installation, consultez l’article [Intégration des journaux Azure avec Azure Diagnostics Logging et Windows Event Forwarding](security-azure-log-integration-get-started.md).

* La dernière version de PowerShell.

   Si vous avez installé Windows Server 2016, vous disposez au moins de PowerShell 5.0. Si vous utilisez une autre version de Windows Server, il est possible que vous possédiez une version antérieure de PowerShell. Pour vérifier la version que vous utilisez, tapez ```get-host``` dans une fenêtre PowerShell. Si vous n’avez pas installé PowerShell 5.0, vous pouvez [le télécharger](https://www.microsoft.com/download/details.aspx?id=50395).

   Une fois que vous avez au moins PowerShell 5.0, vous pouvez passer à installer la dernière version en suivant les instructions dans [installer Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="create-supporting-infrastructure-elements"></a>Créer les éléments d’infrastructure sous-jacents

1. Ouvrez une fenêtre PowerShell avec élévation de privilèges et accédez à **C:\Program Files\Microsoft Azure Log Integration**.
1. Importez les applets de commande AzLog en exécutant le script LoadAzLogModule.ps1. Entrez la commande `.\LoadAzLogModule.ps1`. (Notez la présence du caractère « .\" dans cette commande.) Le résultat suivant devrait s'afficher :</br>

   ![Liste des modules chargés](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Entrez la commande `Connect-AzAccount`. Dans la fenêtre de connexion, entrez les informations d’identification de l’abonnement que vous allez utiliser dans le cadre de ce didacticiel.

   >[!NOTE]
   >S’il s’agit de votre première connexion à Azure à partir de cette machine, vous verrez un message destiné à autoriser Microsoft à collecter les données d’utilisation de PowerShell. Nous vous recommandons d’autoriser cette collecte de données, car elle nous permettra d’améliorer Azure PowerShell.

1. Après une authentification réussie, vous êtes connecté. Notez l’ID et le nom de l’abonnement, car vous aurez besoin de ces éléments dans la suite de cette procédure.

1. Créez des variables pour stocker les valeurs qui seront utilisées par la suite. Entrez chacune des lignes PowerShell suivantes. Vous devrez peut-être ajuster les valeurs pour les faire correspondre à votre environnement.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` (Votre nom d’abonnement peut être différent. Vous pouvez le voir apparaître dans la sortie de la commande précédente.)
    - ```$location = 'West US'``` (Cette variable est utilisée pour transmettre l’emplacement où les ressources doivent être créées. Vous pouvez redéfinir cette variable sur tout autre emplacement de votre choix.)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random``` (Le nom peut correspondre à une chaîne quelconque, mais doit uniquement inclure des lettres minuscules et des chiffres.)
    - ```$storageName = $name``` (Cette variable est utilisée pour le nom du compte de stockage.)
    - ```$rgname = $name``` (Cette variable est utilisée pour le nom du groupe de ressources.)
    - ```$eventHubNameSpaceName = $name``` (Cette variable correspond au nom de l’espace de noms Event Hub.)
1. Spécifiez l’abonnement que vous allez utiliser :
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Créez un groupe de ressources :
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Si vous tapez `$rg` à ce stade, vous verrez apparaître une sortie semblable à la capture d’écran ci-après :

   ![Sortie après la création d’un groupe de ressources](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
1. Créez un compte de stockage qui servira à effectuer le suivi des informations d’état :
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Créez l’espace de noms Event Hub. Cette opération est requise pour la création d’un Event Hub.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Obtenez l’ID de règle qui sera utilisé avec le fournisseur d’informations :
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Obtenez tous les emplacements Azure possibles et ajoutez les noms à une variable que vous pourrez utiliser à une étape ultérieure :
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Si vous entrez `$locations` à ce stade, vous verrez les noms d’emplacement sans les informations supplémentaires renvoyées par Get-AzLocation.
1. Créez un profil de journal Azure Resource Manager : 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Pour plus d’informations sur le profil de journal Azure, consultez l’article [Présentation du journal d’activité Azure](../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Il est possible que vous obteniez un message d’erreur lorsque vous essayez de créer un profil de journal. Vous pouvez alors consulter la documentation pour Get-AzLogProfile et Remove-AzLogProfile. Si vous exécutez Get-AzLogProfile, des informations sur le profil de journal. Vous pouvez supprimer le profil de journal existant en entrant la commande ```Remove-AzLogProfile -name 'Log Profile Name'```.
>
>![Erreur de profil Resource Manager](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

1. Créez le coffre de clés :

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. Configurez la journalisation relative au coffre de clés :

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Générer l’activité de journalisation

La génération de l’activité de journalisation nécessite l’envoi de requêtes à Key Vault. Des actions telles que la génération de clés, le stockage de secrets ou la lecture de secrets à partir de Key Vault entraînent la création d’entrées de journal.

1. Affichez les clés de stockage actuelles :
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Générez une nouvelle valeur **key2** :
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Réaffichez les clés et notez que l’élément **key2** présente une autre valeur :
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Définissez et lisez un secret pour générer des entrées de journal supplémentaires :
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Secret renvoyé](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Configurer l’intégration des journaux Azure

À présent que vous avez configuré tous les éléments nécessaires pour activer la journalisation Key Vault dans un Event Hub, vous devez configurer l’intégration des journaux Azure :

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Exécutez la commande AzLog pour chaque Event Hub :

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Au bout d’une minute environ après l’exécution des deux dernières commandes, vous devriez voir les fichiers JSON en cours de génération. Pour vérifier ce point, surveillez le répertoire **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Étapes suivantes

- [Forum aux questions sur l’intégration des journaux Azure](security-azure-log-integration-faq.md)
- [Bien démarrer avec l’intégration des journaux Azure](security-azure-log-integration-get-started.md)
- [Intégrer des journaux d’activité à partir de ressources Azure dans vos systèmes SIEM](security-azure-log-integration-overview.md)
