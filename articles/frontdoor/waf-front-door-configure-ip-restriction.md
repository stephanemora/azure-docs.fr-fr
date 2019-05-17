---
title: Configurer une règle de restriction IP avec la règle de pare-feu d’application web pour Azure porte d’entrée
description: Découvrez comment configurer une règle de pare-feu d’applications Web de restriction d’adresse IP pour un point de terminaison existant porte d’entrée.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: kumud;tyao
ms.openlocfilehash: b129579916330a34a2a78d98f2c7653f129d3319
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523706"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>Configurer une règle de restriction IP avec des pare-feu d’applications web pour Azure porte d’entrée (version préliminaire)
 Cet article vous montre comment configurer des règles de restriction IP dans le pare-feu d’applications Azure web (WAF) pour la porte d’entrée à l’aide d’Azure CLI, Azure PowerShell ou Azure Resource Manager d’un modèle.

Une règle d’accès basées sur des adresses IP est une règle de pare-feu d’applications Web personnalisée qui vous permet de contrôler l’accès à vos applications web en spécifiant une liste d’adresses IP ou des plages d’adresses IP sous forme d’Interdomain routage CIDR (Classless).

Par défaut, votre application web est accessible à partir d’internet. Si vous souhaitez limiter l’accès à vos applications web uniquement aux clients à partir d’une liste d’adresses IP connues ou des plages d’adresses IP, vous devez créer deux règles de correspondance d’IP. Première règle IP contient la liste des adresses IP en tant que valeurs correspondantes et définissez l’action « Autoriser ». L’autre avec une priorité basse, consiste à bloquer toutes les autres adresses IP à l’aide de l’opérateur « All » et définissez l’action sur « Bloc ». Une fois qu’une règle de restriction IP est appliquée, toutes les demandes provenant d’adresses en dehors de cette liste autorisée reçoit une réponse 403 (interdit).  

> [!IMPORTANT]
> La fonctionnalité de restriction IP de WAF pour Azure porte d’entrée est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="configure-waf-policy-with-azure-cli"></a>Configurer la stratégie de pare-feu d’applications Web avec Azure CLI

### <a name="prerequisites"></a>Conditions préalables
Avant de commencer à configurer une stratégie de restriction IP, configurez votre environnement CLI et créer un profil de la porte d’entrée.

#### <a name="set-up-azure-cli-environment"></a>Configuration d’environnement d’Azure CLI
1. Installer le [Azure CLI](/cli/azure/install-azure-cli), ou utiliser Azure Cloud Shell. Azure Cloud Shell est un interpréteur de commandes Bash gratuit, que vous pouvez exécuter directement dans le portail Azure. Il a l’interface CLI Azure préalablement installée et configurée à utiliser avec votre compte. Sélectionnez le **essayez-le** commandes de bouton dans l’interface CLI qui suivent. En sélectionnant **essayez-le** appelle un Cloud Shell avec lequel vous pouvez vous connecter à votre compte Azure avec. Une fois qu’une session d’interpréteur de commandes cloud démarre, entrez `az extension add --name front-door` pour ajouter l’extension porte d’entrée.
 2. Si vous utilisez l’interface CLI localement dans Bash, connectez-vous à Azure avec `az login`.

