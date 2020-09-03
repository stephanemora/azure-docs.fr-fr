---
title: Mettre à l’échelle et protéger rapidement une application web à l’aide d’Azure Front Door et du pare-feu d’applications web Azure (WAF) | Microsoft Docs
description: Cet article vous aide à comprendre comment utiliser le pare-feu d’applications web avec votre service Azure Front Door
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: duau
ms.openlocfilehash: 7d2978b34d4c1e6cf85c65be2e9c3292ec704be4
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89398883"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Mettez à l’échelle et protégez rapidement une application web à l’aide d’Azure Front Door et du pare-feu d’applications web Azure (WAF)

Beaucoup d’applications web ont connu une augmentation rapide du trafic au cours des dernières semaines liée au COVID-19. De plus, ces applications web observent également une augmentation du trafic malveillant, notamment des attaques par déni de service. Un moyen efficace de répondre à ces deux problématiques, se mettre à l’échelle face au pic de trafic et se protéger des attaques, consiste à configurer Azure Front Door avec Azure WAF comme une couche d’accélération, de mise en cache et de sécurité avant votre application web. Cet article fournit des conseils sur la façon d’obtenir rapidement cette configuration Azure Front Door avec Azure WAF pour toutes les applications web exécutées au sein d’Azure ou en dehors. 

Dans ce tutoriel, nous utilisons Azure CLI pour configurer les pare-feu d’applications web, mais toutes ces étapes sont également entièrement prises en charge par le portail Azure, Azure PowerShell, Azure ARM, et les API REST d’Azure. 

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

