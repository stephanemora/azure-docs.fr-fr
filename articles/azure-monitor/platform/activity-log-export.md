---
title: Exporter le journal d’activité Azure
description: Vous pouvez exporter un journal d’activité Azure vers le stockage pour l’archivage ou Azure Event Hubs pour l’exportation en dehors d’Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: acf2526e79519e610614dc5217efbfe5e327b90f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248143"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exporter le journal d’activité Azure vers le stockage ou Azure Event Hubs
Le [journal d’activité Azure](activity-logs-overview.md) fournit des informations sur les événements de niveau d’abonnement qui se sont produites dans votre abonnement Azure. Outre l’affichage du journal d’activité dans le portail Azure ou en le copiant à un espace de travail Analytique de journal où elles peuvent être analysées avec d’autres données collectées par Azure Monitor, vous pouvez créer un profil de journal pour archiver le journal d’activité à un compte de stockage Azure ou le flux à un  Hub d’événements.

## <a name="archive-activity-log"></a>Archiver le journal d’activité
Archivage du journal d’activité à un compte de stockage est utile si vous souhaitez conserver vos données de journal plues de 90 jours (avec un contrôle total sur la stratégie de rétention) pour la sauvegarde, analyse statique ou d’audit. Si vous souhaitez uniquement conserver vos événements pendant 90 jours ou moins, il est inutile de définir l’archivage sur un compte de stockage, dans la mesure où les événements du journal d’activité sont conservés dans la plateforme Azure pendant 90 jours.

## <a name="stream-activity-log-to-event-hub"></a>Journal d’activité Stream au Hub d’événements
[Azure Event Hubs](/azure/event-hubs/) est une diffusion en continu de la plateforme et les événements service d’ingestion peut recevoir des données et traiter des millions d’événements par seconde. Les données envoyées à un concentrateur d’événements peuvent être transformées et stockées à l’aide d’adaptateurs de traitement par lot/stockage ou d’un fournisseur d’analyse en temps réel. Deux façons d’utiliser la fonctionnalité de diffusion en continu pour le journal d’activité sont :
* **Diffuser en continu sur des systèmes de journalisation et de télémétrie tiers** : Au fil du temps, la diffusion en continu sur Azure Event Hubs deviendra le mécanisme de diffusion de votre journal d’activité vers les solutions tierces SIEM et Log Analytics.
* **Créer une plateforme de journalisation et de télémétrie personnalisée** : Si vous disposez déjà d’une plate-forme de télémétrie personnalisée, ou si vous envisagez d’en créer une, la nature hautement évolutive de publication et d’abonnement d’Event Hubs vous permet d’intégrer avec souplesse le journal d’activité. 

## <a name="prerequisites"></a>Conditions préalables

### <a name="storage-account"></a>Compte de stockage
Si vous effectuez un archivage votre journal d’activité, vous devez [créer un compte de stockage](../../storage/common/storage-quickstart-create-account.md) si vous n’en avez pas déjà. Vous ne devez pas utiliser un compte de stockage existant qui a des autres, non surveillance des données qu’il contient afin que vous pouvez mieux contrôler l’accès aux données d’analyse. Si vous archivez également les journaux de Diagnostic et métriques pour un compte de stockage que vous pouvez choisir d’utiliser ce même compte de stockage pour conserver toutes les données d’analyse dans un emplacement central.

Il n’est pas nécessaire que le compte de stockage se trouve dans le même abonnement que l’abonnement générant des journaux d’activité, à condition que l’utilisateur qui configure le paramètre ait un accès RBAC approprié aux deux abonnements.
> [!NOTE]
>  Vous ne pouvez pas archiver les données dans un stockage situé derrière un réseau virtuel sécurisé.

### <a name="event-hubs"></a>Event Hubs
Si vous envoyez votre journal d’activité à un concentrateur d’événements, vous devez [créer un concentrateur d’événements](../../event-hubs/event-hubs-create.md) si vous n’en avez pas déjà. Si vous précédemment diffusé en continu les événements de journal d’activité pour cet espace de noms Event Hubs, ce hub d’événements sera réutilisé.

La stratégie d’accès partagé définit les autorisations dont dispose le mécanisme de diffusion en continu. Diffusion en continu vers Event Hubs requiert des autorisations de gestion, d’envoi et d’écoute. Vous pouvez créer ou modifier des stratégies d’accès partagé pour l’espace de noms Event Hubs dans le portail Azure sous l’onglet Configurer pour votre espace de noms Event Hubs.

Pour mettre à jour le profil de journal d’activité pour inclure la diffusion en continu, vous devez avoir l’autorisation ListKey sur la règle d’autorisation Event Hubs. Il n’est pas nécessaire que l’espace de noms Event Hubs se trouve dans le même abonnement que l’abonnement qui génère des journaux d’activité, à condition que l’utilisateur configurant le paramètre dispose d’un accès RBAC aux deux abonnements, et que ces derniers se trouvent dans le même locataire AAD.

