---
title: Utiliser Azure Policy pour créer et gérer des stratégies afin d’appliquer la conformité de l’organisation
description: Utilisez une stratégie Azure pour appliquer des normes, se conformer aux réglementations de conformité et d’audit, maîtriser les coûts, garantir la sécurité et maintenir les performances, et imposer les principes de conception à l’échelle de l’entreprise.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/22/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 68ee6b64baf4284bbd0977e82fc473a58a59874c
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42813422"
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Créer et gérer des stratégies pour appliquer la conformité

Il est important de comprendre comment créer et gérer des stratégies dans Azure pour rester conforme aux normes de votre entreprise et aux contrats de niveau de service. Dans ce tutoriel, vous allez découvrir comment utiliser Azure Policy pour effectuer certaines des tâches les plus courantes liées à la création, à l’affectation et à la gestion des stratégies dans votre organisation, notamment :

> [!div class="checklist"]
> - Affecter une stratégie pour appliquer une condition aux ressources que vous créez dans le futur
> - Créer et affecter une définition d’initiative pour effectuer le suivi de la conformité de plusieurs ressources
> - Résoudre une ressource non conforme ou refusée
> - Implémenter une nouvelle stratégie dans l’ensemble de l’entreprise

Si vous souhaitez affecter une stratégie pour identifier l’état actuel de la conformité de vos ressources existantes, consultez les articles de démarrage rapide qui passent en revue la marche à suivre. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="assign-a-policy"></a>Attribution d’une stratégie

La première étape de l’application de la conformité avec une stratégie Azure consiste à affecter une définition de stratégie. Une définition de stratégie définit dans quelle condition une stratégie est appliquée et l’action à effectuer. Dans cet exemple, affectez une définition de stratégie intégrée appelée *Exiger SQL Server version 12.0* afin d’appliquer la condition selon laquelle toutes les bases de données SQL Server doivent être de version 12.0 pour être compatibles.

1. Lancez le service Azure Policy dans le portail Azure en cliquant sur **Tous les services**, puis en recherchant et en cliquant sur **Stratégie**.

   ![Rechercher une stratégie](media/create-manage-policy/search-policy.png)

1. Sélectionnez **Affectations** du côté gauche de la page Azure Policy. Une affectation est une stratégie qui a été affectée pour être appliquée dans une étendue spécifique.
1. Sélectionnez **Assigner une stratégie** en haut de la page**Stratégie - Affectations**.

   ![Affecter une définition de stratégie](media/create-manage-policy/select-assign-policy.png)

1. Dans la page **Assigner une stratégie**, sélectionnez **l’étendue** en cliquant sur les points de suspension et en sélectionnant un abonnement (obligatoire) et un groupe de ressources (facultatif). Une étendue détermine les ressources ou le regroupement de ressources sur lequel la stratégie est appliquée.  Cliquez ensuite sur **Sélectionner** dans le bas de la page **Étendue**.

   Cet exemple utilise **l’abonnement Contoso**. Votre abonnement sera différent.

1. Si vous voulez exclure un ou plusieurs groupes de ressources (si vous avez défini l’étendue seulement pour l’abonnement), ou des ressources spécifiques au sein d’un groupe de ressources (dans l’une ou l’autre définition d’étendue), vous pouvez configurer des **Exclusions** pour l’affectation de la stratégie. Laissez cette zone vide pour l’instant.

1. Sélectionnez le bouton de sélection **Définition de stratégie** pour ouvrir la liste des définitions disponibles. Vous pouvez filtrer le **Type** de définition de stratégie sur *BuiltIn* pour les afficher tous et lire leurs descriptions.

1. Sélectionnez **Exiger SQL Server version 12.0**. Si vous ne la trouvez pas immédiatement, tapez **exiger sql server** dans la zone de recherche, puis appuyez sur Entrée ou cliquez en dehors de la zone de recherche. Cliquez sur **Sélectionner** au bas de la page **Définitions disponibles** une fois que vous avez trouvé et sélectionné la définition de stratégie.

   ![Rechercher une stratégie](media/create-manage-policy/select-available-definition.png)