Les instructions de ce blog utilisent l’interface CLI d’Azure. Consultez ce guide pour [Bien démarrer avec Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

*Conseil : une méthode simple et rapide pour la bien démarrer avec Azure CLI consiste à utiliser [Bash dans Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

Assurez-vous que l’extension Front Door est ajoutée à votre interface de ligne de commande Azure

```azurecli-interactive 
az extension add --name front-door
```

Remarque : Pour plus d’informations sur les commandes listées ci-dessous, reportez-vous à aux [informations de référence sur Azure CLI pour Front Door](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest).

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>Étape 1 : Créer une ressource Azure Front Door (AFD)


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-adresse** : L’adresse principale est le nom de domaine complet (FQDN) de l’application que vous souhaitez protéger. Par exemple, myapplication.contoso.com

**--accepted-protocols** : Les protocoles acceptés spécifient les protocoles que vous souhaitez que AFD prenne en charge pour votre application web. Par exemple, --accepted-protocols Http Https.

**--name** : Spécifiez un nom pour votre ressource AFD

**--resource-group** : Le groupe de ressources que vous souhaitez placer au sein de la ressource AFD.  Pour en savoir plus sur les groupes de ressources, consultez gérer les groupes de ressources dans Azure

Lorsque cette commande est correctement exécutée, recherchez la clé « hostName » dans la réponse que vous recevez et notez sa valeur pour l’utiliser lors d’une prochaine étape. Le nom d’hôte est le nom DNS de la ressource AFD que vous avez créée

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Étape 2 : Créer un profil Azure WAF à utiliser avec les ressources Azure Front Door

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

\- name : spécifiez un nom pour votre stratégie Azure WAF

\- resource-group : le groupe de ressource dans lequel vous souhaitez placer cette ressource WAF. 

Le code CLI ci-dessus crée une stratégie WAF qui est activée et est en mode de prévention. 

Remarque : vous pouvez également créer le pare-feu d’applications web en mode de détection et observer comment il détecte et enregistre les requêtes malveillantes (sans les bloquer) avant de décider de passer en mode de protection.

Lorsque cette commande est correctement exécutée, recherchez la clé « ID » dans la réponse que vous recevez et notez sa valeur pour l’utiliser lors d’une prochaine étape. Le champ d’ID doit respecter le format

/subscriptions/**subscription id**/resourcegroups/**resource group name**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF policy name**

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>Étape 3 : Ajouter des ensembles de règles managés à cette stratégie WAF

Dans une stratégie WAF, vous pouvez ajouter des ensembles de règles managés qui sont un ensemble de règles créées et managéés par Microsoft, et qui offre une protection prête à l’emploi contre des catégories entières de menaces. Dans cet exemple, nous ajoutons deux ensembles de règles de ce type (1) un ensemble de règles par défaut qui protègent contre les menaces web courantes et (2) un ensemble de règles de protection bot, qui protège contre les bots malveillants

(1) Ajouter l’ensemble de règles par défaut

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) Ajouter l’ensemble de règles de gestion des bots

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

\- policy-name : le nom que vous avez donné à votre ressource WAF Azure

\- resource-group : le groupe de ressource dans lequel vous aviez placé cette ressource WAF.

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>Étape 4 : Associer la stratégie WAF à la ressource AFD

Dans cette étape, nous associons la stratégie WAF que nous avons créée avec la ressource AFD qui se trouve avant votre application web.

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

\- name : le nom que vous aviez spécifié pour votre ressource AFD

\- resource-group : le groupe de ressources dans lequel vous aviez placé la ressource Azure Front Door.

\- set : c’est l’emplacement où vous mettez à jour l’attribut WebApplicationFirewallPolicyLink pour le point de terminaison frontend (frontendEndpoint) associé à votre ressource AFD avec la stratégie WAF qui vient d’être créée. L’ID de la stratégie WAF se trouve dans la réponse que vous avez obtenue à l’étape n° 2 ci-dessus

Remarque : l’exemple ci-dessus concerne le cas où vous n’utilisez pas de domaine personnalisé.

Si vous n’utilisez pas de domaines personnalisés pour accéder à vos applications web, vous pouvez ignorer l’étape n° 5. Dans ce cas, vous fournissez à vos utilisateurs finaux le nom d’hôte que vous avez obtenu à l’étape n° 1 pour qu’ils puissent accéder à votre application web

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>Étape 5 : Configurer un domaine personnalisé pour votre application web

Dans un premier temps, le nom de domaine personnalisé de votre application web (celui que les clients utilisent pour faire référence à votre application, par exemple, www.contoso.com) indiquait l’endroit où vous l’aviez exécuté avant l’introduction de AFD. Après ce changement d’architecture lié à l’ajout de AFD + WAF en première ligne de l’application, l’entrée DNS correspondant à ce domaine personnalisé doit maintenant indiquer cette ressource AFD. Pour ce faire, vous pouvez remapper cette entrée sur votre serveur DNS vers le nom d’hôte AFD que vous avez noté à l’étape n° 1.

Les étapes spécifiques pour mettre à jour vos enregistrements DNS dépendront de votre fournisseur de services DNS. Toutefois, si vous utilisez Azure DNS pour héberger votre nom DNS, vous pouvez vous reporter à la documentation intitulée [étapes pour mettre à jour un enregistrement DNS](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli) et indiquer le nom d’hôte AFD. 

Il est important de noter que, si vous avez besoin que vos utilisateurs accèdent à votre site web à l’aide d’apex de zone, par exemple contoso.com, vous devez utiliser Azure DNS et son [n’importe quel type d’enregistrement](https://docs.microsoft.com/azure/dns/dns-alias) pour héberger votre nom DNS. 

En outre, vous devez également mettre à jour votre configuration AFD pour y [ajouter ce domaine personnalisé](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain) afin qu’AFD comprenne ce mappage.

Enfin, si vous utilisez un domaine personnalisé pour atteindre votre application web et que vous souhaitez activer le protocole HTTPs, vous devez disposer des [certificats pour votre configuration de domaine personnalisé dans AFD](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https). 

## <a name="step-6-lock-down-your-web-application"></a>Étape 6 : Verrouiller votre application web

Assurez-vous que seules les périphéries AFD peuvent communiquer avec votre application web. Cela fait partie des meilleures pratiques à suivre facultatives. Cette action permet de s’assurer que personne ne peut contourner les protections AFD et accéder directement à vos applications. Vous pouvez effectuer ce verrouillage en visitant la [section du FAQ sur AFD](https://docs.microsoft.com/azure/frontdoor/front-door-faq) et référez-vous à la question relative au verrouillage des serveurs principaux pour l’accès via AFD uniquement.
