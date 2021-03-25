---
title: Guide pratique pour utiliser des points de terminaison privés avec Speech Services
titleSuffix: Azure Cognitive Services
description: Découvrir comment utiliser Speech Services avec des points de terminaison privés fournis par Azure Private Link
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: alexeyo
ms.openlocfilehash: c9af0cda14261e8eab7f1ecc05c50a289d7ddfdb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99559652"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>Utiliser Speech Services via un point de terminaison privé

[Azure Private Link](../../private-link/private-link-overview.md) vous permet de vous connecter aux services dans Azure en utilisant un [point de terminaison privé](../../private-link/private-endpoint-overview.md). Un point de terminaison privé est une adresse IP privée qui est accessible uniquement au sein d’un [réseau virtuel](../../virtual-network/virtual-networks-overview.md) et d’un sous-réseau spécifiques.

Cet article explique comment configurer et utiliser Private Link et des points de terminaison privés avec Speech Services dans Azure Cognitive Services.

> [!NOTE]
> Avant de continuer, consultez l’article qui explique [comment utiliser des réseaux virtuels avec Cognitive Services](../cognitive-services-virtual-networks.md).

Cet article décrit également [comment supprimer des points de terminaison privés ultérieurement, tout en continuant à utiliser la ressource Speech](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Créer un nom de domaine personnalisé

Les points de terminaison privés demandent un [nom de sous-domaine personnalisé pour Cognitive Services](../cognitive-services-custom-subdomains.md). Suivez les instructions ci-dessous pour en créer un pour votre ressource Speech.

> [!WARNING]
> Une ressource Speech avec un nom de domaine personnalisé activé utilise un autre moyen d’interagir avec Speech Services. Vous devrez peut-être adapter votre code d’application pour ces deux scénarios : avec [point de terminaison privé activé](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled) et [*sans* point de terminaison privé activé](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).
>
> Lorsque vous activez un nom de domaine personnalisé, l’opération n’est [pas réversible](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). La seule façon de revenir au [nom régional](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) consiste à créer une ressource Speech.
>
> Si votre ressource Speech comporte un grand nombre de modèles et de projets personnalisés associés créés avec [Speech Studio](https://speech.microsoft.com/), nous vous recommandons vivement d’essayer la configuration avec une ressource test avant de modifier la ressource utilisée en production.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Pour créer un nom de domaine personnalisé à l’aide du portail Azure, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com/) et connectez-vous à votre compte Azure.
1. Sélectionnez la ressource Speech requise.
1. Dans le groupe **Gestion des ressources** dans le volet gauche, sélectionnez **Réseau**.
1. Sous l’onglet **Pare-feux et réseaux virtuels**, sélectionnez **Générer un nom de domaine personnalisé**. Cela a pour effet d’ouvrir un nouveau panneau contenant des instructions pour créer un sous-domaine personnalisé unique pour votre ressource.
1. Dans le panneau **Générer un nom de domaine personnalisé**, entrez un nom de domaine personnalisé. Votre domaine personnalisé complet ressemble à ce qui suit : `https://{your custom name}.cognitiveservices.azure.com`. 
    
    N’oubliez pas : après avoir créé un nom de domaine personnalisé, vous ne pouvez _plus_ le modifier.
    
    Une fois que vous avez entré votre nom de domaine personnalisé, sélectionnez **Enregistrer**.
1. Quand l’opération est terminée, dans le groupe **Gestion des ressources**, sélectionnez **Clés et point de terminaison**. Confirmez que le nouveau nom de point de terminaison de votre ressource commence ainsi : `https://{your custom name}.cognitiveservices.azure.com`.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pour créer un nom de domaine personnalisé avec PowerShell, vérifiez que votre ordinateur dispose de PowerShell version 7.x ou ultérieure avec le module Azure PowerShell version 5.1.0 ou ultérieure. Pour voir les versions de ces outils, procédez comme suit :

1. Dans une fenêtre PowerShell, entrez :

    `$PSVersionTable`

    Confirmez que la valeur `PSVersion` est 7.x ou une version ultérieure. Pour mettre à niveau PowerShell, suivez les instructions données dans [Installation de plusieurs versions de PowerShell](/powershell/scripting/install/installing-powershell).

1. Dans une fenêtre PowerShell, entrez :

    `Get-Module -ListAvailable Az`

    Si rien ne s’affiche ou si la version du module Azure PowerShell affichée est antérieure à 5.1.0, suivez les instructions fournies dans [Installer le module Azure PowerShell](/powershell/azure/install-Az-ps) pour effectuer la mise à niveau.

Avant de poursuivre, exécutez `Connect-AzAccount` pour créer une connexion avec Azure.

## <a name="verify-that-a-custom-domain-name-is-available"></a>Vérifier la disponibilité d’un nom de domaine personnalisé

Vérifiez si le domaine personnalisé que vous souhaitez utiliser est disponible. Le code suivant confirme que le domaine est disponible en exécutant l’opération [Vérifier la disponibilité du domaine](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) dans l’API REST Cognitive Services.

> [!TIP]
> Le code suivant ne fonctionne *pas* dans Azure Cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Si le nom souhaité est disponible, vous obtenez une réponse similaire à celle-ci :
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Si le nom est déjà utilisé, vous obtenez la réponse suivante :
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Créer votre nom de domaine personnalisé

Pour activer un nom de domaine personnalisé pour la ressource Speech sélectionnée, utilisez l’applet de commande [Set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount).

> [!WARNING]
> Une fois que le code suivant s’est exécuté correctement, vous allez créer un nom de domaine personnalisé pour votre ressource Speech. N’oubliez pas que ce nom n’est *pas* modifiable.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Cette section nécessite la dernière version d’Azure CLI. Si vous utilisez Azure Cloud Shell, sachez que la version la plus récente est déjà installée.

## <a name="verify-that-the-custom-domain-name-is-available"></a>Vérifier la disponibilité du nom de domaine personnalisé

Vérifiez si le domaine personnalisé que vous souhaitez utiliser est disponible. Utilisez la méthode [Vérifier la disponibilité du domaine](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) de l’API REST Cognitive Services.

Copiez le bloc de code suivant, insérez le nom de domaine personnalisé de votre choix, puis enregistrez le tout dans le fichier `subdomain.json`.

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Copiez le fichier dans votre dossier actif ou chargez-le dans Azure Cloud Shell et exécutez la commande suivante. Remplacez `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` par l’identifiant de votre abonnement Azure.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Si le nom souhaité est disponible, vous obtenez une réponse similaire à celle-ci :
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Si le nom est déjà utilisé, vous obtenez la réponse suivante :
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-a-custom-domain-name"></a>Activer un nom de domaine personnalisé

Pour activer un nom de domaine personnalisé pour la ressource Speech sélectionnée, utilisez la commande [az cognitiveservices account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update).

Sélectionnez l’abonnement Azure qui contient la ressource Speech. Si votre compte Azure ne dispose que d’un seul abonnement actif, vous pouvez ignorer cette étape Remplacez `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` par l’identifiant de votre abonnement Azure.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Définissez le nom de domaine personnalisé pour la ressource sélectionnée. Remplacez les exemples de valeurs de paramètre par les valeurs réelles, puis exécutez la commande suivante.

> [!WARNING]
> Une fois que la commande suivante s’est exécutée correctement, vous allez créer un nom de domaine personnalisé pour votre ressource Speech. N’oubliez pas que ce nom n’est *pas* modifiable.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>Activer les points de terminaison privés

Nous vous recommandons d’utiliser la [zone DNS privée](../../dns/private-dns-overview.md) attachée au réseau virtuel avec les mises à jour nécessaires pour les points de terminaison privés. Vous créez une zone DNS privée par défaut pendant le processus de provisionnement. Si vous utilisez votre propre serveur DNS, vous devrez peut-être également modifier votre configuration DNS. 

Choisissez une stratégie DNS *avant* de provisionner des points de terminaison privés pour une ressource Speech en production. Testez également vos modifications DNS, en particulier si vous utilisez votre propre serveur DNS.

Consultez l’un des articles suivants pour créer des points de terminaison privés. Ces articles utilisent une application web en guise d’exemple de ressource à activer avec les points de terminaison privés.

- [Créer un point de terminaison privé au moyen du Portail Azure](../../private-link/create-private-endpoint-portal.md)
- [Créer un point de terminaison privé à l’aide d’Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Créer un point de terminaison privé à l’aide d’Azure CLI](../../private-link/create-private-endpoint-cli.md)

Utilisez ces paramètres à la place des paramètres de l’article de votre choix :

| Paramètre             | Valeur                                    |
|---------------------|------------------------------------------|
| Type de ressource       | **Microsoft.CognitiveServices/accounts** |
| Resource            | **\<your-speech-resource-name>**         |
| Sous-ressource cible | **account**                              |

**DNS pour points de terminaison privés :** familiarisez-vous avec les principes généraux en lien avec [DNS pour points de terminaison privés dans des ressources Cognitive Services](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Vérifiez ensuite que votre configuration DNS fonctionne correctement en effectuant les vérifications décrites dans les sections suivantes.

### <a name="resolve-dns-from-the-virtual-network"></a>Résolution DNS à partir du réseau virtuel

Cette vérification est *obligatoire*.

Effectuez ces étapes pour tester l’entrée DNS personnalisée à partir de votre réseau virtuel :

1. Connectez-vous à une machine virtuelle située dans le réseau virtuel auquel vous avez attaché votre point de terminaison privé. 
1. Ouvrez une invite de commandes Windows ou un shell Bash, exécutez la commande `nslookup` et vérifiez qu’elle résout correctement le nom de domaine personnalisé de votre ressource.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. Confirmez que l’adresse IP correspond à l’adresse IP de votre point de terminaison privé.

### <a name="resolve-dns-from-other-networks"></a>Résolution DNS à partir d’autres réseaux

Effectuez cette vérification uniquement si vous avez activé l’option **Tous les réseaux** ou l’option d’accès **Points de terminaison privés et réseaux sélectionnés** dans la section **Réseau** de votre ressource. 

Si vous envisagez d’accéder à la ressource en utilisant uniquement un point de terminaison privé, vous pouvez ignorer cette section.

1. Connectez-vous à un ordinateur attaché à un réseau qui est autorisé à accéder à la ressource.
2. Ouvrez une invite de commandes Windows ou un shell Bash, exécutez la commande `nslookup` et vérifiez qu’elle résout correctement le nom de domaine personnalisé de votre ressource.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

> [!NOTE]
> L’adresse IP résolue pointe vers un point de terminaison proxy de réseau virtuel qui est utilisé pour distribuer le trafic réseau vers le point de terminaison privé pour la ressource Cognitive Services. Le comportement est différent pour une ressource avec un nom de domaine personnalisé activé, mais *sans* point de terminaison privé. Consultez [cette section](#dns-configuration) pour plus d’informations.

## <a name="adjust-existing-applications-and-solutions"></a>Ajuster les solutions et applications existantes

Une ressource Speech avec un domaine personnalisé activé utilise un autre moyen pour interagir avec Speech Services. Cela est vrai pour une ressource Speech avec un domaine personnalisé activé avec et sans point de terminaison privé. Les informations contenues dans cette section s’appliquent aux deux scénarios.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>Utiliser une ressource Speech avec un nom de domaine personnalisé et un point de terminaison privé activé

Une ressource Speech qui a un nom de domaine personnalisé et un point de terminaison privé activé utilise un autre moyen d’interagir avec Speech Services. Cette section explique comment utiliser une telle ressource avec les API REST Speech Services et le [SDK Speech](speech-sdk.md).

> [!NOTE]
> Une ressource Speech sans points de terminaison privés activés mais avec un nom de domaine personnalisé activé utilise aussi une méthode spéciale d’interaction avec Speech Services. Cette méthode diffère du scénario d’une ressource Speech avec des points de terminaison privés activés. Si vous avez une telle ressource (par exemple, vous aviez une ressource avec des points de terminaison privés, que vous avez décidé de supprimer par la suite), consultez la section [Utiliser une ressource Speech avec un nom de domaine personnalisé mais sans points de terminaison privés](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>Ressource Speech avec un nom de domaine personnalisé et un point de terminaison privé : Utilisation avec les API REST

Dans cette section, nous allons utiliser `my-private-link-speech.cognitiveservices.azure.com` comme exemple de nom DNS de ressource Speech (domaine personnalisé).

La solution Speech Services fournit des API REST pour la [reconnaissance vocale](rest-speech-to-text.md) et la [synthèse vocale](rest-text-to-speech.md). Pour le scénario avec des points de terminaison privés activés, prenez en compte les informations suivantes.

La reconnaissance vocale a deux API REST. Chaque API sert un objectif différent, utilise des points de terminaison différents et nécessite une approche différente quand vous l’utilisez dans un scénario avec des points de terminaison privés activés.

Les API REST de reconnaissance vocale sont les suivantes :
- L’[API REST de reconnaissance vocale v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30), qui est utilisée pour la [transcription Batch](batch-transcription.md) et [Custom Speech](custom-speech-overview.md). La version v3.0 [succède à la version v2.0](./migrate-v2-to-v3.md)
- [L’API REST de reconnaissance vocale pour audio court](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio), qui est utilisée pour la transcription en ligne 

L’utilisation de l’API REST de reconnaissance vocale pour audio court et de l’API REST de synthèse vocale dans le scénario avec des points de terminaison privés est identique. Elle est équivalente au [cas d’usage du SDK Speech](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) décrit plus loin dans cet article. 

L’API REST de reconnaissance vocale v3.0 utilise un autre ensemble de points de terminaison ; elle nécessite donc une approche différente pour le scénario avec des points de terminaison privés activés.

Les sous-sections suivantes décrivent les deux cas.

##### <a name="speech-to-text-rest-api-v30"></a>API REST de reconnaissance vocale v3.0

En général, les ressources Speech utilisent des [points de terminaison régionaux Cognitive Services](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) pour communiquer avec l’[API REST de reconnaissance vocale v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Ces ressources ont le format d’affectation des noms suivant : <p/>`{region}.api.cognitive.microsoft.com`.

Voici un exemple d’URL de demande :

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```

> [!NOTE]
> Consultez [cet article](sovereign-clouds.md) pour les points de terminaison Azure Government et Azure Chine.

Après l’activation d’un domaine personnalisé pour une ressource Speech (nécessaire pour les points de terminaison privés), cette ressource utilise le modèle de nom DNS suivant pour le point de terminaison d’API REST de base : <p/>`{your custom name}.cognitiveservices.azure.com`.

Cela signifie que, dans notre exemple, le nom du point de terminaison d’API REST sera : <p/>`my-private-link-speech.cognitiveservices.azure.com`.

Et l’exemple d’URL de requête doit être converti en :
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Cette URL doit être accessible à partir du réseau virtuel avec le point de terminaison privé attaché (et la [résolution DNS correcte](#resolve-dns-from-the-virtual-network)).

Une fois que vous avez activé un nom de domaine personnalisé pour une ressource Speech, vous remplacez généralement le nom d’hôte de toutes les URL de requête par le nouveau nom d’hôte du domaine personnalisé. Toutes les autres parties de la requête (comme le chemin `/speechtotext/v3.0/transcriptions` dans l’exemple ci-dessus) restent les mêmes.

> [!TIP]
> Certains clients développent des applications qui utilisent la partie région du nom DNS du point de terminaison régional (par exemple, pour envoyer la requête à la ressource Speech déployée dans la région Azure en question).
>
> Un domaine personnalisé pour une ressource Speech ne contient *aucune* information sur la région où la ressource est déployée. C’est pourquoi la logique d’application décrite précédemment ne fonctionne *pas* et doit être modifiée.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST de reconnaissance vocale pour audio court et API REST de synthèse vocale

L’[API REST de reconnaissance vocale pour audio court](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) et l’[API REST de synthèse vocale](rest-text-to-speech.md) utilisent deux types de points de terminaison :
- [points de terminaison régionaux Cognitive Services](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) pour communiquer avec l’API REST Cognitive Services afin d’obtenir un jeton d’autorisation ;
- points de terminaison spéciaux pour toutes les autres opérations.

> [!NOTE]
> Consultez [cet article](sovereign-clouds.md) pour les points de terminaison Azure Government et Azure Chine.

Les points de terminaison spéciaux ainsi que la façon dont leur URL doit être transformée pour une ressource Speech avec des points de terminaison privés activés sont décrits en détail dans [cette sous-section](#construct-endpoint-url) sur l’utilisation avec le SDK Speech. Le principe décrit pour le SDK s’applique également à l’API REST de reconnaissance vocale pour audio court et à l’API REST de synthèse vocale.

Consultez la sous-section mentionnée dans le paragraphe précédent et examinez l’exemple suivant L’exemple décrit l’API REST de synthèse vocale. L’utilisation de l’API REST de reconnaissance vocale pour audio court est strictement équivalente.

> [!NOTE]
> Lorsque vous utilisez l’API REST de reconnaissance vocale pour audio court et l’API REST de synthèse vocale dans des scénarios avec des points de terminaison privés, utilisez une clé d’abonnement transmise via l’en-tête `Ocp-Apim-Subscription-Key`. (Voir [API REST de reconnaissance vocale pour audio court](rest-speech-to-text.md#request-headers) et [API REST de synthèse vocale](rest-text-to-speech.md#request-headers) pour plus de détails)
>
> L’utilisation d’un jeton d’autorisation et sa transmission au point de terminaison spécial via l’en-tête `Authorization` fonctionnent *uniquement* si vous avez activé l’option d’accès **Tous les réseaux** dans la section **Mise en réseau** de votre ressource Speech. Dans d’autres cas, vous obtiendrez une erreur `Forbidden` ou `BadRequest` lors de la tentative d’obtention d’un jeton d’autorisation.

**Exemple d’utilisation de l’API REST de synthèse vocale**

Nous allons utiliser la région Europe Ouest comme exemple de région Azure et `my-private-link-speech.cognitiveservices.azure.com` comme exemple de nom DNS de ressource Speech (domaine personnalisé). Le nom de domaine personnalisé `my-private-link-speech.cognitiveservices.azure.com` dans notre exemple appartient à la ressource Speech créée dans la région Europe Ouest.

Pour obtenir la liste des voix prises en charge dans la région, exécutez la requête suivante :

```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
Pour plus d’informations, consultez la [documentation de l’API REST de synthèse vocale](rest-text-to-speech.md).

Pour la ressource Speech avec des points de terminaison privés activés, l’URL des points de terminaison pour la même opération doit être modifiée. La même requête se présente comme suit :

```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
Consultez une explication détaillée dans la sous-section [Construire l’URL du point de terminaison](#construct-endpoint-url) pour le SDK Speech.

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>Ressource Speech avec un nom de domaine personnalisé et un point de terminaison privé : Utilisation avec le SDK Speech

L’utilisation du SDK Speech pour des ressources Speech avec des noms de domaine personnalisés et des points de terminaison privés activés nécessite une revue et probablement des modifications de votre code d’application.

Dans cette section, nous allons utiliser `my-private-link-speech.cognitiveservices.azure.com` comme exemple de nom DNS de ressource Speech (domaine personnalisé).

##### <a name="construct-endpoint-url"></a>Construire l’URL du point de terminaison

Généralement, dans les scénarios de SDK (ainsi que dans les scénarios d’API REST de reconnaissance vocale pour audio court et d’API REST de synthèse vocale), les ressources Speech utilisent les points de terminaison régionaux dédiés pour différentes offres de service. Le format de nom DNS pour ces points de terminaison est le suivant :

`{region}.{speech service offering}.speech.microsoft.com`

Voici un exemple de nom DNS :

`westeurope.stt.speech.microsoft.com`

Toutes les valeurs possibles pour la région (premier élément du nom DNS) sont listées dans [Régions prises en charge pour le service Speech](regions.md). (Consultez [cet article](sovereign-clouds.md) pour les points de terminaison Azure Government et Azure Chine.) Le tableau suivant présente les valeurs possibles pour l’offre Speech Services (deuxième élément du nom DNS) :

| Valeur du nom DNS | Offre Speech Services                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Commandes personnalisées](custom-commands.md)                       |
| `convai`       | [Transcription de conversation](conversation-transcription.md) |
| `s2s`          | [Traduction vocale](speech-translation.md)                 |
| `stt`          | [Reconnaissance vocale](speech-to-text.md)                         |
| `tts`          | [Synthèse vocale](text-to-speech.md)                         |
| `voice`        | [Custom Voice](how-to-custom-voice.md)                      |

Ainsi, l’exemple précédent (`westeurope.stt.speech.microsoft.com`) correspond au point de terminaison de reconnaissance vocale dans la région Europe Ouest.

Les points de terminaison privés activés communiquent avec Speech Services via un proxy spécial. Pour cette raison, *vous devez modifier les URL de connexion des points de terminaison*. 

Une URL de point de terminaison « standard » ressemble à ceci : <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Une URL de point de terminaison privé ressemble à ceci : <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Exemple 1.** Une application communique par le biais de l’URL suivante (reconnaissance vocale avec le modèle de base pour l’anglais des États-Unis dans la région Europe Ouest) :

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Pour l’utiliser dans le scénario avec des points de terminaison privés activés quand le nom de domaine personnalisé de la ressource Speech est `my-private-link-speech.cognitiveservices.azure.com`, vous devez modifier cette URL comme suit :

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Notez les informations suivantes :

- Le nom d’hôte `westeurope.stt.speech.microsoft.com` est remplacé par le nom d’hôte du domaine personnalisé `my-private-link-speech.cognitiveservices.azure.com`.
- Le deuxième élément du nom DNS d’origine (`stt`) devient le premier élément du chemin de l’URL et précède le chemin d’origine, de sorte que l’URL d’origine `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` devient `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

**Exemple 2.** Une application utilise l’URL suivante pour synthétiser la parole dans la région Europe Ouest en utilisant un modèle de voix personnalisé :
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

L’URL équivalente suivante utilise un point de terminaison privé activé, où le nom de domaine personnalisé de la ressource Speech est `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Le même principe que dans l’Exemple 1 est appliqué mais, cette fois, l’élément clé est `voice`.

##### <a name="modifying-applications"></a>Modification d’applications

Pour modifier votre code, procédez comme suit :

1. Déterminez l’URL du point de terminaison de l’application :

   - [Activez la journalisation pour votre application](how-to-use-logging.md), puis exécutez-la pour générer le journal d’activité.
   - Dans le fichier journal, recherchez `SPEECH-ConnectionUrl`. Dans les lignes correspondantes, le paramètre `value` contient l’URL complète qui a été utilisée par votre application pour accéder à Speech Services.

   Exemple :

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   Par conséquent, l’URL que l’application a utilisée dans cet exemple est la suivante  :

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. Créez une instance de `SpeechConfig` en utilisant une URL de point de terminaison complète :

   1. Modifiez le point de terminaison que vous venez de déterminer, comme cela est décrit dans la section [Construire l’URL du point de terminaison](#construct-endpoint-url), plus haut dans cet article.

   1. Modifiez la manière dont vous créez l’instance de `SpeechConfig`. Le plus souvent, votre application utilisera quelque chose ressemblant à ceci :
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      Cela ne fonctionne pas pour une ressource Speech avec un point de terminaison privé activé en raison des changements de nom d’hôte et d’URL que nous avons décrits dans les sections précédentes. Si vous essayez d’exécuter votre application existante sans faire aucune modification et en utilisant la clé d’une ressource avec un point de terminaison privé activé, vous obtenez une erreur d’authentification (401).

      Pour que cela fonctionne, modifiez la manière dont vous instanciez la classe `SpeechConfig` et utilisez l’initialisation « à partir d’un point de terminaison »/« avec un point de terminaison ». Supposons que les deux variables suivantes sont définies :
      - `subscriptionKey` contient la clé de la ressource Speech avec un point de terminaison privé activé.
      - `endPoint` contient l’URL *modifiée* complète du point de terminaison (utilisant le type requis par le langage de programmation correspondant). Dans notre exemple, cette variable devrait contenir ceci :
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      Créez une instance `SpeechConfig` :
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> Les paramètres de requête spécifiés dans l’URI de point de terminaison ne sont pas modifiés, même s’ils sont définis par d’autres API. Par exemple, si la langue de reconnaissance est définie dans l’URI en tant que paramètre de requête `language=en-US` et qu’elle est également définie sur `ru-RU` dans la propriété correspondante, le paramètre de langue dans l’URI est utilisé. La langue effective est alors `en-US`.
>
> Les paramètres définis dans l’URI de point de terminaison sont toujours prioritaires. Les autres API peuvent remplacer uniquement les paramètres qui ne sont pas définis dans l’URI de point de terminaison.

Après cette modification, votre application fonctionnera correctement avec des ressources Speech avec des points de terminaison privés activés. Nous travaillons sur une prise en charge plus fluide des scénarios avec des points de terminaison privés.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>Utiliser une ressource Speech avec un nom de domaine personnalisé mais sans points de terminaison privés

Dans cet article, nous avons signalé à plusieurs reprises que l’activation d’un domaine personnalisé pour une ressource Speech était une opération *irréversible*. Une telle ressource utilise une autre méthode pour communiquer avec Speech Services, par rapport aux ressources qui utilisent des [noms de points de terminaison régionaux](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

Cette section explique comment utiliser une ressource Speech avec un nom de domaine personnalisé activé mais *sans* points de terminaison privés à l’aide des API REST Speech Services et du [SDK Speech](speech-sdk.md). Par exemple, cela pourrait être une ressource qui a été utilisée dans un scénario avec des points de terminaison privés, mais où tous les points de terminaison privés ont été supprimés par la suite.

#### <a name="dns-configuration"></a>Configuration DNS

Rappelez-vous qu’un nom DNS de domaine personnalisé de la ressource Speech avec point de terminaison privé est [résolu à partir de réseaux publics](#resolve-dns-from-other-networks). Dans ce cas, l’adresse IP résolue pointe vers un point de terminaison proxy d’un réseau virtuel. Ce point de terminaison est utilisé pour distribuer le trafic réseau à la ressource Cognitive Services avec des points de terminaison privés activés.

Toutefois, quand *tous* les points de terminaison privés de la ressource sont supprimés (ou juste après l’activation du nom de domaine personnalisé), l’enregistrement CNAME de la ressource Speech est reprovisionné. Il pointe désormais vers l’adresse IP du [point de terminaison régional Cognitive Services](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) correspondant.

La sortie de la commande `nslookup` doit donc ressembler à ceci :
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Comparez-la avec la sortie de [cette section](#resolve-dns-from-other-networks).

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>Ressource Speech avec un nom de domaine personnalisé mais sans points de terminaison privés : Utilisation avec les API REST

##### <a name="speech-to-text-rest-api-v30"></a>API REST de reconnaissance vocale v3.0

L’utilisation de l’API REST de reconnaissance vocale v3.0 est strictement équivalente à celle des [ressources Speech avec des points de terminaison privés activés](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST de reconnaissance vocale pour audio court et API REST de synthèse vocale

Dans ce cas, l’utilisation de l’API REST de reconnaissance vocale pour audio court et l’utilisation de l’API REST de synthèse vocale ne diffèrent pas du cas général, à une exception près. (Voir la remarque suivante.) Vous devez utiliser les deux API comme cela est décrit dans la documentation de l’[API REST de reconnaissance vocale pour audio court](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) et celle de l’[API REST de synthèse vocale](rest-text-to-speech.md).

> [!NOTE]
> Lorsque vous utilisez l’API REST de reconnaissance vocale pour audio court et l’API REST de synthèse vocale dans des scénarios de domaine personnalisé, utilisez une clé d’abonnement transmise via l’en-tête `Ocp-Apim-Subscription-Key`. (Voir [API REST de reconnaissance vocale pour audio court](rest-speech-to-text.md#request-headers) et [API REST de synthèse vocale](rest-text-to-speech.md#request-headers) pour plus de détails)
>
> L’utilisation d’un jeton d’autorisation et sa transmission au point de terminaison spécial via l’en-tête `Authorization` fonctionnent *uniquement* si vous avez activé l’option d’accès **Tous les réseaux** dans la section **Mise en réseau** de votre ressource Speech. Dans d’autres cas, vous obtiendrez une erreur `Forbidden` ou `BadRequest` lors de la tentative d’obtention d’un jeton d’autorisation.

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>Ressource Speech avec un nom de domaine personnalisé mais sans points de terminaison privés : Utilisation avec le SDK Speech

L’utilisation du SDK Speech avec des ressources Speech avec un domaine personnalisé activé *sans* points de terminaison privés équivaut au cas général, comme décrit dans la [documentation du SDK Speech](speech-sdk.md).

Si vous avez modifié votre code pour l’utiliser avec une [ressource Speech avec des points de terminaison privés activés](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk), tenez compte des éléments suivants.

Dans la section sur les [ressources Speech avec des points de terminaison privés activés](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk), nous avons expliqué comment déterminer l’URL de point de terminaison, comment la modifier et comment la rendre opérationnelle en initialisant l’instance de la classe `SpeechConfig` « à partir d’un point de terminaison »/« avec un point de terminaison ».

Toutefois, si vous tentez d’exécuter la même application après avoir supprimé tous les points de terminaison privés (en attendant le temps nécessaire au reprovisionnement de l’enregistrement DNS correspondant), vous obtenez une erreur de service interne (404). La raison à cela est que l’[enregistrement DNS](#dns-configuration) pointe désormais vers le point de terminaison Cognitive Services régional au lieu du proxy de réseau virtuel, et que les chemins d’URL comme `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` y sont introuvables.

Vous devez restaurer votre application à l’instanciation standard de `SpeechConfig` dans le style du code suivant :

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

## <a name="pricing"></a>Tarifs

Pour plus d’informations sur les tarifs, consultez [Tarification Liaison privée Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>En savoir plus

* [Azure Private Link](../../private-link/private-link-overview.md)
* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk.md)
* [API REST de reconnaissance vocale](rest-speech-to-text.md)
* [API REST de synthèse vocale](rest-text-to-speech.md)