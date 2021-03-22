---
title: Récupérer de volumineux jeux de données de coûts de façon récurrente avec des exportations
description: Cet article vous aide à exporter régulièrement de grandes quantités de données à l’aide d’exportations à partir d’Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 465225341bdffc984ac6cbc82ba94eb656ad60df
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509631"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Récupérer de volumineux jeux de données de coûts de façon récurrente avec des exportations

Cet article vous aide à exporter régulièrement de grandes quantités de données à l’aide d’exportations à partir d’Azure Cost Management. L’exportation est la méthode recommandée pour récupérer des données de coût non agrégées. En particulier, lorsque les fichiers d’utilisation sont trop volumineux pour un appel et un téléchargement fiables avec l’API Détails de l’utilisation. Les données exportées sont placées dans le compte de stockage Azure de votre choix. À partir de là, vous pouvez les charger dans vos propres systèmes, et les analyser en fonction des besoins. Pour configurer des exportations dans le portail Azure, consultez [Exporter des données](tutorial-export-acm-data.md).

Si vous souhaitez automatiser les exportations sur différentes étendues, l’exemple de requête d’API de la section suivante constitue un bon point de départ. Vous pouvez utiliser l’API Exportations pour créer des exportations automatiques dans le cadre de la configuration générale de votre environnement. Les exportations automatiques permettent de vous assurer que vous disposez des données dont vous avez besoin. Vous pouvez vous en servir dans les propres systèmes de votre organisation, à mesure que vous développez votre utilisation d’Azure.

## <a name="common-export-configurations"></a>Configurations d’exportation courantes

Avant de créer votre première exportation, réfléchissez à votre scénario et aux options de configuration nécessaires pour l’activer. Tenez compte des options d’exportation suivantes :

- **Récurrence** : détermine la fréquence d’exécution du travail d’exportation, et le moment où un fichier est placé dans votre compte de stockage Azure. Choisissez entre Quotidienne, Hebdomadaire et Mensuelle. Essayez de configurer votre récurrence pour qu’elle corresponde aux travaux d’importation de données utilisés par le système interne de votre organisation.
- **Période de récurrence** : détermine la durée pendant laquelle l’exportation reste valide. Les fichiers sont exportés uniquement pendant la périodicité.
- **Délai d’exécution** : détermine la quantité de données générée par l’exportation sur une exécution donnée. Les options courantes sont MonthToDate et WeekToDate.
- **Date de début** : configure le moment où vous souhaitez que la planification d’exportation commence. Une exportation est créée à la date de début, puis ultérieurement sur la base de votre périodicité.
- **Type** : il existe trois types d’exportation :
  - ActualCost – Affiche l’utilisation et les coûts totaux de la période spécifiée, tels qu’ils sont comptabilisés et s’affichent sur votre facture.
  - AmortizedCost – Affiche l’utilisation et les coûts totaux de la période spécifiée, avec l’amortissement appliqué aux coûts d’achat de réservation applicables.
  - Usage – Toutes les exportations créées avant le 20 juillet 2020 sont de type Usage. Mettez à jour toutes vos exportations planifiées en tant que ActualCost ou AmortizedCost.
- **Colonnes** : Définit les champs de données que vous souhaitez inclure dans votre fichier d’exportation. Ils correspondent aux champs disponibles dans l’API Détails de l’utilisation. Pour plus d’informations, consultez [API Détails de l’utilisation](/rest/api/consumption/usagedetails/list).

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Créer une exportation quotidienne « mensuelle à ce jour » pour un abonnement

