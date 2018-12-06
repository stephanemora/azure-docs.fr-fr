---
title: Archiver le journal d’activité Azure
description: Archivez votre journal d’activité Azure pour une conservation à long terme dans un compte de stockage.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 33681c7c9e1a625757e3f9403820ed3f469bec64
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705784"
---
# <a name="archive-the-azure-activity-log"></a>Archiver le journal d’activité Azure
Dans cet article, nous vous expliquons comment vous pouvez utiliser le portail Azure, les applets de commande PowerShell ou l’interface de ligne de commande multiplateforme pour archiver votre [**journal d’activité Azure dans un compte de stockage**](monitoring-overview-activity-logs.md). Cette option est utile si vous souhaitez conserver votre journal d’activité pendant une période supérieure à 90 jours (en disposant d’un contrôle total sur la stratégie de rétention) à des fins d’audit, d’analyse statique ou de sauvegarde. Si vous devez conserver vos événements pendant 90 jours ou moins, il est inutile de configurer l’archivage sur un compte de stockage, puisque les événements du journal d’activité sont conservés dans la plateforme Azure pendant 90 jours sans que l’archivage ne soit activé.

> [!WARNING]
> À compter du 1er novembre 2018, le format des données de journal dans le compte de stockage deviendra JSON Lines. [Consultez cet article pour en savoir plus sur les conséquences liées à ce changement et pour découvrir comment mettre à jour vos outils pour qu’ils gèrent ce nouveau format.](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

## <a name="prerequisites"></a>Prérequis
Avant de commencer, vous devez [créer un compte de stockage](../storage/common/storage-quickstart-create-account.md) sur lequel vous pouvez archiver votre journal d’activité. Nous vous recommandons vivement de ne pas utiliser un compte de stockage existant sur lequel sont stockées d’autres données de non-analyse, afin de pouvoir mieux contrôler l’accès aux données d’analyse. En revanche, si vous archivez également des journaux de diagnostic et des métriques sur un compte de stockage, il peut être judicieux d’utiliser ce compte pour votre journal d’activité afin de regrouper toutes vos données d’analyse au même emplacement. Il n’est pas nécessaire que le compte de stockage se trouve dans le même abonnement que l’abonnement générant des journaux, à condition que l’utilisateur qui configure le paramètre ait un accès RBAC approprié aux deux abonnements.

> [!NOTE]
>  Vous ne pouvez pas actuellement archiver les données dans un compte de stockage créé derrière un réseau virtuel sécurisé.

## <a name="log-profile"></a>Profil de journal
Pour archiver le journal d’activité à l’aide de l’une des méthodes ci-dessous, définissez le **profil journal** pour un abonnement. Le profil de journal définit le type d’événements qui sont stockés ou diffusés et les types de sortie : compte de stockage et/ou hub d’événements. Il définit également la stratégie de rétention (nombre de jours de conservation) pour les événements stockés dans un compte de stockage. Si la stratégie de rétention est définie sur zéro, les événements sont stockés indéfiniment. Elle peut également être définie sur n’importe quelle valeur comprise entre 1 et 2147483647. Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’avant-hier seront supprimés. Le processus de suppression commence à minuit UTC, mais notez que la suppression des journaux de votre compte de stockage peut prendre jusqu’à 24 heures. [Vous trouverez plus d’informations sur les profils de journal ici](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Archiver le journal d’activité à l’aide du portail
1. Dans le portail, cliquez sur le lien **Journal d’activité** dans le volet de navigation de gauche. Si vous ne voyez pas de lien pour le journal d’activité, cliquez d’abord sur le lien **Tous les services**.
   
    ![Accéder au panneau Journal d’activité](media/monitoring-archive-activity-log/activity-logs-portal-navigate-v2.png)
2. En haut du panneau, cliquez sur **Exporter vers Event Hub**.
   
    ![Cliquer sur le bouton Exporter](media/monitoring-archive-activity-log/activity-logs-portal-export-v2.png)
3. Dans le panneau qui s’affiche, cochez la case pour **exporter vers un compte de stockage** et sélectionnez un compte de stockage.
   
    ![Créer un compte de stockage](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. À l’aide du curseur ou dans la zone de texte, définissez le nombre de jours (de 0 à 365) pendant lesquels les événements du journal d’activité doivent être conservés dans votre compte de stockage. Si vous préférez que vos données soient conservées indéfiniment dans le compte de stockage, indiquez zéro. Si vous avez besoin d’entrer un nombre de jours supérieur à 365, utilisez les méthodes PowerShell ou CLI décrites ci-dessous.
5. Cliquez sur **Enregistrer**.

## <a name="archive-the-activity-log-via-powershell"></a>Archiver le journal d’activité avec PowerShell

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your storage account belongs to>"
   $storageAccountName = "<your storage account name>"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -StorageAccountId $storageAccountId
   ```

| Propriété | Obligatoire | Description |
| --- | --- | --- |
| StorageAccountId |Oui |ID de ressource du compte de stockage dans lequel les journaux d’activité doivent être enregistrés. |
| Emplacements |Oui |Liste séparée par des virgules des régions pour lesquelles vous souhaitez collecter les événements du journal d’activité. Vous pouvez voir une liste de toutes les régions pour votre abonnement à l’aide de `(Get-AzureRmLocation).Location`. |
| RetentionInDays |Non  |Nombre de jours pendant lesquels les événements doivent être conservés, compris entre 1 et 2147483647. Une valeur de zéro signifie que les journaux seront stockés pour une durée indéfinie (pour toujours). |
| Catégories |Non  |Liste séparée par des virgules des catégories d’événements qui doivent être collectées. Les valeurs possibles sont Write, Delete et Action.  Si elles ne sont pas fournies, toutes les valeurs possibles sont supposées. |

## <a name="archive-the-activity-log-via-cli"></a>Archiver le journal d’activité avec l’interface de ligne de commande

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --storage-account-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>"
   ```

| Propriété | Obligatoire | Description |
| --- | --- | --- |
| Nom |Oui |Nom de votre profil de journal. |
| storage-account-id |Oui |ID de ressource du compte de stockage dans lequel les journaux d’activité doivent être enregistrés. |
| Emplacements |Oui |Liste, séparée par des espaces, des régions pour lesquelles vous souhaitez collecter les événements du journal d’activité. Vous pouvez voir une liste de toutes les régions pour votre abonnement à l’aide de `az account list-locations --query [].name`. |
| days |Oui |Nombre de jours pendant lesquels les événements doivent être conservés, compris entre 1 et 2147483647. Une valeur de zéro signifie que les journaux seront stockés pour une durée indéfinie (pour toujours).  Si zéro est spécifié, le paramètre activé doit être défini sur true. |
|Activé | Oui |True ou False.  Utilisée pour activer ou désactiver la stratégie de rétention.  Si la valeur est True, le paramètre days doit être une valeur supérieure à 0.
| Catégories |Oui |Liste, séparée par des espaces, des catégories d’événements qui doivent être collectées. Les valeurs possibles sont Write, Delete et Action. |

## <a name="storage-schema-of-the-activity-log"></a>Schéma de stockage du journal d’activité
Une fois que vous avez configuré l’archivage, un conteneur de stockage est créé dans le compte de stockage dès qu’un événement de journal d’activité se produit. Les objets blob du conteneur suivent la même convention de nommage dans l’ensemble des journaux d’activité et des journaux de diagnostic, comme illustré ici :

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Voici un exemple de nom d’objet blob :

```
insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Chaque objet blob PT1H.json contient un objet blob d’événements JSON qui se sont produits pendant l’heure spécifiée dans l’URL de l’objet blob (par exemple, h = 12). Pendant l’heure en cours, les événements sont ajoutés au fichier PT1H.json à mesure qu’ils se produisent. La valeur de minute (m = 00) est toujours 00, étant donné que les événements du journal d’activité sont répartis en différents objets blob par heure.

Dans le fichier PT1H.json, chaque événement est stocké dans le tableau « enregistrements », au format suivant :

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Nom de l'élément | Description |
| --- | --- |
| time |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| ResourceId |ID de ressource de la ressource affectée. |
| operationName |Nom de l’opération. |
| category |Catégorie de l’action, par ex. Écriture, Lecture, Action. |
| resultType |Le type du résultat, par ex. Réussite, échec, démarrage |
| resultSignature |Dépend du type de ressource. |
| durationMS |Durée de l’opération en millisecondes |
| callerIpAddress |Adresse IP de l’utilisateur qui a effectué l’opération, la revendication UPN ou la revendication SPN basée sur la disponibilité. |
| correlationId |Généralement un GUID au format chaîne. Les événements qui partagent un correlationId appartiennent à la même action uber. |
| identité |Objet blob JSON décrivant l’autorisation et les revendications. |
| autorisation |Objet blob des propriétés RBAC de l’événement. Inclut généralement les propriétés « action », « role » et « scope ». |
| level |Niveau de l’événement. Une des valeurs suivantes : Critical, Error, Warning, Informational et Verbose |
| location |Région dans laquelle se trouve l’emplacement (ou global). |
| properties |Jeu de paires `<Key, Value>` (p. ex. Dictionary) décrivant les détails de l’événement. |

> [!NOTE]
> Les propriétés et l’utilisation de ces propriétés peuvent varier en fonction de la ressource.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* [Télécharger des objets blob pour analyse](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Transférer le journal d’activité vers Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
* [En savoir plus sur le journal d’activité](monitoring-overview-activity-logs.md)

