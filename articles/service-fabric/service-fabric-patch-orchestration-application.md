---
title: Application d’orchestration des correctifs pour Azure Service Fabric | Microsoft Docs
description: Application permettant d’automatiser la mise à jour corrective du système d’exploitation sur un cluster Service Fabric.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: bce4946b62e3678f7fa8d5f7f6431441cc00b252
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762835"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Corriger le système d’exploitation Windows dans votre cluster Service Fabric

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>


> 
> [!IMPORTANT]
> Version 1.2 de l’application. * va la prise en charge sur 30 avril 2019. Mettez à niveau vers la dernière version.


[Les mises à niveau automatiques de l’image du système d’exploitation du groupe de machines virtuelles identiques Azure ](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) correspondent aux meilleures pratiques pour conserver vos systèmes d’exploitation corrigés dans Azure, et l’Application d’Orchestration des correctifs (POA) est un wrapper autour du service Service Fabrics RepairManager Systems qui permet la planification des correctifs de système d’exploitation en fonction de la configuration pour les clusters hébergés ailleurs que sur Azure. POA n’est pas obligatoire pour les clusters hébergés ailleurs que sur Azure, mais la planification de l’installation de correctifs par les Domaines de mise à niveau est nécessaire pour corriger les hôtes de clusters Service Fabric sans temps d’arrêt.

POA est une application Azure Service Fabric qui automatise les mises à jour correctives du système d’exploitation sur un cluster Service Fabric sans temps d’arrêt.

L’application d’orchestration des correctifs offre les fonctionnalités suivantes :

- **Installation automatique de mise à jour du système d’exploitation**. Des mises à jour du système d’exploitation sont téléchargées et installées automatiquement. Les nœuds de cluster sont redémarrés en fonction des besoins sans temps d’arrêt du cluster.

- **Application de correctifs et intégration de l’intégrité adaptées au cluster**. Lors de l’application des mises à jour, l’application d’orchestration des correctifs surveille l’intégrité des nœuds de cluster. Les nœuds de cluster sont mis à niveau l’un après l’autre, ou domaine de mise à niveau après domaine de mise à niveau. Si l’intégrité du cluster se dégrade en raison du processus de mise à jour corrective, la mise à jour est arrêtée pour éviter d’aggraver le problème.

## <a name="internal-details-of-the-app"></a>Détails internes de l’application

L’application d’orchestration des correctifs comprend les sous-composants suivants :

- **Service coordinateur** : Ce service avec état est responsable des aspects suivants :
    - coordination de la tâche Windows Update sur le cluster tout entier ;
    - stockage du résultat des opérations Windows Update accomplies.
- **Service Agent du nœud** : Ce service sans état s’exécute sur tous les nœuds de cluster Service Fabric. Les responsabilités du service sont les suivantes :
    - amorçage du service NT Agent du nœud ;
    - surveillance du service NT Agent du nœud.
- **Service NT Agent du nœud** : Ce service Windows NT s’exécute avec un privilège de niveau supérieur (SYSTEM). À l’opposé, les services Agent du nœud et Coordinateur s’exécutent avec des privilèges inférieurs (service réseau). Le service est chargé de l’exécution sur tous les nœuds de cluster des tâches Windows Update suivantes :
    - désactivation des mises à jour automatiques Windows Update sur le nœud ;
    - téléchargement et installation des mises à jour Windows Update en fonction de la stratégie définie par l’utilisateur ;
    - redémarrage de l’ordinateur après installation des mises à jour Windows Update ;
    - chargement des résultats des mises à jour Windows apportées au service Coordinateur ;
    - génération de rapport d’intégrité en cas d’échec de l’opération une fois toutes les nouvelles tentatives effectuées.

> [!NOTE]
> L’application d’orchestration des correctifs utilise le service système gestionnaire des réparations de Service Fabric pour désactiver ou activer le nœud et effectuer des vérifications d’intégrité. La tâche de réparation créée par l’application d’orchestration des correctifs suit la progression de l’exécution de Windows Update pour chaque nœud.

## <a name="prerequisites"></a>Conditions préalables

> [!NOTE]
> Version minimale de .NET framework requise correspond à 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Activer le service de gestion des réparations (s’il est inactif)

Pour que l’application d’orchestration des correctifs puisse fonctionner, le service système de gestion des réparations doit être activé sur le cluster.

