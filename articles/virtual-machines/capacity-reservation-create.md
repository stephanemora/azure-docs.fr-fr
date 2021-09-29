---
title: Créer une réservation de capacité dans Azure (version préliminaire)
description: Découvrez comment réserver de la capacité de calcul dans une région ou une zone de disponibilité Azure en créant une réservation de capacité.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: db9b8b71ddbdccb2cdc4893ce7ea041304bafba9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776611"
---
# <a name="create-a-capacity-reservation-preview"></a>Créer une réservation de capacité (version préliminaire)

La réservation de capacité est toujours créée dans le cadre d’un groupe de réservations de capacité. La première étape consiste à créer un groupe si un groupe approprié n’existe pas déjà, puis à créer des réservations. Une fois correctement créé, les réservations sont immédiatement disponibles pour une utilisation avec des machines virtuelles. La capacité est réservée à votre utilisation tant que la réservation n’est pas supprimée.     

Une requête bien formée pour le groupe de réservations de capacité doit toujours réussir, car elle ne réserve aucune capacité. Il agit simplement comme un conteneur pour les réservations. Toutefois, une requête de réservation de capacité peut échouer si vous n’avez pas le quota requis pour la série de machines virtuelles ou si Azure ne dispose pas d’une capacité suffisante pour traiter la requête. Demandez plus de quota ou essayez une taille de machine virtuelle, un emplacement ou une combinaison de zones différents. 

La création d’une réservation de capacité réussit ou échoue dans son intégralité. Pour qu’une demande de réserve de 10 instances, la réussite est retournée uniquement si les 10 peuvent être allouées. Dans le cas contraire, la création de réservation de capacité échoue. 

> [!IMPORTANT]
> La réservation de capacité est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-for-capacity-reservation"></a>S’inscrire à la réservation de capacité 

Avant de pouvoir utiliser la fonctionnalité Réservation de capacité, vous devez [inscrire votre abonnement pour la préversion](capacity-reservation-overview.md#register-for-capacity-reservation). L’inscription peut prendre plusieurs minutes. Vous pouvez utiliser Azure CLI ou PowerShell pour finaliser l’inscription de la fonctionnalité.


## <a name="considerations"></a>Considérations

La réservation de capacité doit respecter les règles suivantes : 
- Le paramètre d’emplacement doit correspondre à la propriété d’emplacement pour le groupe de réservations de capacité parent. Une incompatibilité se traduira par une erreur. 
- La taille de la machine virtuelle doit être disponible dans la région cible. Dans le cas contraire, la création de réservation échoue. 
- L’abonnement doit avoir un quota approuvé suffisant égal ou supérieur à la quantité de machines virtuelles réservées pour la série de machines virtuelles et pour la région globale. Si nécessaire, [demandez plus de quota](../azure-portal/supportability/per-vm-quota-requests.md).
- Chaque groupe de réservations de capacité peut avoir une seule réservation pour une taille de machine virtuelle donnée. Par exemple, une seule réservation de capacité peut être créée pour la taille de la machine virtuelle `Standard_D2s_v3`. Toute tentative de création d’une deuxième réservation pour `Standard_D2s_v3` dans le même groupe de réservations de capacité génère une erreur. Toutefois, une autre réservation peut être créée dans le même groupe pour d’autres tailles de machine virtuelle, telles que `Standard_D4s_v3`, `Standard_D8s_v3`, etc.  
- Pour un groupe de réservations de capacité qui prend en charge les zones, chaque type de réservation est défini par la combinaison de la taille de la **machine virtuelle** et de la **zone**. Par exemple, une réservation de capacité pour `Standard_D2s_v3` dans `Zone 1`, une autre réservation de capacité pour `Standard_D2s_v3` dans `Zone 2` et une troisième réservation de capacité pour `Standard_D2s_v3` dans `Zone 3` est prise en charge.


## <a name="create-a-capacity-reservation"></a>Créer une réservation de capacité 

### <a name="api"></a>[API](#tab/api1)

1. Créer un groupe de réservations de capacité 

    Pour créer un groupe de réservations de capacité, construisez la requête PUT suivante sur le fournisseur *Microsoft. Compute* : 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}&api-version=2021-04-01
    ``` 
    
    Dans le corps de la requête, ajoutez les éléments suivants : 
    
    ```json
    { 
      "location":"eastus"
    } 
    ```
    
    Ce groupe est créé pour contenir des réservations pour l’emplacement USA Est. 
    
    Dans cet exemple, le groupe prend en charge uniquement les réservations régionales, car les zones n’ont pas été spécifiées au moment de la création. Pour créer un groupe de zone, transmettez un paramètre supplémentaire *zones* dans le corps de la requête, comme indiqué ci-dessous : 
    
    ```json
    { 
      "location":"eastus",
      "zones": ["1", "2", "3"] 
    } 
    ```
 
1. Créer une réservation de capacité 

    Pour créer une réservation, construisez la requête PUT suivante sur le fournisseur *Microsoft.Compute* : 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01 
    ```
    
    Dans le corps de la requête, ajoutez les éléments suivants : 
    
    ```json
    { 
      "location": "eastus", 
      "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
      }, 
     "tags": { 
            "environment": "testing" 
    } 
    ```
    
    La requête ci-dessus crée une réservation dans l’emplacement USA Est pour 5 quantités de la taille de machine virtuelle D2s_v3. 


### <a name="portal"></a>[Portail](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Dans la barre de recherche, tapez **groupes de réservations de capacité**
1. Sélectionnez **Groupes de réservations de capacité** parmi les options
1. Sélectionnez **Créer**
1. Sous l’onglet *Bases*, créez un groupe de réservations de capacité :
    1. Sélectionnez un **Abonnement**
    1. Sélectionnez ou créez un **Groupe de ressources**
    1. **Nommez** votre groupe 
    1. Sélectionnez une **Région** 
    1. Vous pouvez également sélectionner des **zones de disponibilité** ou choisir de ne pas spécifier de zones et autoriser Azure à choisir pour vous
1. Sélectionnez **Suivant**.
1. Sous l’onglet *Réservations*, créez au moins une réservation de capacité :
    1. Attribuez à chaque réservation un **Nom de réservation**, la quantité d'**Instances** de machine virtuelle et sélectionnez une **taille de machine virtuelle** unique
    1. La colonne *Coût/mois* affiche les informations de facturation en fonction de vos sélections
1. Sélectionnez **Suivant**.
1. Sous l’onglet *Balises*, créez éventuellement des balises
1. Sélectionnez **Suivant**. 
1. Sous l’onglet *Vérifier + Créer*, passez en revue les informations de votre groupe de réservations de capacité
1. Sélectionnez **Créer**


### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Avant de pouvoir créer une réservation de capacité, créez un groupe de ressources avec `New-AzResourceGroup`. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement USA Est.

    ```powershell-interactive
    New-AzResourceGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    ```

1. Créez maintenant un groupe de réservations de capacité avec `New-AzCapacityReservationGroup`. L’exemple suivant crée un groupe *myCapacityReservationGroup* à l’emplacement USA Est pour les 3 zones de disponibilité.

    ```powershell-interactive
    New-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1","2","3"
    -Name "myCapacityReservationGroup"
    ```

1. Une fois le groupe de réservations de capacité créé, créez une nouvelle réservation de capacité avec `New-AzCapacityReservation`. L’exemple suivant crée *myCapacityReservation* pour 5 quantités de taille de machine virtuelle Standard_D2s_v3 dans la zone 1 de l’emplacement USA Est.

    ```powershell-interactive
    New-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -Sku "Standard_D2s_v3"
    -CapacityToReserve 5
    ```

Pour plus d’informations, accédez aux commandes Azure PowerShell [New-AzResourceGroup](/powershell/module/az.Resources/new-azresourcegroup), [New-AzCapacityReservationGroup](/powershell/module/az.compute/new-azcapacityreservationgroup), et [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation).


### <a name="arm-template"></a>[Modèle ARM](#tab/arm1)

Un  [modèle ARM](../azure-resource-manager/templates/overview.md)  est un fichier JSON (JavaScript Object Notation) qui définit l’infrastructure et la configuration de votre projet. Le modèle utilise la syntaxe déclarative. Dans la syntaxe déclarative, vous décrivez le déploiement souhaité sans écrire la séquence de commandes de programmation pour créer le déploiement. 

Les modèles ARM vous permettent de déployer des groupes de ressources associées. Dans un modèle unique, vous pouvez créer un groupe de réservations de capacité et des réservations de capacité. Vous pouvez déployer des modèles via le Portail Azure, Azure CLI ou Azure PowerShell, ou à partir de pipelines d’intégration continue/de livraison continue. 

Si votre environnement satisfait les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, utilisez un des modèles suivants : 

- [Créer une réservation de capacité zonale](https://github.com/Azure/on-demand-capacity-reservation/blob/main/ZonalCapacityReservation.json)
- [Créer une machine virtuelle avec la réservation de capacité](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json)
- [Créer Virtual Machine Scale Sets avec réservation de capacité](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json)


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="check-on-your-capacity-reservation"></a>Vérifier votre réservation de capacité 

Une fois correctement créé, la réservation de capacité est immédiatement disponible pour une utilisation avec des machines virtuelles. 

### <a name="api"></a>[API](#tab/api2)

```rest
GET  
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```
 
```json
{ 
    "name": "<CapacityReservationName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}", 
    "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
    "location": "eastus", 
    "tags": { 
        "environment": "testing" 
    }, 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
    }, 
    "properties": { 
        "reservationId": "<reservationId>", 
         "provisioningTime": "<provisioningTime>", 
         "provisioningState": "Updating" 
    } 
} 
```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

Vérifiez votre réservation de capacité :

```powershell-interactive
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
```

Pour rechercher la taille de la machine virtuelle et la quantité réservée, utilisez la commande suivante : 

```powershell-interactive
$CapRes =
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">

$CapRes.sku
```

Pour plus d’informations, accédez à la commande Azure PowerShell [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation).

### <a name="portal"></a>[Portail](#tab/portal3)

1. Ouvrez le [portail Azure](https://portal.azure.com).
1. Dans la barre de recherche, tapez **groupes de réservations de capacité**
1. Sélectionnez **Groupes de réservations de capacité** parmi les options
1. Dans la liste, sélectionnez le nom du groupe de réservations de capacité que vous venez de créer
1. Sélectionnez **Vue d’ensemble** à gauche
1. Choisissez **Réservations**
1. Dans cette vue, vous pourrez voir toutes les réservations dans le groupe, ainsi que la taille de machine virtuelle et la quantité réservée

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment modifier votre réservation de capacité](capacity-reservation-modify.md)
