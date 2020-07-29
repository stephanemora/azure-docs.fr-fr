---
title: Empêcher l’accès en lecture public anonyme aux conteneurs et aux blobs
titleSuffix: Azure Storage
description: Découvrez comment analyser les requêtes anonymes sur un compte de stockage et comment empêcher l’accès anonyme pour l’ensemble du compte de stockage ou pour un conteneur spécifique.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 24d726f7600c3ba80833640be8036bf0daa2c014
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518722"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Empêcher l’accès en lecture public anonyme aux conteneurs et aux blobs

L’accès en lecture public anonyme aux conteneurs et aux blobs dans le stockage Azure est un moyen pratique de partager des données, mais peut également présenter un risque pour la sécurité. Il est important de gérer judicieusement l’accès anonyme et de comprendre la manière d’évaluer l’accès anonyme à vos données. La complexité opérationnelle, une erreur humaine ou une attaque malveillante contre les données accessibles publiquement peuvent entraîner des violations de données coûteuses. Microsoft vous recommande d’activer l’accès anonyme uniquement quand cela est nécessaire pour votre scénario d’application.

Par défaut, un utilisateur disposant des autorisations appropriées peut configurer un accès public aux conteneurs et aux blobs. Vous pouvez empêcher tout accès public au niveau du compte de stockage. Lorsque vous interdisez l’accès public aux blobs pour le compte de stockage, les conteneurs du compte ne peuvent pas être configurés pour un accès public. Les conteneurs qui ont déjà été configurés pour un accès public n’acceptent plus les requêtes anonymes. Pour en savoir plus, consultez la section [Configure anonymous public read access for containers and blobs](anonymous-read-access-configure.md) (Configurer l’accès en lecture publique anonyme pour les conteneurs et les objets blob).

Cet article explique comment analyser les demandes anonymes sur un compte de stockage et comment empêcher l’accès anonyme pour l’ensemble du compte de stockage ou pour un conteneur spécifique.

## <a name="detect-anonymous-requests-from-client-applications"></a>Détecter les demandes anonymes des applications clientes

Quand vous interdisez l’accès en lecture public pour un compte de stockage, vous risquez de rejeter les requêtes à destination des conteneurs et des blobs qui sont configurés pour l’accès public. L’interdiction de l’accès public pour un compte de stockage remplace les paramètres d’accès public pour tous les conteneurs appartenant à ce compte de stockage. Quand l’accès public est interdit pour le compte de stockage, toute requête anonyme ultérieure adressée à ce compte échoue.

Pour que vous compreniez dans quelle mesure l’interdiction de l’accès public peut influer sur les applications clientes, Microsoft vous recommande d’activer la journalisation et les métriques pour ce compte et d’analyser les modèles de requêtes anonymes sur un intervalle de temps. Utilisez des métriques pour déterminer le nombre de demandes anonymes adressées au compte de stockage et utilisez les journaux pour déterminer les conteneurs qui sont accessibles de façon anonyme.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Superviser les demandes anonymes avec Metrics Explorer