#### <a name="azure-clusters"></a>Clusters Azure

Le service de gestion des réparations est activé par défaut pour les clusters Azure au niveau de durabilité Silver. Le service de gestion des réparations peut être activé ou non pour les clusters Azure au niveau de durabilité Gold, en fonction du moment de leur création. Le service de gestion des réparations est déactivé par défaut pour les clusters Azure au niveau de durabilité Bronze. Si le service est déjà activé, vous pouvez le voir s’exécuter dans la section des services système de Service Fabric Explorer.

##### <a name="azure-portal"></a>Portail Azure
Vous pouvez activer le gestionnaire des réparations à partir du portail Azure au moment de la configuration du cluster. Sélectionnez l’option **Inclure le gestionnaire de réparation** sous **Fonctionnalités complémentaires** lors de la configuration du cluster.
![Image représentant l’activation du gestionnaire des réparations à partir du portail Azure](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Modèle de déploiement Azure Resource Manager
Vous pouvez également utiliser le [modèle de déploiement Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) pour activer le service de gestion des réparations sur les clusters Service Fabric nouveaux et existants. Récupérez le modèle approprié pour le cluster que vous souhaitez déployer. Vous pouvez soit utiliser les exemples de modèles, soit créer un modèle de déploiement Azure Resource Manager personnalisé. 

Pour activer le service de gestion des réparations à l’aide du [modèle de déploiement Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) :

1. Commencez par vérifier que `apiversion` a la valeur `2017-07-01-preview` pour la ressource `Microsoft.ServiceFabric/clusters`. Si ce n’est pas le cas, vous devez mettre à jour `apiVersion` en affectant la valeur `2017-07-01-preview` ou une valeur supérieure :

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. À présent, activez le service de gestion des réparations en ajoutant la section `addonFeatures` suivante après la section `fabricSettings`:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Après avoir mis à jour votre modèle de cluster avec ces modifications, appliquez-les et laissez la mise à niveau s’accomplir. Vous pouvez maintenant voir le service système de gestion des réparations s’exécuter dans votre cluster. Il est appelé `fabric:/System/RepairManagerService` dans la section des services système de Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Clusters locaux autonomes

Vous pouvez utiliser les [paramètres de configuration de cluster Windows autonome](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) pour activer le service de gestion des réparations sur un cluster Service Fabric nouveau ou existant.

Pour activer le service de gestion des réparations :

1. Vérifiez que `apiversion` dans [Configurations de cluster générales](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) est défini sur `04-2017` ou une valeur supérieure :

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. À présent, activez le service de gestion des réparations en ajoutant la section `addonFeatures` suivante après la section `fabricSettings`, comme ci-dessous :

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Mettez à jour le manifeste de cluster avec ces modifications, en utilisant le manifeste de cluster mis à jour, [créez un cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) ou [mettez à niveau la configuration du cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). Une fois que le cluster fonctionne avec le manifeste de cluster mis à jour, vous pouvez voir, sous la section des services système de Service Fabric Explorer, que le service système de gestion des réparations appelé `fabric:/System/RepairManagerService` s’exécute dans votre cluster.

### <a name="configure-windows-updates-for-all-nodes"></a>Configurer les mises à jour Windows pour tous les nœuds

Les mises à jour automatiques Windows peuvent entraîner une perte de disponibilité, car plusieurs nœuds de cluster peuvent redémarrer en même temps. Par défaut, l’application d’orchestration des correctifs tente de désactiver les mises à jour automatiques Windows Update sur chaque nœud du cluster. Toutefois, si les paramètres sont gérés par une stratégie d’administrateur ou de groupe, nous vous recommandons de définir la stratégie Windows Update en optant explicitement pour l’envoi d’une notification avant le téléchargement.

## <a name="download-the-app-package"></a>Télécharger le package de l’application

L’application et les scripts d’installation peuvent être téléchargés à partir de [Lien de l’archive](https://go.microsoft.com/fwlink/?linkid=869566).

L’application au format sfpkg peut être téléchargée à partir de [lien sfpkg](https://aka.ms/POA/POA.sfpkg). Cela s’avère utile pour le [déploiement de l’application basée sur Azure Resource Manager](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Configurer l’application

Vous pouvez configurer le comportement de l’application d’orchestration des correctifs en fonction de vos besoins. Remplacez les valeurs par défaut en entrant le paramètre d’application durant la création ou la mise à jour de l’application. Il est possible de fournir des paramètres d’application en spécifiant `ApplicationParameter` pour les applets de commande `Start-ServiceFabricApplicationUpgrade` ou `New-ServiceFabricApplication`.

|**Paramètre**        |**Type**                          | **Détails**|
|:-|-|-|
|MaxResultsToCache    |long                              | Nombre maximal de résultats d’exécution de Windows Update à mettre en cache. <br>La valeur par défaut est 3 000, en supposant ce qui suit : <br> - Le nombre de nœuds est 20. <br> - Le nombre de mises à jour par mois effectuées sur un nœud est 5. <br> - Le nombre maximal de résultats par opération est 10. <br> - Les résultats des trois derniers mois doivent être stockés. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy indique la stratégie que le service Coordinateur doit utiliser pour installer les mises à jour Windows Update sur les nœuds du cluster Service Fabric.<br>                         Les valeurs autorisées sont les suivantes : <br>                                                           <b>NodeWise</b>. Les mises à jour Windows Update sont installées de façon séquentielle, nœud après nœud. <br>                                                           <b>UpgradeDomainWise</b>. Les mises à jour Windows Update sont installées sur un domaine de mise à niveau à la fois (au maximum, tous les nœuds appartenant à un domaine de mise à niveau peuvent bénéficier de Windows Update).<br> Reportez-vous à la section [FAQ](#frequently-asked-questions) sur la façon de déterminer la stratégie la plus adaptée pour votre cluster.
|LogsDiskQuotaInMB   |long  <br> (Par défaut : 1 024               |Taille maximale en Mo des journaux de l’application d’orchestration des correctifs qui peuvent être conservés localement sur des nœuds.
| WUQuery               | string<br>(Par défaut : « IsInstalled=0 »)                | Requête pour obtenir les mises à jour Windows Update. Pour plus d’informations, voir [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
| InstallWindowsOSOnlyUpdates | Booléen <br> (par défaut : false)                 | Utilisez cet indicateur pour contrôler les mises à jour qui doivent être téléchargées et installées. Les valeurs suivantes sont autorisées <br>True : installe uniquement les mises à jour du système d’exploitation Windows.<br>False : installe toutes les mises à jour disponibles sur l’ordinateur.          |
| WUOperationTimeOutInMinutes | Int <br>(Par défaut : 90)                   | Spécifie le délai d’expiration de toute opération de Windows Update (rechercher, télécharger ou installer). Si l’opération n’est pas terminée dans le délai imparti, elle est abandonnée.       |
| WURescheduleCount     | Int <br> (Par défaut : 5)                  | Nombre maximal de fois que le service replanifie une mise à jour Windows en cas d’échec persistant d’une opération.          |
| WURescheduleTimeInMinutes | Int <br>(Par défaut : 30) | Intervalle auquel le service replanifie une mise à jour de Windows en cas d’échec persistant. |
| WUFrequency           | Chaîne de valeurs séparées par une virgule (par défaut : « Toutes les semaines, Mercredi, 7:00:00 »)     | Fréquence d’installation des mises à jour Windows Update. Le format et les valeurs possibles sont les suivants : <br>-   Mensuelle, JJ,HH:MM:SS, par exemple, Mensuelle, 5,12:22:32.<br>Les valeurs autorisées pour le champ JJ (jour) sont des nombres compris entre la plage 1 à 28 et « last » (dernier). <br> -   Hebdomadaire, JOUR, HH:MM:SS, par exemple, Hebdomadaire, mardi, 12:22:32.  <br> -   Quotidienne, HH:MM:SS, par exemple, Quotidienne, 12:22:32.  <br> -  Aucune  indique que les mises à jour Windows Update ne doivent pas être effectuées.  <br><br> Notez que les heures sont exprimées en UTC.|
| AcceptWindowsUpdateEula | Booléen <br>(Par défaut : true) | Lorsque cet indicateur est défini, l’application accepte le contrat de licence utilisateur final de Windows Update pour le compte du propriétaire de l’ordinateur.              |

> [!TIP]
> Si vous souhaitez qu’une mise à jour Windows Update ait lieu immédiatement, définissez `WUFrequency` par rapport à l’heure de déploiement de l’application. Par exemple, supposons que vous disposez d’un cluster de test de cinq nœuds et que vous prévoyez de déployer l’application vers 17 heures UTC. Si vous estimez que la mise à niveau ou le déploiement de l’application nécessite au maximum 30 minutes, définissez WUFrequency sur « Quotidienne, 17:30:00 »

## <a name="deploy-the-app"></a>Déployer l’application

1. Accomplissez toutes les étapes requises pour préparer le cluster.
2. Déployez l’application d’orchestration des correctifs comme toute autre application Service Fabric. Vous pouvez déployer l’application à l’aide de PowerShell. Procédez de la manière décrite dans [Déployer et supprimer des applications avec PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Pour configurer l’application au moment du déploiement, passez `ApplicationParameter` à l’applet de commande `New-ServiceFabricApplication`. Pour votre commodité, nous vous avons fourni le script Deploy.ps1 avec l’application. Pour utiliser le script :

    - Connectez-vous à un cluster Service Fabric en utilisant `Connect-ServiceFabricCluster`.
    - Exécutez le script PowerShell Deploy.ps1 avec la valeur `ApplicationParameter` appropriée.

> [!NOTE]
> Conservez le script et le dossier d’application PatchOrchestrationApplication dans le même répertoire.

## <a name="upgrade-the-app"></a>Mettre à niveau l’application

Pour mettre à niveau une application d’orchestration des correctifs existante à l’aide de PowerShell, procédez de la manière décrite dans [Mise à niveau d’applications Service Fabric à l’aide de PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Supprimer l’application

Pour supprimer l’application, procédez de la manière décrite dans [Déployer et supprimer des applications avec PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Pour votre commodité, nous avons fourni le script Undeploy.ps1 avec l’application. Pour utiliser le script :

  - Connectez-vous à un cluster Service Fabric en utilisant ```Connect-ServiceFabricCluster```.

  - Exécutez le script PowerShell Undeploy.ps1.

> [!NOTE]
> Conservez le script et le dossier d’application PatchOrchestrationApplication dans le même répertoire.

## <a name="view-the-windows-update-results"></a>Afficher les résultats des mises à jour Windows Update

L’application d’orchestration des correctifs expose les API REST pour afficher l’historique des résultats à l’utilisateur. Exemple de JSON de résultat :
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2017-05-21T11:46:52.1953713Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

Les champs de l’objet JSON sont décrits ci-dessous.

Champ | Valeurs | Détails
-- | -- | --
OperationResult | 0 - Réussi<br> 1 - Réussi avec erreurs<br> 2 - Échec<br> 3 - Abandonné<br> 4 - Abandonné avec délai d’expiration | Indique le résultat de l’opération globale (généralement impliquant l’installation d’une ou de plusieurs mises à jour).
ResultCode | Identique à OperationResult | Ce champ indique le résultat de l’opération d’installation pour une mise à jour individuelle.
OperationType | 1 - Installation<br> 0 - Rechercher et télécharger.| L’installation est le seul OperationType qui s’affiche dans les résultats par défaut.
WindowsUpdateQuery | La valeur par défaut est « IsInstalled=0 » |Requête Windows Update utilisée pour rechercher les mises à jour. Pour plus d’informations, voir [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
RebootRequired | true : le redémarrage était requis<br> false : le redémarrage n’était pas requis | Indique si le redémarrage était requis pour terminer l’installation des mises à jour.

Si aucune mise à jour n’est planifiée, le JSON de résultat est vide.

Connectez-vous au cluster pour interroger les résultats des mises à jour Windows Update. Déterminez ensuite l’adresse de réplica pour le serveur principal du service Coordinateur, puis accédez à l’URL à partir du navigateur : http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

Le point de terminaison REST pour le service Coordinateur a un port dynamique. Pour vérifier l’URL exacte, reportez-vous à Service Fabric Explorer. Par exemple, les résultats sont disponibles à l’adresse `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Image de point de terminaison REST](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Si le proxy inverse est activé sur le cluster, vous pouvez accéder à l’URL également à partir de l’extérieur du cluster.
Le point de terminaison à atteindre est http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Pour activer le proxy inverse sur le cluster, procédez de la manière décrite dans [Proxy inverse dans Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> Une fois le proxy inverse configuré, tous les microservices au sein du cluster, qui exposent un point de terminaison HTTP, sont adressables à partir de l’extérieur du cluster.

## <a name="diagnosticshealth-events"></a>Événements de diagnostic et d’intégrité

### <a name="diagnostic-logs"></a>Journaux de diagnostic

Les journaux de l’application d’orchestration des correctifs sont collectés en même temps que les journaux du runtime Service Fabric.

Si vous le souhaitez, vous pouvez capturer les journaux au moyen du pipeline ou de l’outil de diagnostic de votre choix. L’application d’orchestration des correctifs utilise les ID de fournisseur fixes ci-dessous pour journaliser les événements par le biais [d’eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Rapports d'intégrité

L’application d’orchestration des correctifs publie également des rapports d’intégrité concernant le service Coordinateur ou le service Agent du nœud dans les cas suivants :

#### <a name="a-windows-update-operation-failed"></a>Une opération de Windows Update a échoué

Si une opération de Windows Update échoue sur un nœud, un rapport d’intégrité est généré concernant le service Agent du nœud. Les détails du rapport d’intégrité contiennent le nom du nœud problématique.

Une fois la mise à jour corrective effectuée avec succès sur le nœud problématique, le rapport est automatiquement effacé.

#### <a name="the-node-agent-ntservice-is-down"></a>Le service NT Agent du nœud est inactif

Si le service NT Agent du nœud est inactif sur un nœud, un rapport d’intégrité de niveau avertissement est généré concernant le service Agent du nœud.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Le service de gestion des réparations n’est pas activé

Si le service de gestion des réparations est introuvable sur le cluster, un rapport d’intégrité de niveau avertissement est généré concernant le service Coordinateur.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

Q. **Pourquoi mon cluster présente-t-il un état d’erreur lorsque l’application d’orchestration des correctifs est en cours d’exécution ?**

R. Pendant le processus d’installation, l’application d’orchestration des correctifs désactive ou redémarre des nœuds, ce qui peuvent entraîner une dégradation temporaire de l’intégrité du cluster.

Selon la stratégie définie pour l’application, une opération de mise à jour corrective peut entraîner la dégradation d’un nœud isolé *ou* d’un domaine de mise à niveau entier.

À la fin de l’installation des mises à jour Windows Update, les nœuds sont réactivés après redémarrage.

Dans l’exemple suivant, le cluster a présenté temporairement un état d’erreur, car deux nœuds ont été désactivés et la stratégie MaxPercentageUnhealthyNodes a été enfreinte. L’erreur persiste temporairement jusqu’à ce que l’opération de mise à jour corrective soit en cours.

![Image de cluster défectueux](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Si le problème persiste, voir la section Résolution des problèmes.

Q. **L’application d’orchestration des correctifs est en état d’avertissement**

R. Vérifiez si un rapport d’intégrité publié concernant l’application est la cause première. En règle générale, l’avertissement contient des détails concernant le problème. Si le problème est temporaire, l’application est supposée récupérer automatiquement de cet état.

Q. **Que faire si mon cluster est défectueux et que je dois effectuer une mise à jour urgente du système d’exploitation ?**

R. L’application d’orchestration des correctifs n’installe pas de mises à jour lorsque le cluster est défectueux. Essayez de ramener votre cluster à un état sain pour débloquer le flux de travail de l’application d’orchestration des correctifs.

Q. **Dois-je définir TaskApprovalPolicy en tant que « NodeWise » ou « UpgradeDomainWise » pour mon cluster ?**

R. « UpgradeDomainWise » accélère la mise à jour corrective du cluster global en corrigeant tous les nœuds appartenant à un domaine de mise à niveau en parallèle. Cela signifie que les nœuds appartenant à un domaine de mise à niveau entier sont indisponibles (en état [Désactivé](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)) pendant le processus de mise à jour corrective.

En revanche la stratégie « NodeWise » ne corrige qu’un seul nœud à la fois, ce qui implique que la mise à jour corrective du cluster global prend plus de temps. En revanche, au maximum un nœud est indisponible (en état [Désactivé](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)) pendant le processus de mise à jour corrective.

Si votre cluster peut tolérer l’exécution sur le nombre N-1 de domaines de mise à niveau durant le cycle de mise à jour corrective (où N est le nombre total de domaines de mise à niveau sur votre cluster), vous pouvez définir la stratégie en tant que « UpgradeDomainWise ». Autrement, affectez-lui la valeur « NodeWise ».

Q. **Combien de temps prend la correction d’un nœud ?**

R. La mise à jour corrective d’un nœud peut prendre plusieurs minutes (par exemple : [Mises à jour des définitions de Windows Defender](https://www.microsoft.com/wdsi/definitions)) à plusieurs heures (par exemple : [Mises à jour cumulatives de Windows](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Le temps nécessaire pour appliquer un correctif à un nœud dépend principalement des éléments suivants : 
 - Taille des mises à jour
 - Nombre de mises à jour qui doivent être appliquées dans une fenêtre de mise à jour corrective.
 - Temps nécessaire pour installer les mises à jour, redémarrer le nœud (si nécessaire), puis achever les étapes d’installation après redémarrage.
 - Performances de machine virtuelle/conditions de l’ordinateur et du réseau.

Q. **Combien de temps faut-il pour appliquer un correctif à un cluster entier ?**

R. Le temps nécessaire pour appliquer un correctif à un cluster entier dépend des facteurs suivants :

- Temps nécessaire pour appliquer un correctif à un nœud.
- La stratégie du service Coordinateur. - La stratégie par défaut, `NodeWise`, entraîne la mise à jour corrective d’un nœud à la fois, ce qui est plus lent que `UpgradeDomainWise`. Par exemple :  Si l’application d’un correctif à un nœud prend environ 1 heure, pour appliquer un correctif à un cluster de 20 nœuds (du même type) avec 5 domaines de mise à niveau, contenant 4 nœuds chacun.
    - Il faut environ 20 heures pour appliquer le correctif au cluster entier si la stratégie est `NodeWise`.
    - Il faut environ 5 heures si la stratégie est `UpgradeDomainWise`.
- Charge du cluster : chaque opération de mise à jour corrective requiert un déplacement de la charge de travail client vers d’autres nœuds disponibles dans le cluster. Pendant ce temps, le nœud auquel est appliqué le correctif est en état de [désactivation](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling). Si le cluster exécute une charge proche du pic, le processus de désactivation prend plus de temps. Par conséquent, le processus de mise à jour corrective global peut sembler lent dans de telles conditions de sollicitation.
- Échecs d’intégrité du cluster durant la mise à jour corrective : toute [dégradation](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) de l’[intégrité du cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) interrompt le processus de mise à jour corrective. Cela s’ajoute au temps global nécessaire pour appliquer le correctif au cluster entier.

Q. **Pourquoi certaines mises à jour sont-elles affichées dans les résultats Windows Update obtenus via les API REST, et non dans l’historique Windows Update de l’ordinateur ?**

R. Certaines mises à jour de produits apparaissent uniquement dans leur historique de correctifs/mises à jour correspondant. Par exemple, les mises à jour de Windows Defender peuvent apparaître ou non dans l’historique de Windows Update sur Windows Server 2016.

Q. **Puis-je utiliser l’application d’orchestration des correctifs pour appliquer un correctif à mon cluster de développement (cluster à un nœud) ?**

R. Non, l’application d’orchestration des correctifs ne peut pas être utilisée pour corriger un cluster à un nœud. Cette limitation est liée à la conception, car [services système de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) ou toute application cliente connaît des interruptions de service et, par conséquent, toute opération de réparation pour la correction ne sera jamais approuvée par le service de gestion des réparations.

## <a name="disclaimers"></a>Clauses d’exclusion de responsabilité

- L’application d’orchestration des correctifs accepte le contrat de licence utilisateur final de Windows Update à la place de l’utilisateur. Il est possible de désactiver ce paramètre dans la configuration de l’application.

- L’application d’orchestration des correctifs collecte des données de télémétrie pour effectuer le suivi de l’utilisation et des performances. La télémétrie de l’application suit la valeur du paramètre de télémétrie du runtime Service Fabric (activé par défaut).

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="a-node-is-not-coming-back-to-up-state"></a>Un nœud ne se réactive pas

**Le nœud peut être bloqué en état de désactivation pour les raisons suivantes** :

Une vérification de sécurité est en attente. Pour résoudre ce problème, assurez-vous que les nœuds en état d’intégrité normal disponibles sont en nombre suffisant.

**Le nœud peut être bloqué en état désactivé pour les raisons suivantes** :

- Le nœud a été désactivé manuellement.
- Le nœud a été désactivé en raison d’un travail en cours sur l’infrastructure Azure.
- Le nœud a été désactivé temporairement par l’application d’orchestration des correctifs afin d’appliquer un correctif au nœud.

**Le nœud peut être bloqué en état inactif pour les raisons suivantes** :

- Le nœud a été mis en état inactif manuellement.
- Le nœud est en cours de redémarrage (qui peut être déclenché par l’application d’orchestration des correctifs).
- Le nœud est en état inactif en raison d’une défaillance de machine virtuelle ou d’ordinateur, ou de problèmes de connectivité réseau.

### <a name="updates-were-skipped-on-some-nodes"></a>Des mises à jour ont été ignorées sur certains nœuds

L’application d’orchestration des correctifs tente d’installer une mise à jour Windows Update conformément à la stratégie de replanification. Le service tente de récupérer le nœud et ignore la mise à jour en vertu de la stratégie d’application.

Dans ce cas, un rapport d’intégrité de niveau avertissement est généré concernant le service Agent du nœud. Le résultat de la mise à jour Windows Update contient également la cause possible de l’échec.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>L’intégrité du cluster présente un état d’erreur pendant l’installation de la mise à jour

Une mise à jour Windows Update défectueuse peut dégrader l’intégrité d’une application ou d’un cluster sur un nœud ou un domaine de mise à niveau particuliers. L’application d’orchestration des correctifs interrompt toute autre opération de Windows Update jusqu’à ce que le cluster retrouve son intégrité.

Un administrateur doit intervenir et déterminer la raison pour laquelle l’application ou le cluster sont devenus défectueux en raison d’une opération de Windows Update.

## <a name="release-notes"></a>Notes de publication

### <a name="version-110"></a>Version 1.1.0
- Version publique

### <a name="version-111"></a>Version 1.1.1
- Correction d’un bogue dans le paramètre SetupEntryPoint de NodeAgentService, qui empêchait l’installation de NodeAgentNTService.

### <a name="version-120"></a>Version 1.2.0

- Résolution de bogues liés au flux de travail de redémarrage du système.
- Résolution d’un bogue lié à la création de tâches RM, suite auquel le contrôle d’intégrité survenant lors de la préparation des tâches de réparation ne s’exécutait pas comme convenu.
- Transition du mode de démarrage du service Windows POANodeSvc, depuis le démarrage automatique vers le démarrage automatique différé.

### <a name="version-121"></a>Version 1.2.1

- Correctif de bogue dans le flux de travail de réduction du cluster. Introduction d’une logique garbage collection pour les tâches de réparation POA appartenant à des nœuds non existants.

### <a name="version-122"></a>Version 1.2.2

- Divers correctifs de bogues.
- Les fichiers binaires sont maintenant signés.
- Ajout du lien sfpkg pour l’application.

### <a name="version-130"></a>Version 1.3.0

- L’affectation de la valeur False à InstallWindowsOSOnlyUpdates installe maintenant toutes les mises à jour disponibles.
- Changement de la logique de désactivation des mises à jour automatiques. Cela résout un bogue à cause duquel les mises à jour automatiques n’étaient pas désactivées sur Server 2016 et ultérieur.
- Contrainte de placement paramétrable pour les deux microservices de l’application d’orchestration des correctifs pour les cas d’utilisation avancés.

### <a name="version-131"></a>Version 1.3.1
- Correction d’une régression où POA 1.3.0 ne fonctionnait pas sur Windows Server 2012 R2 ou antérieur en raison d’une impossibilité de désactiver les mises à jour automatiques. 
- Résolution du bogue avec lequel la configuration InstallWindowsOSOnlyUpdates avait toujours la valeur True.
- Remplacement de la valeur par défaut de InstallWindowsOSOnlyUpdates par la valeur False.

### <a name="version-132"></a>Version 1.3.2
- Correction d’un problème qui a effectué la mise à jour corrective vie-importantes sur un nœud dans le cas où il existe des nœuds de nom qui est le sous-ensemble du nom du nœud actuel. Pour de tels nœuds, il est possible qu’un correctif soit omis ou qu’un redémarrage soit en cours. 
