---
title: Packs de requêtes dans Azure Monitor
description: Les packs de requêtes dans Azure Monitor offrent un moyen de partager des collections de requêtes de journal dans plusieurs espaces de travail Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: 59df6fa8624a19b611515551839076a511fe717f
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482115"
---
# <a name="query-packs-in-azure-monitor-logs-preview"></a>Packs de requêtes dans les journaux Azure Monitor (préversion)
Un pack de requêtes est un objet du Gestionnaire des ressources qui agit comme un conteneur pour les requêtes de journal dans Azure Monitor qui offrent un moyen d’enregistrer des requêtes de journal et de les partager entre plusieurs espaces de travail et d’autres contextes dans Log Analytics. 

## <a name="view-query-packs"></a>Afficher les packs de requêtes
Vous pouvez afficher et gérer les packs de requêtes dans le Portail Azure à partir du menu **Packs de requêtes Log Analytics**. Sélectionnez un pack de requêtes pour afficher et modifier ses autorisations. Pour plus d’informations sur la création d’un pack de requêtes à l’aide de l’API, voir ci-dessous.

[![Afficher les packs de requêtes](media/query-packs/view-query-pack.png)](media/query-packs/view-query-pack.png#lightbox)

## <a name="permissions"></a>Autorisations
Vous pouvez définir les autorisations sur un pack de requêtes quand vous l’affichez dans la Portail Azure. Les utilisateurs ont besoin des autorisations suivantes pour utiliser les packs de requêtes :

- **Lecteur** : l’utilisateur peut afficher et exécuter toutes les requêtes dans le pack de requêtes.
- **Contributeur** : l’utilisateur peut modifier des requêtes existantes et ajouter de nouvelles requêtes au pack de requêtes.

## <a name="default-query-pack"></a>Pack de requêtes par défaut
Un pack de requêtes, appelé **DefaultQueryPack**, est automatiquement créé dans chaque abonnement dans un groupe de ressources appelé **LogAnalyticsDefaultResources** lorsque la première requête est enregistrée. Vous pouvez créer des requêtes dans ce pack de requêtes ou créer des packs de requêtes supplémentaires en fonction de vos besoins.

## <a name="using-multiple-query-packs"></a>Utilisation de plusieurs packs de requêtes
Le pack de requêtes par défaut est suffisant pour la plupart des utilisateurs afin d’enregistrer et de réutiliser des requêtes. Toutefois, vous pouvez être amené à créer plusieurs packs de requêtes pour les utilisateurs de votre organisation, y compris le chargement de différents jeux de requêtes dans différentes sessions de Log Analytics et en fournissant des autorisations différentes pour des regroupements de requêtes différents. 

Lorsque vous créez un nouveau pack de requêtes à l’aide de l’API, vous pouvez ajouter des balises qui classent les requêtes en fonction des besoins de votre entreprise. Par exemple, vous pouvez marquer un pack de requêtes pour le lier à un service particulier dans votre organisation ou à la gravité des problèmes que les requêtes incluses sont censées traiter. Cela vous permet de créer différents jeux de requêtes destinés à différents groupes d’utilisateurs et différentes situations.

## <a name="query-pack-definition"></a>Définition du pack de requêtes
Chaque pack de requêtes est défini dans un fichier JSON qui contient la définition d’une ou plusieurs requêtes. Chaque requête est représentée par un bloc comme suit :

```json
{
    "properties":
       {
        "displayName": "Query name that will be displayed in the UI",
        "description": "Query description that will be displayed in the UI",
        "body": "<<query text, standard KQL code>>",
        "related": {
            "categories": [
                "workloads"
            ],
            "resourceTypes": [
                "microsoft.insights/components"
            ],
            "solutions": [
                "logmanagement"
            ]
        },
        "tags": {
            "Tag1": [
                "Value1",
                "Value2"
            ]
        },
   }
}
```


## <a name="query-properties"></a>Propriétés de la requête
Chaque requête dans le pack de requêtes a les propriétés suivantes.


| Propriété | Description |
|:---|:---|
| displayName | Nom complet indiqué dans Log Analytics pour chaque requête. | 
| description | Description de la requête affichée dans Log Analytics pour chaque requête. |
| body        | Requête écrite en KQL. |
| Lié au     | Catégories liées, types de ressources et solutions associés à la requête. Utilisé pour le regroupement et le filtrage dans Log Analytics par l’utilisateur pour faciliter la localisation de la requête. Chaque requête peut en avoir jusqu’à dix de chaque type. Récupérez les valeurs autorisées de https://api.loganalytics.io/v1/metadata?select=resourceTypes,solutions,catégories. |
| tags        | Balises supplémentaires utilisées par l’utilisateur pour le tri et le filtrage dans Log Analytics. Chaque balise sera ajoutée à Catégorie, à Type de ressource et à Solution lors du [regroupement et du filtrage des requêtes](queries.md#finding-and-filtering-queries). |




## <a name="create-a-query-pack"></a>Créer un pack de requêtes
La seule façon d’installer un pack de requêtes est d’utiliser l’API REST. 

### <a name="create-token"></a>Créer un jeton
Vous avez besoin d’un jeton pour l’authentification de la requête d’API. Il existe plusieurs méthodes pour obtenir un jeton, y compris l’utilisation de **armclient**.

Connectez-vous d’abord à Azure en utilisant la commande suivante :

```
armclient login
```

Créez ensuite le jeton avec la commande suivante. Le jeton est automatiquement copié dans le presse-papiers pour vous permettre de le coller dans un autre outil.

```
armclient token
```

### <a name="create-payload"></a>Créer une charge utile
La charge utile de la demande est le JSON définissant une ou plusieurs requêtes et l’emplacement où le pack de requêtes doit être stocké. Le nom du pack de requêtes est spécifié dans la demande d’API décrite dans la section suivante.

```json
{
    "location": "eastus",
    "properties":
    {
        "displayName": "Query name that will be displayed in the UI",
        "description": "Query description that will be displayed in the UI",
        "body": "<<query text, standard KQL code>>",
        "related": {
            "categories": [
                "workloads"
            ],
            "resourceTypes": [
                "microsoft.insights/components"
            ],
            "solutions": [
                "logmanagement"
            ]
        },
        "tags": {
            "Tag1": [
                "Value1",
                "Value2"
            ]
        }
    }
}
```

### <a name="create-request"></a>Créer une demande
Utilisez la requête suivante pour créer un nouveau pack de requêtes à l’aide de l’API REST. La requête doit utiliser l’autorisation du jeton du porteur. Le type de contenu doit être application/json.

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/querypacks/my-query-pack?api-version=2019-09-01-preview
```

Utilisez un outil qui peut envoyer une requête d’API REST telle que Fiddler ou Postman pour envoyer la demande à l’aide de la charge utile décrite dans la section précédente. L’ID de requête sera généré et retourné dans la charge utile. 

## <a name="update-a-query-pack"></a>Mettre à jour un pack de requêtes
Pour mettre à jour un pack de requêtes, envoyez la demande suivante avec une charge utile mise à jour. Cette commande requiert l’ID du pack de requêtes.

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/querypacks/my-query-pack/queries/query-id/?api-version=2019-09-01-preview
```

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Utilisation de requêtes dans Azure Monitor Log Analytics](queries.md) pour voir comment les utilisateurs interagissent avec les packs de requêtes dans Log Analytics.