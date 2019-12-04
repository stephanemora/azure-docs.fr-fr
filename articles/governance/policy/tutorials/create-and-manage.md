---
title: 'Didacticiel : Générer des stratégies pour appliquer la conformité'
description: Dans ce tutoriel, vous utilisez des stratégies pour appliquer des normes, maîtriser les coûts, garantir la sécurité et imposer des principes de conception à l’échelle de l’entreprise.
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: 75a1d892a88f2b5bbdbec2a1b8d525245bb1e86f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482326"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Didacticiel : Créer et gérer des stratégies pour appliquer la conformité

Il est important de comprendre comment créer et gérer des stratégies dans Azure pour rester conforme aux normes de votre entreprise et aux contrats de niveau de service. Dans ce tutoriel, vous allez découvrir comment utiliser Azure Policy pour effectuer certaines des tâches les plus courantes liées à la création, à l’affectation et à la gestion des stratégies dans votre organisation, notamment :

> [!div class="checklist"]
> - Affecter une stratégie pour appliquer une condition aux ressources que vous créez dans le futur
> - Créer et affecter une définition d’initiative pour effectuer le suivi de la conformité de plusieurs ressources
> - Résoudre une ressource non conforme ou refusée
> - Implémenter une nouvelle stratégie dans l’ensemble de l’entreprise

Si vous souhaitez affecter une stratégie pour identifier l’état actuel de la conformité de vos ressources existantes, consultez les articles de démarrage rapide qui passent en revue la marche à suivre.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="assign-a-policy"></a>Attribution d’une stratégie

La première étape de l’application de la conformité avec une stratégie Azure consiste à affecter une définition de stratégie. Une définition de stratégie définit dans quelle condition une stratégie est appliquée et l’action à effectuer. Dans cet exemple, affectez une définition de stratégie intégrée appelée *Exiger SQL Server version 12.0* afin d’appliquer la condition selon laquelle toutes les bases de données SQL Server doivent être de version 12.0 pour être compatibles.

1. Accédez au portail Azure pour attribuer des stratégies. Recherchez et sélectionnez **Stratégie**.

   ![Rechercher Stratégie dans la barre de recherche](../media/create-and-manage/search-policy.png)

1. Sélectionnez **Affectations** du côté gauche de la page Azure Policy. Une affectation est une stratégie qui a été affectée pour être appliquée dans une étendue spécifique.

   ![Sélectionner des affectations à partir de la page de vue d’ensemble de la stratégie](../media/create-and-manage/select-assignments.png)

1. Sélectionnez **Assigner une stratégie** en haut de la page**Stratégie - Affectations**.

   ![Affecter une définition de stratégie à partir de la page Affectations](../media/create-and-manage/select-assign-policy.png)

1. Dans la page **Assigner une stratégie**, sélectionnez l’**étendue** en sélectionnant les points de suspension et en sélectionnant un groupe d’administration ou un abonnement. Sélectionnez éventuellement un groupe de ressources. Une étendue détermine les ressources ou le regroupement de ressources sur lequel la stratégie est appliquée. Cliquez ensuite sur **Sélectionner** au bas de la page **Étendue**.

   Cet exemple utilise l’abonnement **Contoso**. Votre abonnement sera différent.

1. Vous pouvez exclure des ressources en fonction de **l’étendue**. Les **exclusions** commencent à un niveau inférieur à celui de **l’étendue**. Les **exclusions** étant facultatives, laissez ce champ vide pour l’instant.

1. Sélectionnez les points de suspension de **Définition de stratégie** pour ouvrir la liste des définitions disponibles. Vous pouvez filtrer le **Type** de définition de stratégie sur *BuiltIn* pour les afficher tous et lire leurs descriptions.

1. Sélectionnez **Exiger SQL Server version 12.0**. Si vous ne la trouvez pas immédiatement, tapez **exiger sql server** dans la zone de recherche, puis appuyez sur Entrée ou cliquez en dehors de la zone de recherche. Cliquez sur **Sélectionner** au bas de la page **Définitions disponibles** une fois que vous avez trouvé et sélectionné la définition de stratégie.

   ![Utiliser le filtre de recherche pour localiser une stratégie](../media/create-and-manage/select-available-definition.png)

1. Le **Nom de l’attribution** est automatiquement rempli avec le nom de stratégie que vous avez sélectionné, mais vous pouvez le modifier. Pour cet exemple, laissez *Exiger SQL Server version 12.0*. Vous pouvez également ajouter une **Description** (facultatif). La description fournit des détails sur cette affectation de stratégie.
   **Affectée par** est automatiquement renseigné en fonction de l’utilisateur connecté. Ce champ étant facultatif, vous pouvez entrer des valeurs personnalisées.

1. Laissez la case **Créer une identité managée** non cochée. Vous _devez_ la cocher si la stratégie ou l’initiative affectée inclut une stratégie avec l’effet [deployIfNotExists](../concepts/effects.md#deployifnotexists). Comme ce n’est pas le cas pour la stratégie utilisée dans ce tutoriel, ne cochez pas la case. Pour plus d’informations, consultez [Identités managées](../../../active-directory/managed-identities-azure-resources/overview.md) et [Fonctionnement de la sécurité par correction](../how-to/remediate-resources.md#how-remediation-security-works).

1. Sélectionnez **Attribuer**.

## <a name="implement-a-new-custom-policy"></a>Implémenter une nouvelle stratégie personnalisée

Maintenant que vous avez affecté une définition de stratégie intégrée, vous pouvez continuer avec Azure Policy. Créez une stratégie personnalisée pour réduire les coûts en veillant à ce que les machines virtuelles créées dans votre environnement ne puissent pas être dans la série G. De cette manière, à chaque fois qu’un utilisateur de votre organisation tente de créer une machine virtuelle dans la série G, la requête est refusée.

1. Sélectionnez **Définitions** sous **Création** dans la partie gauche de la page Azure Policy.

   ![Page Définition sous Création de groupe](../media/create-and-manage/definition-under-authoring.png)

1. Sélectionnez **+ Définition de stratégie** en haut de la page. Ce bouton ouvre la page **Définition de stratégie**.

1. Entrez les informations suivantes :

   - Le groupe d’administration ou l’abonnement dans lequel la définition de stratégie est enregistrée. Sélectionnez-le à l’aide du bouton de sélection **Emplacement de définition**.

     > [!NOTE]
     > Si vous envisagez d’appliquer cette définition de stratégie à plusieurs abonnements, l’emplacement doit correspondre à un groupe d’administration qui contient les abonnements auxquels vous affectez la stratégie. Il en va de même pour une définition d’initiative.

   - Le nom de la définition de stratégie : *nécessite des références (SKU) de machines virtuelles inférieures à la série G*
   - La description de l’objectif de la définition de stratégie : *cette définition de stratégie impose que toutes les machines virtuelles créées dans cette étendue possèdent des références SKU inférieures à la série G afin de réduire le coût.*
   - Faites votre choix parmi les options existantes (par exemple _Calculer_), ou créez une catégorie pour cette définition de stratégie.
   - Copiez le code JSON suivant, puis mettez-le à jour selon vos besoins avec :
      - Les paramètres de la stratégie.
      - Les règles/conditions de la stratégie, dans ce cas : la taille de la référence (SKU) de la machine virtuelle est égale à la série G
      - L’effet de la stratégie, dans ce cas – **Refuser**.

   Voici à quoi le code JSON doit ressembler. Collez le code révisé dans le portail Azure.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   La propriété *field* dans la règle de stratégie doit avoir l’une des valeurs suivantes : Name, Type, Location, Tags ou un alias. Exemple d’alias : `"Microsoft.Compute/VirtualMachines/Size"`.

   Pour voir d’autres exemples relatifs à Azure Policy, consultez [Exemples Azure Policy](../samples/index.md).

1. Sélectionnez **Enregistrer**.

## <a name="create-a-policy-definition-with-rest-api"></a>Créer une définition de stratégie avec l’API REST

Vous pouvez créer une stratégie avec l’API REST pour les définitions Azure Policy. L’API REST vous permet de créer et de supprimer des définitions de stratégies, ainsi que d’obtenir des informations sur les définitions existantes. Pour créer une définition de stratégie, utilisez l’exemple suivant :

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Incluez un texte de demande semblable à l’exemple suivant :

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Créer une définition de stratégie avec PowerShell

Avant de passer à l’exemple PowerShell, assurez-vous d’avoir installé la dernière version du module Azure PowerShell Az.

Vous pouvez créer une définition de stratégie en utilisant l’applet de commande `New-AzPolicyDefinition`.

Pour créer une définition de stratégie à partir d’un fichier, transmettez le chemin d’accès au fichier. Pour un fichier externe, utilisez l’exemple suivant :

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Pour un fichier local, utilisez l’exemple suivant :

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Pour créer une définition de stratégie avec une règle en ligne, utilisez l’exemple suivant :

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

Le résultat est stocké dans un objet `$definition` utilisé lors de l’attribution de la stratégie. L’exemple suivant crée une définition de stratégie qui inclut des paramètres :

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Voir les définitions de stratégie avec PowerShell

Pour afficher toutes les définitions de stratégie dans votre abonnement, utilisez la commande suivante :

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Elle renvoie toutes les définitions de stratégie disponibles, y compris les stratégies intégrées. Chaque stratégie est renvoyée au format suivant :

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Créer une définition de stratégie avec Azure CLI

Vous pouvez créer une définition de stratégie à l’aide d’Azure CLI avec la commande de définition de stratégie. Pour créer une définition de stratégie avec une règle en ligne, utilisez l’exemple suivant :

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Voir les définitions de stratégie avec Azure CLI

Pour afficher toutes les définitions de stratégie dans votre abonnement, utilisez la commande suivante :

```azurecli-interactive
az policy definition list
```

Elle renvoie toutes les définitions de stratégie disponibles, y compris les stratégies intégrées. Chaque stratégie est renvoyée au format suivant :

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Créer et attribuer une définition d’initiative

Avec une définition d’initiative, vous pouvez regrouper plusieurs définitions de stratégie pour atteindre un objectif global. Une initiative vérifie si les ressources figurant dans l’étendue de l’attribution sont conformes aux stratégies incluses. Pour plus d’informations sur les définitions d’initiative, consultez [Vue d’ensemble d’Azure Policy](../overview.md).

### <a name="create-an-initiative-definition"></a>Créer une définition d’initiative

1. Sélectionnez **Définitions** sous **Création** dans la partie gauche de la page Azure Policy.

   ![Sélectionner une définition dans la page Définitions](../media/create-and-manage/definition-under-authoring.png)

1. Sélectionnez **+ Définition d’initiative** en haut de la page pour ouvrir la page **Définition d’initiative**.

   ![Passer en revue la page de la définition d’initiative](../media/create-and-manage/initiative-definition.png)

1. Utilisez le bouton de sélection **Emplacement de définition** pour sélectionner un groupe d'administration ou un abonnement afin de stocker la définition. Si la page précédente se limite à un seul groupe d’administration ou à un seul abonnement, **Emplacement de la définition** est automatiquement renseigné.

1. Entrez le **nom** et la **description** de l’initiative.

   Cet exemple vérifie que les ressources sont conformes aux définitions de stratégie relatives à la sécurisation. Nommez l’initiative **Garantir la sécurité** et définissez la description comme suit : **Cette initiative a été créée pour gérer toutes les définitions de stratégie associées à la sécurisation des ressources**.

1. Pour **Catégorie**, choisissez une des options existantes ou créez une catégorie.

1. Parcourez la liste des **définitions disponibles** (partie droite de la page **Définition d’initiative**) et sélectionnez les définitions de stratégie à ajouter à cette initiative. Pour l’initiative **Garantir la sécurité**, ajoutez les définitions de stratégie prédéfinies suivantes en sélectionnant **+** en regard des informations correspondantes ou en sélectionnant une ligne de définition de stratégie, puis l’option **+ Ajouter** dans la page de détails :

   - Nécessitent SQL Server version 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Une fois que vous avez sélectionné la définition de stratégie dans la liste, elle est ajoutée sous **Stratégies et paramètres**.

   ![Passer en revue les paramètres de la définition d’initiative](../media/create-and-manage/initiative-definition-2.png)

1. Si une définition de stratégie ajoutée à l’initiative comporte des paramètres, ils apparaissent sous le nom de la stratégie dans la zone **Stratégies et paramètres**. La _valeur_ peut être définie sur « Définir une valeur » (codée en dur pour toutes les affectations de cette initiative) ou « Utiliser le paramètre d'initiative » (définie au cours de chaque affectation d’initiative). Si « Définir une valeur » est sélectionné, la liste déroulante à droite de _Valeurs_ permet d’entrer ou de sélectionner les valeurs. Si l’option Utiliser le paramètre d’initiative est sélectionnée, une nouvelle section **Paramètres d’initiative** s’affiche, ce qui vous permet de définir le paramètre défini durant l’affectation d’initiative. Les valeurs autorisées sur ce paramètre d’initiative peuvent restreindre davantage ce qui peut être défini au cours de l’affectation d’initiative.

   ![Modifier les paramètres de la définition d’initiative à partir des valeurs autorisées](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > Dans le cas de certains paramètres `strongType`, la liste de valeurs ne peut pas être déterminée automatiquement. Dans ce cas, un bouton de sélection s’affiche à droite de la ligne de paramètre. En le sélectionnant, vous ouvrez la page Étendue du paramètre (&lt;nom du paramètre&gt;). Sur cette page, sélectionnez l’abonnement à utiliser pour fournir les options de valeur. L’étendue de ce paramètre est utilisée uniquement lors de la création de la définition d’initiative et n’a aucun impact sur l’évaluation de la stratégie ou l’étendue de l’initiative lors de l’affectation.

1. Sélectionnez **Enregistrer**.

### <a name="assign-an-initiative-definition"></a>Attribuer une définition d’initiative

1. Sélectionnez **Définitions** sous **Création** dans la partie gauche de la page Azure Policy.

1. Recherchez la définition d’initiative **Garantir la sécurité** que vous avez précédemment créée et sélectionnez-la. Sélectionnez **Affecter** en haut de la page pour ouvrir la page **Garantir la sécurité : affecter l’initiative**.

   ![Affecter une définition à partir de la page de définition d’initiative](../media/create-and-manage/assign-definition.png)

   Vous pouvez également cliquer avec le bouton droit sur la ligne sélectionnée ou sélectionner les points de suspension situé en fin de ligne pour faire apparaître un menu contextuel. Sélectionnez ensuite **Affecter**.

   ![Autres options pour une initiative](../media/create-and-manage/select-right-click.png)

1. Renseignez la page **Garantir la sécurité : affecter l’initiative** en entrant les exemples d’informations suivants. Vous pouvez utiliser vos propres informations.

   - Étendue : l’abonnement ou le groupe d’administration dans lequel vous avez enregistré l’initiative devient la valeur par défaut.
     Vous pouvez changer l’étendue pour affecter l’initiative à un abonnement ou un groupe de ressources, à l’emplacement d’enregistrement.
   - Exclusions : configurez des ressources dans l’étendue afin d’empêcher que l’affectation d’initiative leur soit appliquée.
   - Définition d’initiative et Nom de l’affectation : Garantir la sécurité (prérenseigné avec le nom de l’initiative assignée).
   - Description : cette affectation d’initiative est adaptée afin d’appliquer ce groupe de définitions de stratégie.
   - Affectée par : renseigné automatiquement en fonction de l’utilisateur connecté. Ce champ étant facultatif, vous pouvez entrer des valeurs personnalisées.

1. Laissez la case **Créer une identité managée** non cochée. Vous _devez_ la cocher si la stratégie ou l’initiative affectée inclut une stratégie avec l’effet [deployIfNotExists](../concepts/effects.md#deployifnotexists). Comme ce n’est pas le cas pour la stratégie utilisée dans ce tutoriel, ne cochez pas la case. Pour plus d’informations, consultez [Identités managées](../../../active-directory/managed-identities-azure-resources/overview.md) et [Fonctionnement de la sécurité par correction](../how-to/remediate-resources.md#how-remediation-security-works).

1. Sélectionnez **Attribuer**.

## <a name="check-initial-compliance"></a>Vérifier la conformité initiale

1. Sélectionnez **Conformité** dans la partie gauche de la page Azure Policy.

1. Localisez l’initiative **Garantir la sécurité**. Elle est probablement toujours à l’_État de conformité_ **Non démarrée**.
   Sélectionnez l’initiative pour obtenir tous les détails sur la progression de l’affectation.

   ![Page de conformité de l’initiative - évaluations non démarrées](../media/create-and-manage/compliance-status-not-started.png)

1. Une fois l’affectation de l’initiative effectuée, la page de conformité est mise à jour avec l’_État de conformité_ **Conforme**.

   ![Page de conformité de l’initiative - ressources conformes](../media/create-and-manage/compliance-status-compliant.png)

1. Pour ouvrir la page de détails sur la conformité de la stratégie, sélectionnez une stratégie dans la page de conformité de l’initiative. Cette page fournit des détails au niveau des ressources pour la conformité.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Exempter une ressource non conforme ou refusée en utilisant l’exclusion

Selon l’exemple ci-dessus, après avoir attribué la définition de stratégie pour demander SQL Server version 12.0, tout serveur SQL créé avec une autre version sera refusé. Dans cette section, vous allez résoudre pas à pas une situation dans laquelle la demande de création d’un serveur SQL a été refusée en créant une exclusion sur un seul groupe de ressources. L’exclusion empêche l’application de la stratégie (ou de l’initiative) à cette ressource.
Dans l’exemple suivant, toutes les versions de serveur SQL Server sont autorisées dans un seul groupe de ressources. Une exclusion peut s’appliquer à un abonnement, un groupe de ressources ou à des ressources particulières.

Un déploiement bloqué en raison d’une stratégie ou d’une initiative affectée est consultable à deux emplacements :

- Sur le groupe de ressources ciblé par le déploiement : Sélectionnez **Déploiements** dans la partie gauche de la page, puis sélectionnez le **nom du déploiement** qui a échoué. La ressource refusée est listée avec l’état _Interdit_. Pour déterminer la stratégie ou l’initiative et l’affectation qui a refusé la ressource, sélectionnez **Échec. Cliquez ici pour plus d’informations ->** dans la page Vue d’ensemble du déploiement.
  Une fenêtre s’ouvre dans la partie droite de la page pour présenter les informations d’erreur. Sous **Détails de l’erreur** figurent les GUID des objets de stratégie associés.

  ![Déploiement refusé par l’affectation de stratégie](../media/create-and-manage/rg-deployment-denied.png)

- Dans la page Azure Policy : Sélectionnez **Conformité** dans la partie gauche de la page, puis sélectionnez la stratégie **Exiger SQL Server version 12.0**. Dans la page qui s’ouvre, vous pouvez observer une augmentation du nombre de **refus**. Sous l’onglet **Événements**, vous pouvez également observer le nom de la personne qui a tenté d’effectuer le déploiement refusé par la stratégie.

  ![Vue d’ensemble de la conformité d’une stratégie affectée](../media/create-and-manage/compliance-overview.png)

Dans cet exemple, Trent Baker, l’un des spécialistes de la virtualisation chez Contoso, effectuait des tâches requises. Nous avons besoin d’octroyer à Trent une exception, mais nous ne voulons pas qu’un groupe de ressources comporte des serveurs dont la version est autre que 12.0. Nous avons créé un groupe de ressources, **SQLServers_Excluded** et allons à présent lui octroyer une exception à cette affectation de stratégie.

### <a name="update-assignment-with-exclusion"></a>Mettre à jour une affectation avec une exclusion

1. Sélectionnez **Affectations** sous **Création** dans la partie gauche de la page Azure Policy.

1. Parcourez toutes les affectations de stratégie, puis ouvrez l’affectation *Nécessiter SQL Server version 12.0*.

1. Définissez l’**exclusion** en sélectionnant les points de suspension et en sélectionnant le groupe de ressources à exclure, *SQLServers_Excluded* dans cet exemple.

   ![Ajouter un groupe de ressources exclues à l’affectation de stratégie](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > En fonction de la stratégie et de son effet, l’exclusion peut également être octroyée à des ressources spécifiques au sein du groupe de ressources dans l’étendue de l’affectation. Comme un effet **Refuser** a été utilisé dans ce tutoriel, il ne serait pas judicieux de définir l’exclusion sur une ressource spécifique qui existe déjà.

1. Cliquez sur **Sélectionner**, puis sur **Enregistrer**.

Dans cette section, vous avez résolu la demande refusée en créant une exclusion sur un seul groupe de ressources.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez fini d’utiliser les ressources de ce tutoriel, effectuez les étapes suivantes pour supprimer les affectations ou définitions créées ci-dessus :

1. Sélectionnez **Définitions** (ou **Affectations** si vous essayez de supprimer une affectation) sous **Création** dans la partie gauche de la page Azure Policy.

1. Recherchez la nouvelle définition d’initiative ou de stratégie (ou affectation) à supprimer.

1. Cliquez avec le bouton droit sur la liste ou cliquez sur le bouton de sélection en fin de définition (ou d’affectation), puis sélectionnez **Supprimer la définition** (ou **Supprimer l’affectation**).

## <a name="review"></a>Révision

Dans ce didacticiel, vous avez effectué avec succès les tâches suivantes :

> [!div class="checklist"]
> - Attribué une stratégie pour appliquer une condition aux ressources que vous créez dans le futur
> - Créé et attribué une définition d’initiative pour effectuer le suivi de la conformité de plusieurs ressources
> - Résolu une ressource non conforme ou refusée
> - Implémenté une nouvelle stratégie dans l’ensemble de l’entreprise

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les structures des définitions de stratégie, consultez cet article :

> [!div class="nextstepaction"]
> [Structure de définition Azure Policy](../concepts/definition-structure.md)