Stream le journal d’activité à un concentrateur d’événements par [création d’un profil de journal](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Créer un profil de journal
Vous définissez la façon dont votre journal d’activité Azure est exporté à l’aide un **profil de journal**. Chaque abonnement Azure n’admet qu’un seul profil de journal. Ces paramètres peuvent être configurés via la **exporter** option dans le panneau journal d’activité dans le portail. Ils peuvent également être configurés par programme [à l’aide de l’API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), d’applets de commande PowerShell ou de l’interface de ligne de commande.

Le profil de journal définit les éléments suivants.

**Où le journal d’activité doivent être envoyé.** Actuellement, les options disponibles sont le compte de stockage ou Event Hubs.

**Les catégories d’événements doivent être envoyées.** La signification de *catégorie* dans le journal d’activité et de profils de journal des événements est différent. Dans le profil de journal, *catégorie* représente le type d’opération (Write, Delete, Action). Dans un événement de journal d’activité, le *catégorie*« * propriété représente la source ou le type d’événement (par exemple, Administration, ServiceHealth et alerte).

**Les régions (emplacements) doivent être exportées.** Vous devez inclure tous les emplacements dans la mesure où de nombreux événements dans le journal d’activité sont des événements globaux.

**La durée pendant laquelle le journal d’activité doit être conservé dans un compte de stockage.** Une durée de rétention de zéro jour signifie que les journaux d’activité sont conservés indéfiniment. La valeur peut également être n’importe quel nombre de jours, compris entre 1 et 2147483647.

Si les stratégies de rétention sont définies, mais le stockage des journaux dans un compte de stockage est désactivée, les stratégies de rétention n’ont aucun effet. Les stratégies de rétention sont appliquées sur une base quotidienne. Donc, à la fin d’une journée (UTC), les journaux d’activité de la journée qui est désormais au-delà de la stratégie de rétention sont supprimés. Par exemple, si vous aviez une stratégie de rétention d’une journée, au début de la journée d’aujourd’hui les journaux d’activité d’avant-hier seront supprimés. Le processus de suppression commence à minuit UTC, mais notez que la suppression des journaux d’activité de votre compte de stockage peut prendre jusqu’à 24 heures.



> [!WARNING]
> Depuis le 1er novembre 2018, le format des données de journal dans le compte de stockage est devenu JSON Lines. [Consultez cet article pour en savoir plus sur les conséquences liées à ce changement et pour découvrir comment mettre à jour vos outils pour qu’ils gèrent ce nouveau format.](diagnostic-logs-append-blobs.md)
>
>

### <a name="create-log-profile-using-the-azure-portal"></a>Créer un profil de journal à l’aide du portail Azure

Créer ou modifier un profil de journal avec le **exporter vers Event Hub** option dans le portail Azure.

1. À partir de la **moniteur** menu dans le portail Azure, sélectionnez **exporter vers Event Hub**.

    ![Bouton Exporter dans le portail](media/activity-log-export/portal-export.png)

3. Dans le panneau qui s’affiche, spécifiez les éléments suivants :
   * Régions avec les événements à exporter. Vous devez sélectionner toutes les régions pour vous assurer que vous ne manquez pas les événements de touche dans la mesure où le journal d’activité est un journal (non régional) global et la plupart des événements ne bénéficient donc pas une région qui s’y rapportent. 
   * Si vous souhaitez écrire dans le compte de stockage :
       * Le compte de stockage auquel vous souhaitez enregistrer les événements.
       * le nombre de jours pendant lesquels que vous souhaitez conserver ces événements dans le stockage. Un paramètre de 0 jour conserve les journaux d’activité indéfiniment.
   * Si vous souhaitez écrire dans event hub :
       * Namespace de Bus de Service dans lequel vous souhaitez qu’un concentrateur d’événements doit être créée pour ces événements de diffusion en continu.

     ![Panneau Exporter le journal d’activité](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Cliquez sur **Enregistrer** pour enregistrer ces paramètres. Les paramètres sont immédiatement appliqués à votre abonnement.


### <a name="configure-log-profile-using-powershell"></a>Configurer le profil de journal à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Si un profil de journal existe déjà, vous devez d’abord supprimer le profil de journal existant et créer un nouveau.

1. Utilisez `Get-AzLogProfile` pour déterminer s’il existe un profil de journal.  Si un profil de journal n’existe pas, notez le *nom* propriété.

1. Utilisez `Remove-AzLogProfile` pour supprimer le profil de journal à l’aide de la valeur provenant de la propriété *name*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Utilisez `Add-AzLogProfile` pour créer un profil de journal :

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Propriété | Obligatoire | Description |
    | --- | --- | --- |
    | Name |Oui |Nom de votre profil de journal. |
    | StorageAccountId |Non  |ID de ressource du compte de stockage dans lequel le journal d’activité doit être enregistré. |
    | serviceBusRuleId |Non  |ID de règle Service Bus pour l’espace de noms Service Bus dans lequel vous souhaitez que des concentrateurs d’événements soient créés. Il s’agit d’une chaîne au format : `{service bus resource ID}/authorizationrules/{key name}`. |
    | Location |Oui |Liste séparée par des virgules des régions pour lesquelles vous souhaitez collecter les événements du journal d’activité. |
    | RetentionInDays |Oui |Nombre de jours pour lequel les événements doivent être conservés dans le compte de stockage, entre 1 et 2147483647. Une valeur de zéro signifie que les journaux d’activité seront stockés pour une durée indéfinie. |
    | Category |Non  |Liste séparée par des virgules des catégories d’événements qui doivent être collectées. Les valeurs possibles sont _écrire_, _supprimer_, et _Action_. |

### <a name="example-script"></a>Exemple de script
Voici un exemple de script PowerShell pour créer un profil de journal qui écrit le journal d’activité dans les deux un stockage compte hub d’événements et.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configurer le profil de journal à l’aide d’Azure CLI

Si un profil de journal existe déjà, vous devez tout d’abord le supprimer, puis créer un nouveau profil de journal.

1. Utilisez `az monitor log-profiles list` pour déterminer s’il existe un profil de journal.
2. Utilisez `az monitor log-profiles delete --name "<log profile name>` pour supprimer le profil de journal à l’aide de la valeur provenant de la propriété *name*.
3. Utilisez `az monitor log-profiles create` pour créer un profil de journal :

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propriété | Obligatoire | Description |
    | --- | --- | --- |
    | name |Oui |Nom de votre profil de journal. |
    | storage-account-id |Oui |ID de ressource du compte de stockage dans lequel les journaux d’activité doivent être enregistrés. |
    | locations |Oui |Liste, séparée par des espaces, des régions pour lesquelles vous souhaitez collecter les événements du journal d’activité. Vous pouvez voir une liste de toutes les régions pour votre abonnement à l’aide de `az account list-locations --query [].name`. |
    | days |Oui |Nombre de jours pour les événements doivent être conservés, compris entre 1 et 365. Une valeur de zéro signifie que les journaux d’activité seront stockés pour une durée indéfinie (pour toujours).  Si zéro est spécifié, le paramètre activé doit être défini sur true. |
    |enabled | Oui |True ou False.  Utilisée pour activer ou désactiver la stratégie de rétention.  Si la valeur est True, le paramètre days doit être une valeur supérieure à 0.
    | categories |Oui |Liste, séparée par des espaces, des catégories d’événements qui doivent être collectées. Les valeurs possibles sont Write, Delete et Action. |



## <a name="activity-log-schema"></a>Schéma des journaux d’activité
Si envoyées au stockage Azure ou concentrateur d’événements, les données de journal d’activité seront écrite au format JSON avec le format suivant.

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
Les éléments dans ce JSON sont décrits dans le tableau suivant.

| Nom de l'élément | Description |
| --- | --- |
| time |Horodatage lorsque l’événement a été généré par le service Azure traitant la demande correspondant à l’événement. |
| resourceId |ID de ressource de la ressource affectée. |
| operationName |Nom de l’opération. |
| category |Catégorie de l’action, par ex. Écriture, Lecture, Action. |
| resultType |Le type du résultat, par ex. Réussite, échec, démarrage |
| resultSignature |Dépend du type de ressource. |
| durationMS |Durée de l’opération en millisecondes |
| callerIpAddress |Adresse IP de l’utilisateur qui a effectué l’opération, la revendication UPN ou la revendication SPN basée sur la disponibilité. |
| correlationId |Généralement un GUID au format chaîne. Les événements qui partagent un correlationId appartiennent à la même action uber. |
| identité |Objet blob JSON décrivant l’autorisation et les revendications. |
| autorisation |Objet blob des propriétés RBAC de l’événement. Inclut généralement les propriétés « action », « role » et « scope ». |
| level |Niveau de l’événement. L’une des valeurs suivantes : _Critique_, _erreur_, _avertissement_, _d’information_, et _détaillée_ |
| location |Région dans laquelle se trouve l’emplacement (ou global). |
| properties |Jeu de paires `<Key, Value>` (p. ex. Dictionary) décrivant les détails de l’événement. |

> [!NOTE]
> Les propriétés et l’utilisation de ces propriétés peuvent varier en fonction de la ressource.



## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur le journal d’activité](../../azure-resource-manager/resource-group-audit.md)
* [Collecter le journal d’activité dans les journaux Azure Monitor](activity-log-collect.md)