URL de requête : `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>Copier des blobs de stockage Azure volumineux

Vous pouvez utiliser Cost Management pour planifier l’exportation des détails de votre utilisation d’Azure vers vos comptes Azure Storage sous forme de blobs. La taille des blobs résultants peut dépasser les gigaoctets. L’équipe d’Azure Cost Management a collaboré avec l’équipe de Stockage Azure pour tester la copie de blocs de stockage Azure volumineux. Les résultats sont documentés dans les sections suivantes. Vous pouvez vous attendre à obtenir des résultats similaires lorsque vous copiez des blobs de stockage d’une région Azure à une autre.

Pour tester ses performances, l’équipe a transféré des blobs de comptes de stockage de la région USA Ouest vers la même région ainsi que vers d’autres régions. L’équipe a mesuré des vitesses allant de 2 Go par seconde dans la même région à 150 Mo par seconde vers des comptes de stockage de la région Asie Sud-Est.

### <a name="test-configuration"></a>Configuration de test

Pour mesurer les vitesses de transfert des blobs, l’équipe a créé une application console .NET simple référençant la version la plus récente (v2.0.1) de la bibliothèque de déplacement de données Azure (DLM) via NuGet. DLM est un Kit de développement logiciel (SDK) fourni par l’équipe de Stockage Azure qui facilite l’accès programmatique à ses services de transfert. L’équipe a ensuite créé des comptes de stockage v2 Standard dans plusieurs régions et a utilisé la région USA Ouest comme région source. Elle a rempli les comptes de stockage de conteneurs, chacun contenant 10 objets blob de blocs de 2 Go. Elle a copié les conteneurs sur d’autres comptes de stockage à l’aide de la méthode _TransferManager.CopyDirectoryAsync()_ de DLM avec l’option _CopyMethod.ServiceSideSyncCopy_. Des tests ont été effectués sur un ordinateur fonctionnant sous Windows 10 avec 12 cœurs et un réseau de 1 GbE.

Paramètres d’application utilisés :

- _TransferManager.Configurations.ParallelOperations_ = _Environment.ProcessorCount \* 32_. L’équipe a constaté que ce paramètre avait le plus d’effet sur le débit global. Une valeur de 32 fois le nombre de cœurs a fourni le meilleur débit pour le client de test.
- _ServicePointManager.DefaultConnectionLimit = int.MaxValue_. En lui attribuant une valeur maximale, on laisse le contrôle total du parallélisme des transferts au paramètre _ParallelOperations_ ci-dessus.
- _TransferManager.Configurations.BlockSize = 4,194,304_. Il a eu un certain effet sur les vitesses de transfert avec 4 Mo, ce qui s’avère idéal pour les tests.

Pour plus d’informations et pour obtenir un exemple de code, consultez les liens dans la section [Étapes suivantes](#next-steps).

### <a name="test-results"></a>Résultats des tests

| **Numéro du test** | **Vers la région** | **Objets blob** | **Durée (en secondes)** | **Mo/s** | **Commentaires** |
| --- | --- | --- | --- | --- | --- |
| 1 | WestUS | 2 Go x 10 | 10 | 2 000 |   |
| 2 | WestUS2 | 2 Go x 10 | 33 | 600 |   |
| 3 | USAEst | 2 Go x 10 | 67 | 300 |   |
| 4 | USAEst | 2 Go x 10 x 4 | 99 | 200 | 4 transferts parallèles utilisant 8 comptes de stockage : moyenne de 4 Ouest vers 4 Est par transfert |
| 6 | USAEst | 2 Go x 10 x 4 | 92 | 870 | 4 transferts parallèles d’un compte de stockage à un autre |
| 5 | USAEst | 2 Go x 10 x 8 | 148 | 135 | 8 transferts parallèles utilisant 8 comptes de stockage : moyenne de 4 Ouest vers 4x2 Est par transfert |
| 7 | SE Asie | 2 Go x 10 | 133 | 150 |   |
| 8 | SE Asie | 2 Go x 10 x 4 | 444 | 180 | 4 transferts parallèles d’un compte de stockage à un autre |

### <a name="sync-transfer-characteristics"></a>Caractéristiques du transfert synchrone

Voici quelques-unes des caractéristiques du transfert synchrone côté service utilisé avec DML qui sont pertinentes pour son utilisation :

- DML peut transférer un seul blob ou un répertoire. Pour le transfert de répertoire, vous pouvez utiliser un modèle de recherche pour correspondre au préfixe du blob.
- Les transferts d’objets blob de blocs se produisent en parallèle. Ils se terminent tous vers la fin du processus de transfert. Les objets blob de blocs individuels sont transférés en parallèle.
- Le transfert est exécuté de façon asynchrone sur le client. L’état du transfert est disponible régulièrement via le rappel d’une méthode qui peut être définie dans un objet _TransferContext_.
- Le transfert crée des points de contrôle pendant sa progression et expose un objet _TransferCheckpoint_. L’objet représente le dernier point de contrôle via l’objet _TransferContext_. Si le _TransferCheckpoint_ est enregistré avant l’annulation/abandon d’un transfert, le transfert peut reprendre à partir du point de contrôle pendant sept jours au maximum. Le transfert peut reprendre à partir de n’importe quel point de contrôle, pas seulement le dernier.
- Si le processus client de transfert est tué et redémarré sans implémenter la fonctionnalité de point de contrôle :
  - Avant la fin des transferts de blobs, le transfert redémarre.
  - Lorsque le transfert de certains blobs est terminé, le transfert redémarre uniquement pour les blobs incomplets.
- La suspension de l’exécution du client suspend les transferts.
- La fonctionnalité de transfert de blob résume le client des échecs temporaires. Par exemple, la limitation d’un compte de stockage n’entraîne généralement pas l’échec d’un transfert, mais le ralentit.
- Les transferts côté service présentent une faible utilisation des ressources client (UC, mémoire, bande passante réseau et connexions).

### <a name="async-transfer-characteristics"></a>Caractéristiques du transfert asynchrone

Vous pouvez appeler la méthode _TransferManager.CopyDirectoryAsync()_ avec l’option _CopyMethod.ServiceSideAsyncCopy_. Son fonctionnement est similaire à celui du mécanisme de transfert synchrone du point de vue du client, mais avec les différences de fonctionnement suivantes :

- Les vitesses de transfert sont beaucoup plus lentes que le transfert synchrone équivalent (en général 10 Mo/s ou moins).
- Le transfert se poursuit même si le processus client se termine.
- Bien que les points de contrôle soient pris en charge, la reprise d’un transfert à l’aide d’un _TransferCheckpoint_ ne se fera pas à l’heure du point de contrôle, mais à l’état actuel du transfert.

### <a name="test-summary"></a>Résumé du test

Stockage Blob Azure prend en charge des vitesses de transfert globales élevées grâce à sa fonctionnalité de transfert synchrone côté service. L’utilisation de cette fonctionnalité dans les applications .NET est très simple grâce à la bibliothèque de déplacement de données. Il est possible pour les exportations de Cost Management de copier de manière fiable des centaines de gigaoctets de données sur un compte de stockage de n’importe quelle région en moins d’une heure.

## <a name="next-steps"></a>Étapes suivantes

- Voir la source [Bibliothèque de déplacement des données de Stockage Microsoft Azure](https://github.com/Azure/azure-storage-net-data-movement).
- [Transférer des données avec la bibliothèque de déplacement de données](../../storage/common/storage-use-data-movement-library.md).
- Voir l’exemple de source [Exemple d’application AzureDmlBackup](https://github.com/markjbrown/AzureDmlBackup).
- Lire [Haut débit avec Stockage Blob Azure](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage).