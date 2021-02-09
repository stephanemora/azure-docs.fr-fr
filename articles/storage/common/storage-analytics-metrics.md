---
title: Mesures Azure Storage Analytics (classique)
description: Découvrez comment utiliser les mesures Storage Analytics dans le Stockage Azure. En savoir plus sur les métriques de transaction et de capacité, leur mode de stockage, leur activation et bien plus encore.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 8f698aadc24d0dc0691743f1d8dd54c5d5fd287e
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220954"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Mesures Azure Storage Analytics (classique)

Au **31 août 2023**, les métriques Storage Analytics, également appelées *métriques classiques* seront retirées. Pour plus d’informations, consultez l’[annonce officielle](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Si vous utilisez des métriques classiques, veillez à passer aux métriques dans Azure Monitor avant cette date. Cet article vous aide à effectuer la transition. 

Le Stockage Azure utilise Storage Analytics pour stocker des mesures qui comprennent les statistiques de transactions agrégées et les données de capacité relatives aux requêtes adressées à un service de stockage. Les transactions sont signalées au niveau de l’opération d’API et au niveau du service de stockage. La capacité est indiquée au niveau du service de stockage. Les données des mesures peuvent être utilisées pour :
- analyser l’utilisation du service de stockage ;
- diagnostiquer les problèmes liés aux requêtes envoyées au service de stockage ;
- améliorer les performances des applications qui utilisent un service.

 Les métriques de Storage Analytics sont activées par défaut pour les nouveaux comptes de stockage. Vous pouvez configurer des métriques dans le [portail Azure](https://portal.azure.com/) à l’aide de PowerShell ou d’Azure CLI. Pour obtenir des instructions pas à pas, consultez [Activer et gérer les métriques Azure Storage Analytics (classique)](./storage-monitor-storage-account.md). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez les opérations Set Service Properties pour activer Storage Analytics pour chaque service.  

> [!NOTE]
> Les mesures Storage Analytics sont disponibles pour le stockage d’objets blob Azure, Azure Files, le stockage File d’attente Azure et le stockage de tables Azure.
> Les mesures Storage Analytics sont maintenant des mesures classiques. Nous vous recommandons d’utiliser les [mesures de stockage dans Azure Monitor](../blobs/monitor-blob-storage.md) au lieu des mesures Storage Analytics.

## <a name="transaction-metrics"></a>Métriques de transaction  
 Un ensemble fiable de données est enregistré toutes les heures ou chaque minute pour chaque service de stockage et opération d’API demandée, notamment les entrées et sorties, la disponibilité, les erreurs et les pourcentages de requête triés par catégorie. La liste complète des détails de transaction est disponible dans la rubrique [Schéma de table de mesures Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Les données de transaction sont enregistrées au niveau du service et au niveau de l’opération API. Au niveau du service, des statistiques résumant toutes les opérations d’API demandées sont écrites dans une entité de table toutes les heures, même si aucune requête n’a été adressée au service. Au niveau de l'opération d'API, les statistiques sont écrites uniquement dans une entité si l'opération a été demandée dans l'heure.  

 Par exemple, si vous exécutez une opération **GetBlob** sur votre service BLOB, les mesures Storage Analytics enregistrent la requête et l’incluent dans les données agrégées pour le service BLOB et l’opération **GetBlob**. Si aucune opération **GetBlob** n’est demandée pendant l’heure, aucune entité n’est écrite dans *$MetricsTransactionsBlob* pour cette opération.  

 Les mesures de transaction sont enregistrées pour les requêtes utilisateur et les demandes effectuées par Storage Analytics. Par exemple, les demandes par Storage Analytics pour écrire des journaux d’activité et des entités de table sont enregistrées.

## <a name="capacity-metrics"></a>Métriques de capacité  

> [!NOTE]
>  Actuellement, les mesures de capacité sont disponibles uniquement pour le service BLOB.

 Les données de capacité sont enregistrées quotidiennement pour le service BLOB d’un compte de stockage, et deux entités de table sont écrites. Une entité fournit des statistiques sur les données utilisateur et l'autre, sur le conteneur d'objets blob `$logs` utilisé par Storage Analytics. La table *$MetricsCapacityBlob* inclut les statistiques suivantes :  

- **Capacity** : Quantité de stockage utilisée par le service BLOB du compte de stockage, en octets.  
- **ContainerCount** : Nombre de conteneurs d’objets blob dans le service BLOB du compte de stockage.  
- **ObjectCount** : Nombre d’objets blob de blocs ou de pages validés et non validés dans le service BLOB du compte de stockage.  

  Pour plus d’informations sur les mesures de capacité, voir [Schéma de table de mesures Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Stockage des métriques  

 Toutes les données de mesures des services de stockage sont stockées dans trois tables réservées à ce service : une table pour les informations sur les transactions, une autre pour les informations sur les transactions par minute, et une troisième pour les informations sur la capacité. Les informations relatives aux transactions et aux transactions par minute se composent des données de requête et de réponse. Quant aux informations de capacité, elles comprennent des données sur l’utilisation du stockage. Les mesures par heure, les mesures par minute et la capacité pour le service blob d’un compte de stockage sont accessibles dans des tables nommées comme indiqué dans la table ci-dessous.  

|Niveau de métriques|Noms de tables|Versions prises en charge|  
|-------------------|-----------------|----------------------------|  
|Métriques toutes les heures, emplacement principal|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|Versions antérieures au 15 août 2013, uniquement. Bien que ces noms soient toujours pris en charge, il est recommandé d’utiliser les tables répertoriées ci-dessous.|  
|Métriques toutes les heures, emplacement principal|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|Toutes les versions. La prise en charge des mesures de service Fichier est disponible uniquement dans la version du 5 avril 2015 et les versions ultérieures.|  
|Métriques par minute, emplacement principal|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|Toutes les versions. La prise en charge des mesures de service Fichier est disponible uniquement dans la version du 5 avril 2015 et les versions ultérieures.|  
|Métriques toutes les heures, emplacement secondaire|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|Toutes les versions. La géo-réplication redondante avec accès en lecture doit être activée.|  
|Métriques par minute, emplacement secondaire|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|Toutes les versions. La géo-réplication redondante avec accès en lecture doit être activée.|  
|Capacité (service BLOB uniquement)|$MetricsCapacityBlob|Toutes les versions.|  

 Ces tables sont automatiquement créées lorsque Storage Analytics est activé pour un point de terminaison de service de stockage. Elles sont accessibles via l’espace de noms du compte de stockage, par exemple : `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Les tables de mesures n’apparaissent pas dans une opération de liste et sont accessibles directement via le nom de la table.

## <a name="metrics-alerts"></a>Alertes liées aux métriques
Il peut être intéressant de définir des alertes dans le [Portail Azure](https://portal.azure.com), afin d’être averti automatiquement de tout changement important de comportement de vos services de stockage. Pour obtenir des instructions pas à pas, consultez [Créer des alertes de métriques](storage-monitor-storage-account.md#create-metric-alerts).

Si vous utilisez un outil Explorateur Stockage pour télécharger ces données de mesures dans un format délimité, vous pouvez utiliser Microsoft Excel pour les analyser. Pour obtenir la liste des outils Explorateur Stockage disponibles, consultez la rubrique relative aux [outils clients du Stockage Azure](./storage-explorers.md).

> [!IMPORTANT]
> Il peut y avoir un décalage entre un événement de stockage et l’enregistrement des données de mesures par heure ou par minute correspondantes. Dans le cas des mesures par minute, plusieurs minutes de données peuvent être écrites à la fois. Cela peut entraîner l’agrégation des transactions des minutes précédentes dans la transaction pour la minute actuelle. Dans ce cas, le service d’alerte peut ne pas disposer de toutes les données de mesures disponibles pour l’intervalle d’alerte configuré, et cela peut entraîner des déclenchements d’alerte inattendus.
>

## <a name="billing-on-storage-metrics"></a>Facturation sur les métriques de stockage
Les demandes d’écriture pour créer des entités de table pour les métriques sont facturées au tarif standard applicable à toutes les opérations Azure Storage.  

Les demandes de lecture et de suppression formulées par un client sur des données de métriques sont aussi facturées au tarif standard. Si vous avez configuré une stratégie de conservation des données, vous n’êtes pas facturé quand le stockage Azure supprime les anciennes données de mesures. Toutefois, si vous supprimez des données analytiques, les opérations de suppression sont facturées à votre compte.  

La capacité utilisée par les tables de métriques est également facturable. Utilisez les informations suivantes pour estimer la capacité utilisée pour stocker les données de mesures :  

-   En une heure, pour un service qui utilise toutes les API de chaque service, environ 148 Ko de données sont stockés par heure dans les tables de transaction de mesures si vous avez activé la synthèse au niveau des services et de l’API.  
-   Si, en une heure, un service utilise toutes les API du service, alors environ 12 Ko de données sont stockés par heure dans les tables de transaction de mesures si vous avez uniquement activé la synthèse au niveau des services.  
-   Deux lignes supplémentaires sont ajoutées chaque jour dans la table de capacité pour les objets blob, si vous avez choisi de recevoir les journaux. Cela implique que la taille de cette table augmente d’environ 300 octets maximum chaque jour.

## <a name="next-steps"></a>Étapes suivantes
* [Surveiller un compte de stockage](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schéma de table de mesures Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Opérations et messages d’état enregistrés Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Journalisation Storage Analytics](storage-analytics-logging.md)