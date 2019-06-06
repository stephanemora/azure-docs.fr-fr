---
title: Configurer une règle de restriction IP avec une règle de pare-feu d’application web pour le Service de porte d’entrée Azure
description: Découvrez comment configurer une règle de pare-feu d’application web pour limiter les adresses IP pour un point de terminaison de Service de porte d’entrée Azure existant.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 88c5c284f26203ff3d6c39810a7b2810c1ebbc5a
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743163"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Configurer une règle de restriction IP avec un pare-feu d’applications web pour le Service de porte d’entrée Azure
Cet article vous montre comment configurer des règles de restriction IP dans un pare-feu d’applications web (WAF) pour le Service de porte d’entrée Azure à l’aide de l’interface CLI, Azure PowerShell ou un modèle Azure Resource Manager.

Une règle de contrôle d’accès basé sur l’adresse IP est une règle de pare-feu d’applications Web personnalisée qui vous permet de contrôler l’accès à vos applications web. Il effectue cela en spécifiant une liste d’adresses IP ou des plages d’adresses IP au format d’Interdomain routage CIDR (Classless).

Par défaut, votre application web est accessible à partir d’internet. Si vous souhaitez limiter l’accès aux clients à partir d’une liste d’adresses IP connues ou des plages d’adresses IP, vous devez créer deux règles de correspondance d’IP. La première règle correspondante IP contient la liste des adresses IP en tant que valeurs correspondantes et affecte à l’action **autoriser**. L’autre, avec une priorité basse, bloque toutes les autres adresses IP à l’aide de la **tous les** opérateur et en définissant l’action sur **bloc**. Une fois une règle de restriction IP est appliquée, les demandes provenant d’adresses en dehors de cette liste autorisée recevoir une réponse 403 Interdit.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configurer une stratégie de pare-feu d’applications Web avec l’interface CLI Azure

### <a name="prerequisites"></a>Conditions préalables
Avant de commencer à configurer une stratégie de restriction IP, configurez votre environnement CLI et créer un profil de Service de porte d’Azure.