1. Le **Nom de l’attribution** est automatiquement rempli avec le nom de stratégie que vous avez sélectionné, mais vous pouvez le modifier. Pour cet exemple, laissez *Exiger SQL Server version 12.0*. Vous pouvez également ajouter une **Description** (facultatif). La description fournit des détails sur cette affectation de stratégie.

1. Cliquez sur **Affecter**.

## <a name="implement-a-new-custom-policy"></a>Implémenter une nouvelle stratégie personnalisée

Maintenant que vous avez affecté une définition de stratégie intégrée, vous pouvez continuer avec Azure Policy. Créez une stratégie personnalisée pour réduire les coûts en veillant à ce que les machines virtuelles créées dans votre environnement ne puissent pas être dans la série G. De cette manière, à chaque fois qu’un utilisateur de votre organisation tente de créer une machine virtuelle dans la série G, la requête est refusée.

1. Sélectionnez **Définitions** sous **CRÉATION** dans la partie gauche de la page Azure Policy.

   ![Définition sous Création](media/create-manage-policy/definition-under-authoring.png)

1. Sélectionnez **+ Définition de stratégie** en haut de la page. La page **Définition de stratégie** s’ouvre.
1. Entrez les informations suivantes :

   - Le groupe d’administration ou l’abonnement dans lequel la définition de stratégie est enregistrée. Sélectionnez-le à l’aide du bouton de sélection **Emplacement de définition**.

     > [!NOTE]
     > Si vous envisagez d’appliquer cette définition de stratégie à plusieurs abonnements, l’emplacement doit correspondre à un groupe d’administration qui contient les abonnements auxquels vous allez affecter la stratégie. Il en va de même pour une définition d’initiative.

   - Le nom de la définition de stratégie : *nécessite des références (SKU) de machines virtuelles inférieures à la série G*
   - La description de l’objectif de la définition de stratégie : *cette définition de stratégie impose que toutes les machines virtuelles créées dans cette étendue possèdent des références SKU inférieures à la série G afin de réduire le coût.*
   - Faites votre choix parmi les options existantes ou créez une catégorie pour cette définition de stratégie.
   - Copiez le code json suivant et mettez-le à jour selon vos besoins avec :
      - Les paramètres de la stratégie.
      - Les règles/conditions de la stratégie, dans ce cas : la taille de la référence (SKU) de la machine virtuelle est égale à la série G
      - L’effet de la stratégie, dans ce cas – **Refuser**.

    Le fichier json devrait ressembler à ce qui suit. Collez le code révisé dans le portail Azure.

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

    La valeur de la propriété de *champ* de la règle de stratégie doit être l’une des suivantes : Nom, Type, Emplacement, Balises ou un alias. Exemple d’alias : `"Microsoft.Compute/VirtualMachines/Size"`.

    Pour consulter d’autres exemples de stratégie Azure, consultez [Modèles pour Azure Policy](json-samples.md).

1. Sélectionnez **Enregistrer**.

## <a name="create-a-policy-definition-with-rest-api"></a>Créer une définition de stratégie avec l’API REST

Vous pouvez créer une stratégie avec l’API REST pour les définitions des stratégies. L’API REST vous permet de créer et de supprimer des définitions de stratégies, ainsi que d’obtenir des informations sur les définitions existantes.
Pour créer une définition de stratégie, utilisez l’exemple suivant :

```http-interactive
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
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

Avant de passer à l’exemple PowerShell, assurez-vous d’avoir installé la dernière version d’Azure PowerShell. Les paramètres de stratégie ont été ajoutés dans la version 3.6.0. Si vous utilisez une version antérieure, les exemples renvoient une erreur indiquant que le paramètre est introuvable.

Vous pouvez créer une définition de stratégie en utilisant l’applet de commande `New-AzureRmPolicyDefinition`.

Pour créer une définition de stratégie à partir d’un fichier, transmettez le chemin d’accès au fichier. Pour un fichier externe, utilisez l’exemple suivant :

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Pour un fichier local, utilisez l’exemple suivant :

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Pour créer une définition de stratégie avec une règle en ligne, utilisez l’exemple suivant :

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
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

Le résultat est stocké dans un objet `$definition` utilisé lors de l’attribution de la stratégie.
L’exemple suivant crée une définition de stratégie qui inclut des paramètres :

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

$definition = New-AzureRmPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Voir les définitions de stratégie avec PowerShell

Pour afficher toutes les définitions de stratégie dans votre abonnement, utilisez la commande suivante :

```azurepowershell-interactive
Get-AzureRmPolicyDefinition
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

Vous pouvez créer une définition de stratégie à l’aide d’Azure CLI avec la commande de définition de stratégie.
Pour créer une définition de stratégie avec une règle en ligne, utilisez l’exemple suivant :

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

Avec une définition d’initiative, vous pouvez regrouper plusieurs définitions de stratégie pour atteindre un objectif global. Vous créez une définition initiative pour vous assurer que les ressources dans l’étendue de la définition restent conformes aux définitions de stratégie qui composent la définition d’initiative.  Pour plus d’informations sur les définitions d’initiative, consultez [Vue d’ensemble d’Azure Policy](azure-policy-introduction.md).

### <a name="create-an-initiative-definition"></a>Créer une définition d’initiative

1. Sélectionnez **Définitions** sous **CRÉATION** dans la partie gauche de la page Azure Policy.

   ![Sélectionner les définitions](media/create-manage-policy/select-definitions.png)

1. Sélectionnez **+ Définition d’initiative** en haut de la page pour ouvrir la page **Définition d’initiative**.

   ![Définition d’initiative](media/create-manage-policy/initiative-definition.png)

1. Utilisez le bouton de sélection **Emplacement de définition** pour sélectionner un groupe d'administration ou un abonnement afin de stocker la définition.

1. Entrez le **nom** et la **description** de l’initiative.

   Dans cet exemple, vérifiez que les ressources sont conformes aux définitions de stratégie relatives à la sécurisation. Le nom de l’initiative serait donc **Garantir la sécurité**, et la description **Cette initiative a été créée pour gérer toutes les définitions de stratégie associées à la sécurisation des ressources**.

1. Pour **Catégorie**, choisissez une des options existantes ou créez une catégorie.

1. Parcourez la liste des **définitions disponibles** (partie droite de la page **Définition d’initiative**) et sélectionnez les définitions de stratégie à ajouter à cette initiative. Pour l’initiative **Garantir la sécurité**, ajoutez les définitions de stratégie prédéfinies suivantes en cliquant sur **+** en regard des informations correspondantes ou en cliquant sur une ligne de définition de stratégie, puis sur l’option **+ Ajouter** dans la page de détails :
   - Nécessitent SQL Server version 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Après avoir sélectionné la définition de stratégie dans la liste, celle-ci est ajoutée sous **STRATÉGIES ET PARAMÈTRES**.

   ![Définitions d’initiative](media/create-manage-policy/initiative-definition-2.png)

1. Si une définition de stratégie qui est ajoutée à l’initiative possède des paramètres, ils sont affichés sous le nom de la stratégie dans la zone **STRATÉGIES ET PARAMÈTRES**. La _valeur_ peut être définie sur « Définir une valeur » (codée en dur pour toutes les affectations de cette initiative) ou « Utiliser le paramètre d'initiative » (définie au cours de chaque affectation d’initiative). Si « Définir une valeur » est sélectionné, la liste déroulante à droite de _Valeurs_ permet d’entrer ou sélectionner le(s) valeur(s) souhaitée(s). Si « Utiliser le paramètre d'initiative » est sélectionné, une nouvelle section **Paramètres d’initiative** s’affiche, ce qui vous permet de définir le paramètre qui sera défini au cours de l’affectation d’initiative. Les valeurs autorisées sur ce paramètre d’initiative peuvent restreindre davantage ce qui peut être défini au cours de l’affectation d’initiative.

   ![Paramètres de la définition d’initiative](media/create-manage-policy/initiative-definition-3.png)

   > [!NOTE]
   > Dans le cas de certains paramètres `strongType`, la liste de valeurs ne peut pas être déterminée automatiquement. Dans ce cas, un bouton de sélection s’affiche à droite de la ligne de paramètre. En cliquant dessus, vous ouvrez la page « Étendue du paramètre (&lt;nom du paramètre&gt;) ». Sur cette page, sélectionnez l’abonnement à utiliser pour fournir les options de valeur. L’étendue de ce paramètre est utilisée uniquement lors de la création de la définition d’initiative et n’a aucun impact sur l’évaluation de la stratégie ou l’étendue de l’initiative lors de l’affectation.

1. Cliquez sur **Enregistrer**.

### <a name="assign-an-initiative-definition"></a>Attribuer une définition d’initiative

1. Sélectionnez **Définitions** sous **CRÉATION** dans la partie gauche de la page Azure Policy.
1. Recherchez la définition d’initiative **Garantir la sécurité** que vous avez précédemment créée et sélectionnez-la.
1. Sélectionnez **Affecter** en haut de la page pour ouvrir la page **Garantir la sécurité : affecter l’initiative**.

   ![Attribuer une définition](media/create-manage-policy/assign-definition.png)

   Vous pouvez également cliquer avec le bouton droit sur la ligne sélectionnée ou cliquer sur le bouton de sélection situé en fin de ligne pour faire apparaître un menu contextuel.  Sélectionnez ensuite **Affecter**.

   ![Clic droit sur une ligne](media/create-manage-policy/select-right-click.png)

1. Renseignez la page **Garantir la sécurité : affecter l’initiative** en entrant les exemples d’informations suivants. Vous pouvez utiliser vos propres informations.

   - Étendue : l’abonnement dans lequel vous avez enregistré l’initiative est la valeur par défaut.  Vous pouvez modifier l’étendue pour affecter l’initiative à un groupe de ressources à l’emplacement d’enregistrement de l’abonnement.
   - Exclusions : configurez des ressources dans l’étendue de sorte à empêcher que l’affectation d’initiative leur soit appliquée.
   - Nom de la définition d’initiative et de l’affectation : Garantir la sécurité (préremplie en tant que nom de l’initiative affectée).
   - Description : cette affectation d’initiative est adaptée de sorte à appliquer ce groupe de définitions de stratégie.

1. Cliquez sur **Affecter**.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Exempter une ressource non conforme ou refusée en utilisant l’exclusion

Selon l’exemple ci-dessus, après avoir attribué la définition de stratégie pour demander SQL Server version 12.0, tout serveur SQL créé avec une autre version sera refusé. Dans cette section, vous allez résoudre pas à pas une situation dans laquelle la création d’un serveur SQL a été refusée en créant une exclusion sur un seul groupe de ressources. L’exclusion empêche l’application de la stratégie (ou de l’initiative) à cette ressource. Dans l’exemple suivant, toutes les versions de serveur SQL sont autorisées dans un groupe de ressources unique. Une exclusion peut s’appliquer à un groupe de ressources ou à des ressources particulières.

Un déploiement bloqué en raison d’une stratégie ou une initiative affectée est consultable à deux emplacements :

- Sur le groupe de ressources ciblé par le déploiement : sélectionnez **Déploiements** dans la partie gauche de la page, puis cliquez sur le **nom du déploiement** qui a échoué. La ressource refusée présente un état _Interdit_. Pour déterminer la stratégie ou initiative et l’affectation qui a refusé la ressource, cliquez sur **Échec. Cliquez ici pour plus d’informations ->** dans la page Vue d’ensemble du déploiement. Une fenêtre s’ouvre dans la partie droite de la page pour présenter les informations d’erreur. Sous **Détails de l’erreur** figurent les GUID des objets de stratégie associés.

   ![Déploiement refusé par l’affectation de stratégie](media/create-manage-policy/rg-deployment-denied.png)

- Dans la page Azure Policy : sélectionnez **Conformité** dans la partie gauche de la page, puis cliquez sur la stratégie **Exiger SQL Server version 12.0**. Dans la page qui s’ouvre, vous pouvez observer une augmentation du nombre de **refus**. Sous l’onglet **Événements**, vous pouvez également observer le nom de la personne qui a tenté d’effectuer le déploiement refusé par la stratégie.

   ![Vue d’ensemble de la conformité d’une stratégie affectée](media/create-manage-policy/compliance-overview.png)

Dans cet exemple, Trent Baker, un des experts en virtualisation de Contoso effectue ce qu’il doit faire. Nous avons besoin de lui octroyer une exception, mais nous ne voulons pas qu’un groupe de ressources comporte des serveurs dont la version est autre que 12.0. Nous avons créé un groupe de ressources, **SQLServers_Excluded** et allons à présent lui octroyer une exception à cette affectation de stratégie.

### <a name="update-assignment-with-exclusion"></a>Mettre à jour une affectation avec une exclusion

1. Sélectionnez **Affectations** sous **CRÉATION** dans la partie gauche de la page Azure Policy.
1. Parcourez toutes les affectations de stratégie, puis ouvrez l’affectation *Nécessiter SQL Server version 12.0*.
1. Définissez l’**exclusion** en cliquant sur le bouton de sélection et en sélectionnant le groupe de ressources à exclure, *SQLServers_Excluded* dans cet exemple.

   ![Exclusion d’une demande](media/create-manage-policy/request-exclusion.png)

   > [!NOTE]
   > En fonction de la stratégie et de son effet, l’exclusion peut également être octroyée à des ressources spécifiques au sein du groupe de ressources dans l’étendue de l’affectation. Comme un effet **Refuser** a été utilisé dans ce tutoriel, il ne serait pas judicieux de définir l’exclusion sur une ressource spécifique qui existe déjà.

1. Cliquez sur **Sélectionner**, puis sur **Enregistrer**.

Dans cette section, vous avez résolu le refus de tentative de création d’une version interdite de serveur SQL en créant une exclusion sur un seul groupe de ressources.

## <a name="clean-up-resources"></a>Supprimer les ressources

Si vous avez fini d’utiliser les ressources de ce tutoriel, effectuez les étapes suivantes pour supprimer les affectations ou définitions créées ci-dessus :

1. Sélectionnez **Définitions** (ou **Affectations** si vous essayez de supprimer une affectation) sous **CRÉATION** dans la partie gauche de la page Azure Policy.
1. Recherchez la nouvelle définition d’initiative ou de stratégie (ou affectation) à supprimer.
1. Cliquez avec le bouton droit sur la liste ou cliquez sur le bouton de sélection en fin de définition (ou d’affectation), puis sélectionnez **Supprimer la définition** (ou **Supprimer l’affectation**).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez effectué avec succès les opérations suivantes :

> [!div class="checklist"]
> - Attribué une stratégie pour appliquer une condition aux ressources que vous créez dans le futur
> - Créé et attribué une définition d’initiative pour effectuer le suivi de la conformité de plusieurs ressources
> - Résolu une ressource non conforme ou refusée
> - Implémenté une nouvelle stratégie dans l’ensemble de l’entreprise

Pour plus d’informations sur les structures des définitions de stratégie, consultez cet article :

> [!div class="nextstepaction"]
> [Structure de définition Azure Policy](policy-definition.md)