#### <a name="create-front-door-profile"></a>Créer un profil de la porte d’entrée
Créez un profil Front Door en suivant les instructions décrites dans [Démarrage rapide : Créer un profil de la porte d’entrée](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Créer une stratégie de pare-feu d’applications Web

Créez une stratégie de pare-feu d’applications Web avec le [créer az réseau waf-policy](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) commande. Dans l’exemple ci-dessous, remplacez le nom de la stratégie *IPAllowPolicyExampleCLI* avec un nom unique de stratégie.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>Ajouter la règle de contrôle d’accès IP personnalisé

Ajouter une règle de contrôle d’accès IP personnalisée à la stratégie de pare-feu d’applications Web créée à l’étape précédente avec le [créer az réseau waf-policy-règle personnalisée](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) commande. 

Dans l’exemple ci-dessous :
-  Remplacez *IPAllowPolicyExampleCLI* avec votre stratégie unique créé précédemment.
-  Remplacez *ip-adresse-plage de-1*, *ip-adresse-range-2* avec votre propre plage.

Tout d’abord, créez l’adresse IP permettent de règle pour les adresses spécifiées.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Ensuite, créez un bloc de toutes les règles IP avec une priorité plus faible que la règle d’autorisation de l’adresse IP précédente. Remplacez le *IPAllowPolicyExampleCLI* avec votre stratégie unique créé précédemment.

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

### <a name="find-waf-policy-id"></a>Rechercher des ID de stratégie de pare-feu d’applications Web
Rechercher l’ID d’une stratégie de pare-feu d’applications Web avec le [az réseau waf-policy afficher](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) commande. Remplacez le *IPAllowPolicyExampleCLI* avec votre stratégie unique créé précédemment.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Stratégie de lien WAF à un hôte de serveur frontal porte d’entrée

Définir la porte *WebApplicationFirewallPolicyLink* ID à l’ID de stratégie avec le [mise à jour de la porte d’entrée-az réseau](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) commande. Remplacez le *IPAllowPolicyExampleCLI* avec votre stratégie unique créé précédemment.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Dans cet exemple, la stratégie de pare-feu d’applications Web est appliquée à FrontendEndpoints [0]. Vous pouvez lier la stratégie de pare-feu d’applications Web à un de vos serveurs frontaux.
> [!Note]
> Vous devez uniquement définir le **WebApplicationFirewallPolicyLink** propriété une fois pour lier une stratégie de pare-feu d’applications Web à une porte d’entrée frontal. Mises à jour de stratégie suivantes sont automatiquement appliquées à la partie frontale.

## <a name="configure-waf-policy-with-azure-powershell"></a>Configurer la stratégie de pare-feu d’applications Web avec Azure PowerShell

### <a name="prerequisites"></a>Conditions préalables
Avant de commencer à configurer une stratégie de restriction IP, configurez votre environnement PowerShell et créer un profil de la porte d’entrée.

#### <a name="set-up-your-powershell-environment"></a>Configurer votre environnement PowerShell
Azure PowerShell fournit un ensemble d’applets de commande qui utilisent le modèle [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pour gérer vos ressources Azure. 

Vous pouvez installer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) sur votre ordinateur local et l’utiliser sur n’importe quelle session PowerShell. Suivez les instructions sur la page, pour vous connecter avec vos informations d’identification Azure et installer le module PowerShell de Az.

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Se connecter à Azure avec une boîte de dialogue interactive pour la connexion
```
Connect-AzAccount

```
Avant d’installer le module de porte d’entrée, assurez-vous que vous avez la version actuelle de PowerShellGet est installé. Exécutez la commande ci-dessous et les rouvrir PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>Installer le module Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Créer un profil Front Door
Créez un profil Front Door en suivant les instructions décrites dans [Démarrage rapide : Créer un profil de la porte d’entrée](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>Définir la condition de correspondance IP
Utilisez le [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) commande pour définir une condition de correspondance IP. Dans l’exemple ci-dessous, remplacez *ip-adresse-plage de-1*, *ip-adresse-range-2* avec votre propre plage.

```powershell
  $IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Créer une correspondance IP toutes les règles de condition
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
Créez un bloc de toutes les règles IP avec une priorité plus faible que la règle d’autorisation de l’adresse IP précédente.

```powershell
  $IPBlockAll = New-AzFrontDoorCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>Configurer la stratégie de pare-feu d’applications Web
Recherchez le nom du groupe de ressources qui contient le profil Front Door à l’aide de `Get-AzResourceGroup`. Ensuite, configurez une stratégie de pare-feu d’applications Web avec la règle de bloc IP à l’aide [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Stratégie de lien WAF à un hôte de serveur frontal porte d’entrée

Lier l’objet de stratégie de pare-feu d’applications Web à un hôte de serveur frontal existant porte d’entrée et de mettre à jour les propriétés de la porte d’entrée. Tout d’abord récupérer l’objet de la porte d’entrée en utilisant [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Ensuite, définissez le serveur frontal *WebApplicationFirewallPolicyLink* propriété à l’ID de ressource de la *$IPAllowPolicyExamplePS* créé à l’étape précédente avec le [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) commande.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Dans cet exemple, la stratégie de pare-feu d’applications Web est appliquée à FrontendEndpoints [0]. Vous pouvez lier la stratégie de pare-feu d’applications Web à un de vos serveurs frontaux. Vous devez uniquement définir *WebApplicationFirewallPolicyLink* propriété une fois pour lier une stratégie de pare-feu d’applications Web à une porte d’entrée frontal. Mises à jour de stratégie suivantes sont automatiquement appliquées à la partie frontale.


## <a name="configure-waf-policy-with-resource-manager-template"></a>Configurer la stratégie de pare-feu d’applications Web avec le modèle Resource Manager
Afficher le modèle qui crée une porte d’entrée et une stratégie de pare-feu d’applications Web avec des règles de restriction IP personnalisées [ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer un profil de la porte d’entrée](quickstart-create-front-door.md).