#### <a name="set-up-the-azure-cli-environment"></a>Configurer l’environnement Azure CLI
1. Installer le [Azure CLI](/cli/azure/install-azure-cli), ou utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. L’interface Azure CLI est préinstallée et configurée pour être utilisée avec votre compte. Sélectionnez le **essayez-le** bouton dans les commandes CLI qui suivent et puis connectez-vous à votre compte Azure dans la session de Cloud Shell s’ouvre. Une fois la session démarre, entrez `az extension add --name front-door` pour ajouter l’extension de Service de porte d’Azure.
 2. Si vous utilisez l’interface CLI localement dans Bash, connectez-vous à Azure à l’aide de `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Créer un profil de Service de porte d’entrée Azure
Créer un profil de Service de porte d’entrée Azure en suivant les instructions décrites dans [Guide de démarrage rapide : Créer une porte d’entrée pour une application web hautement disponibles à l’international](quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Créer une stratégie de pare-feu d’applications Web

Créer une stratégie de pare-feu d’applications Web à l’aide de la [créer az réseau waf-policy](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) commande. Dans l’exemple que suit, remplacez le nom de la stratégie *IPAllowPolicyExampleCLI* avec un nom unique de stratégie.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Ajouter une règle de contrôle d’accès IP personnalisée

Utilisez le [créer az réseau waf-policy-règle personnalisée](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) commande pour ajouter une adresse IP accès contrôle règle personnalisée pour la stratégie de pare-feu d’applications Web vous venez de créer.

Dans les exemples suivants :
-  Remplacez *IPAllowPolicyExampleCLI* avec votre stratégie unique créé précédemment.
-  Remplacez *ip-adresse-plage de-1*, *ip-adresse-range-2* avec votre propre plage.

Tout d’abord, créez l’adresse IP permettent de règle pour les adresses spécifiées.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch "<ip-address-range-1>","<ip-address-range-2>" \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Ensuite, créez un **bloquer tout** règle avec une priorité plus faible que le précédent **autoriser** règle. Là encore, remplacez *IPAllowPolicyExampleCLI* dans l’exemple suivant avec votre stratégie unique que vous avez créé précédemment.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
    
### <a name="find-the-id-of-a-waf-policy"></a>Rechercher l’ID d’une stratégie de pare-feu d’applications Web 
Rechercher les ID d’une stratégie de pare-feu d’applications Web à l’aide de la [az réseau waf-policy afficher](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) commande. Remplacez *IPAllowPolicyExampleCLI* dans l’exemple suivant avec votre stratégie unique que vous avez créé précédemment.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Lier une stratégie de pare-feu d’applications Web à un hôte de serveur frontal Azure porte Service

Configurer le Service de la porte d’entrée Azure *WebApplicationFirewallPolicyLink* ID à l’ID de stratégie à l’aide de la [mise à jour de la porte d’entrée-az réseau](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) commande. Remplacez *IPAllowPolicyExampleCLI* avec votre stratégie unique que vous avez créé précédemment.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Dans cet exemple, la stratégie de pare-feu d’applications Web est appliquée à **FrontendEndpoints [0]** . Vous pouvez lier la stratégie de pare-feu d’applications Web à un de vos serveurs frontaux.
> [!Note]
> Vous devez définir le **WebApplicationFirewallPolicyLink** propriété qu’une seule fois pour lier une stratégie de pare-feu d’applications Web à un serveur frontal Azure porte Service. Mises à jour de stratégie suivantes sont automatiquement appliquées à la partie frontale.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configurer une stratégie de pare-feu d’applications Web avec Azure PowerShell

### <a name="prerequisites"></a>Conditions préalables
Avant de commencer à configurer une stratégie de restriction IP, configurez votre environnement PowerShell et créez un profil de Service de porte d’Azure.

#### <a name="set-up-your-powershell-environment"></a>Configurer votre environnement PowerShell
Azure PowerShell fournit un ensemble d’applets de commande qui utilisent la [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modèle pour la gestion des ressources Azure.

Vous pouvez installer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) sur votre ordinateur local et l’utiliser sur n’importe quelle session PowerShell. Suivez les instructions sur la page pour vous connecter à PowerShell à l’aide de vos informations d’identification Azure et puis installer le module Az.

1. Se connecter à Azure à l’aide de la commande suivante et ensuite utiliser une boîte de dialogue interactive pour se connecter.
    ```
    Connect-AzAccount
    ```
 2. Avant d’installer un module de Service de porte d’Azure, assurez-vous que vous avez la version actuelle du module PowerShellGet est installée. Exécutez la commande suivante, puis rouvrez PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installer le module Az.FrontDoor à l’aide de la commande suivante. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Créer un profil de Service de porte d’entrée Azure
Créer un profil de Service de porte d’entrée Azure en suivant les instructions décrites dans [Guide de démarrage rapide : Créer une porte d’entrée pour une application web hautement disponibles à l’international](quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Définir une condition de correspondance IP
Utilisez le [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) commande pour définir une condition de correspondance IP.
Dans l’exemple suivant, remplacez *ip-adresse-plage de-1*, *ip-adresse-range-2* avec votre propre plage.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Créer une adresse IP *tous les condition de correspondance* règle à l’aide de la commande suivante :
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>Créez une règle d’autorisation IP

Utilisez le [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) commande pour définir une action et définissez une priorité. Dans l’exemple suivant, à partir d’adresses IP des clients, les requêtes qui correspondent à la liste seront autorisés.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
Créer un **bloquer tout** règle avec une priorité plus faible que l’adresse IP précédente **autoriser** règle.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>Configurer une stratégie de pare-feu d’applications Web
Rechercher le nom du groupe de ressources qui contient le profil de Service de porte d’entrée Azure à l’aide de `Get-AzResourceGroup`. Ensuite, configurez une stratégie de pare-feu d’applications Web avec l’adresse IP **bloquer tout** règle à l’aide de [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Lier une stratégie de pare-feu d’applications Web à un hôte de serveur frontal Azure porte Service

Lier un objet de stratégie de pare-feu d’applications Web à un frontal hôte et mise à jour Azure porte Service des propriétés existantes. Récupérez tout d’abord l’objet de Service de porte d’entrée Azure à l’aide de [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Ensuite, définissez le **WebApplicationFirewallPolicyLink** à l’ID de ressource de propriété *$IPAllowPolicyExamplePS*, créé à l’étape précédente, à l’aide de la [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)commande.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Dans cet exemple, la stratégie de pare-feu d’applications Web est appliquée à **FrontendEndpoints [0]** . Vous pouvez lier une stratégie de pare-feu d’applications Web à un de vos serveurs frontaux. Vous devez définir le **WebApplicationFirewallPolicyLink** propriété qu’une seule fois pour lier une stratégie de pare-feu d’applications Web à un serveur frontal Azure porte Service. Mises à jour de stratégie suivantes sont automatiquement appliquées à la partie frontale.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configurer une stratégie de pare-feu d’applications Web avec un modèle Resource Manager
Pour afficher le modèle qui crée une stratégie de Service de porte d’entrée Azure et une stratégie de pare-feu d’applications Web avec des règles de restriction IP personnalisées, accédez à [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer un profil de Service de porte d’entrée Azure](quickstart-create-front-door.md).