Pour effectuer le suivi des demandes anonymes adressées à un compte de stockage, utilisez Azure Metrics Explorer dans le portail Azure. Pour plus d’informations sur Metrics Explorer, consultez [Prise en main d’Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

Suivez ces étapes pour créer une métrique qui effectue le suivi des requêtes anonymes :

1. Accédez à votre compte de stockage dans le portail Azure. Dans la section **Supervision**, sélectionnez **Métriques**.
1. Sélectionnez **Ajouter une métrique**. Dans la boîte de dialogue **Métrique**, spécifiez les valeurs suivantes :
    1. Laissez le champ Étendue défini sur le nom du compte de stockage.
    1. Définissez l’**Espace de noms de métrique** sur *Blob*. Cette métrique ne porte que sur les demandes à destination du stockage Blob.
    1. Définissez le champ **Métrique** sur *Transactions*.
    1. Définissez le champ **Agrégation** sur *Somme*.

    La nouvelle métrique affiche la somme du nombre de transactions sur le stockage Blob sur un intervalle de temps donné. La métrique qui en résulte s’affiche comme indiqué dans l’image suivante :

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Capture d’écran montrant comment configurer une métrique pour additionner les transactions blob":::

1. Ensuite, sélectionnez le bouton **Ajouter un filtre** afin de créer un filtre sur la métrique pour les demandes anonymes.
1. Dans la boîte de dialogue **Filtre**, spécifiez les valeurs suivantes :
    1. Définissez la valeur **Propriété** sur *Authentification*.
    1. Affectez au champ **Opérateur** le signe égal (=).
    1. Définissez le champ **Valeurs** sur *Anonyme*.
1. Dans l’angle supérieur droit, sélectionnez l’intervalle de temps sur lequel doit porter la métrique. Vous pouvez également indiquer le degré de précision de l’agrégation des demandes, en spécifiant des intervalles compris entre 1 minute et 1 mois.

Une fois que vous avez configuré la métrique, les demandes anonymes commencent à s’afficher sur le graphique. L’illustration suivante montre les demandes anonymes agrégées sur les trente dernières minutes.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Capture d’écran montrant les demandes anonymes agrégées sur le stockage Blob":::

Vous pouvez également configurer une règle d’alerte pour vous avertir quand un certain nombre de demandes anonymes sont effectuées sur votre compte de stockage. Pour plus d'informations, consultez [Créer, afficher et gérer des alertes de métrique à l'aide d'Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Analyser les journaux pour identifier les conteneurs recevant des demandes anonymes

Les journaux du stockage Azure capturent des détails sur les demandes effectuées sur le compte de stockage, y compris la manière dont une demande a été autorisée. Vous pouvez analyser les journaux pour identifier les conteneurs qui reçoivent des demandes anonymes.

Pour journaliser les demandes dans votre compte de stockage Azure afin d’évaluer les demandes anonymes, vous pouvez utiliser la journalisation du stockage Azure dans Azure Monitor (préversion). Pour plus d’informations, consultez [Superviser le stockage Azure](../common/monitor-storage.md).

La journalisation du stockage Azure dans Azure Monitor prend en charge l’utilisation de requêtes de journal pour analyser les données des journaux. Pour interroger les journaux, vous pouvez utiliser un espace de travail Azure Log Analytics. Pour en savoir plus sur les requêtes de journal, consultez [Tutoriel : Bien démarrer avec les requêtes Log Analytics](../../azure-monitor/log-query/get-started-portal.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Créer un paramètre de diagnostic dans le portail Azure

Pour journaliser des données de stockage Azure avec Azure Monitor et les analyser avec Azure Log Analytics, vous devez d’abord créer un paramètre de diagnostic qui indique les types de demandes et les services de stockage pour lesquels vous souhaitez journaliser les données. Pour créer un paramètre de diagnostic dans le portail Azure, suivez ces étapes :

1. Inscrivez-vous à la [journalisation de stockage Azure dans Azure Monitor (préversion)](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Créez un espace de travail Log Analytics dans l’abonnement qui contient votre compte de stockage Azure. Une fois que vous avez configuré la journalisation pour votre compte de stockage, les journaux sont disponibles dans l’espace de travail Log Analytics. Pour plus d’informations, consultez [Créer un espace de travail Log Analytics dans le portail Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. Accédez à votre compte de stockage dans le portail Azure.
1. Dans la section Supervision, Sélectionnez **Paramètres de diagnostic (préversion)** .
1. Sélectionnez **Blob** pour journaliser les demandes effectuées sur le stockage Blob.
1. Sélectionnez **Ajouter le paramètre de diagnostic**.
1. Fournissez un nom pour le paramètre de diagnostic.
1. Sous **Détails de la catégorie**, dans la section **Journal**, choisissez les types de demandes à journaliser. Toutes les demandes anonymes étant des demandes de lecture, sélectionnez **StorageRead** pour capturer les demandes anonymes.
1. Sous **Détails de la destination**, sélectionnez **Envoyer à Log Analytics**. Sélectionnez votre abonnement et l’espace de travail Log Analytics que vous avez créé, comme illustré dans l’image suivante.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Capture d’écran montrant comment créer un paramètre de diagnostic pour la journalisation des demandes":::

Une fois le paramètre de diagnostic créé, les demandes adressées au compte de stockage sont journalisées conformément à ce paramètre. Pour plus d’informations, consultez [Créer un paramètre de diagnostic pour collecter les journaux et les métriques des ressources dans Azure](../../azure-monitor/platform/diagnostic-settings.md).

Pour obtenir des informations de référence sur les champs disponibles dans les journaux de stockage Azure dans Azure Monitor, consultez [Journaux de ressource (préversion)](../common/monitor-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Interroger les journaux pour rechercher les demandes anonymes

Les journaux de stockage Azure dans Azure Monitor incluent le type d’autorisation qui a été utilisé pour effectuer une demande à destination d’un compte de stockage. Dans votre requête de journal, filtrez sur la propriété **AuthenticationType** pour afficher les demandes anonymes.

Pour récupérer les journaux des 7 derniers jours pour les demandes anonymes sur le stockage Blob, ouvrez votre espace de travail Log Analytics. Collez ensuite la requête suivante dans une nouvelle requête de journal et exécutez-la :

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

Vous pouvez également configurer une règle d’alerte basée sur cette requête pour vous avertir des demandes anonymes. Pour plus d’informations, consultez [Créer, afficher et gérer des alertes de journal à l’aide d’Azure Monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Corriger l’accès public anonyme

Une fois que vous avez évalué les demandes anonymes destinées aux conteneurs et aux blobs dans votre compte de stockage, vous pouvez prendre des mesures pour limiter ou empêcher l’accès public. Si certains conteneurs de votre compte de stockage doivent être disponibles pour un accès public, vous pouvez configurer le paramètre d’accès public pour chaque conteneur dans votre compte de stockage. Cette option fournit le contrôle le plus précis de l’accès public. Pour plus d’informations, consultez [Définir le niveau d’accès public pour un conteneur](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container).

Pour renforcer la sécurité, vous pouvez interdire l’accès public pour l’ensemble du compte de stockage. Le paramètre d’accès public pour un compte de stockage remplace les paramètres individuels des conteneurs dans ce compte. Quand vous interdisez l’accès public pour un compte de stockage, tous les conteneurs qui sont configurés pour autoriser l’accès public ne sont plus accessibles de façon anonyme. Pour plus d’informations, consultez [Autoriser ou interdire l’accès en lecture public pour un compte de stockage](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account).

Si votre scénario nécessite que certains conteneurs soient disponibles pour un accès public, il peut être préférable de déplacer ces conteneurs et leurs blobs dans des comptes de stockage réservés à un accès public. Vous pouvez ensuite interdire l’accès public pour tous les autres comptes de stockage.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Vérifier que l’accès public à un blob n’est pas autorisé

Pour vérifier que l’accès public à un blob spécifique est interdit, vous pouvez essayer de télécharger le blob via son URL. Si le téléchargement s’effectue correctement, le blob est toujours accessible publiquement. Si le blob n’est pas accessible publiquement parce que l’accès public a été interdit pour le compte de stockage, un message d’erreur s’affiche, indiquant que l’accès public n’est pas autorisé sur ce compte.

L’exemple suivant montre comment utiliser PowerShell pour essayer de télécharger un blob via son URL. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Vérifier que la modification du paramètre d’accès public du conteneur n’est pas autorisée

Pour vérifier que le paramètre d’accès public d’un conteneur ne peut pas être modifié après l’interdiction de l’accès public pour le compte de stockage, vous pouvez essayer de modifier le paramètre. La modification du paramètre d’accès public du conteneur échoue si l’accès public est interdit pour le compte de stockage.

L’exemple suivant montre comment utiliser PowerShell pour tenter de changer le paramètre d’accès public d’un conteneur. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Vérifier que la création d’un conteneur avec accès public activé n’est pas autorisée

Si l’accès public est interdit pour le compte de stockage, vous ne pouvez pas créer de conteneur avec un accès public autorisé. Pour vérifier, vous pouvez essayer de créer un conteneur avec accès public activé.

L’exemple suivant montre comment utiliser PowerShell pour essayer de créer un conteneur avec accès public activé. N’oubliez pas de remplacer les valeurs d’espace réservé entre crochets par vos propres valeurs :

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

## <a name="next-steps"></a>Étapes suivantes

- [Configurer l’accès en lecture public anonyme pour les conteneurs et les objets blob](anonymous-read-access-configure.md)
- [Accéder anonymement aux conteneurs et objets blob publics avec .NET](anonymous-read-access-client.md)
