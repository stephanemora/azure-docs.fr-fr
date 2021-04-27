---
title: Portail des développeurs - Forum aux questions
titleSuffix: Azure API Management
description: Forum aux questions sur le portail des développeurs dans Gestion des API. Le portail des développeurs est un site web personnalisable, où les consommateurs d’API peuvent explorer vos API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: b4413bc53cf5c86c311d049046db790582737ca4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741095"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>Portail des développeurs Gestion des API - Forum aux questions

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>Que se passe-t-il si j’ai besoin de fonctionnalités non prises en charge dans le portail ?

Vous pouvez ouvrir une demande de fonctionnalité dans le [référentiel GitHub](https://github.com/Azure/api-management-developer-portal) ou [implémenter vous-même la fonctionnalité manquante](developer-portal-implement-widgets.md). En savoir plus sur l’[ extensibilité ](api-management-howto-developer-portal.md#managed-vs-self-hosted) du portail des développeurs.


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Puis-je avoir plusieurs portails des développeurs dans un même service Gestion des API ?

Vous pouvez avoir un portail géré et plusieurs portails auto-hébergés. Le contenu de tous les portails étant stocké dans le même service Gestion des API, ils sont identiques. Si vous souhaitez différencier leur apparence et leurs fonctionnalités, vous pouvez les auto-héberger en utilisant vos propres widgets personnalisés pour personnaliser dynamiquement les pages à l’exécution, par exemple, en fonction de l’URL.

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Le portail prend-il en charge les modèles Azure Resource Manager ? Est-il compatible avec le kit de ressources DevOps Gestion des API ?

Non.

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Le contenu du portail est-il enregistré avec la fonctionnalité de sauvegarde/restauration de la Gestion des API ?

Non.

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Faut-il activer une connectivité de réseau virtuel supplémentaire pour les dépendances du portail managé ?

Dans la plupart des cas, non.

Si votre service de gestion des API se trouve dans un réseau virtuel interne, votre portail des développeurs est accessible uniquement à partir de ce réseau. Le nom d’hôte du point de terminaison de gestion doit être résolu en adresse IP virtuelle interne du service à partir de l’ordinateur utilisé pour accéder à l’interface d’administration du portail. Veillez à ce que le point de terminaison de gestion soit inscrit dans le DNS. En cas d’erreur de configuration, l’erreur suivante apparaît : `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

Si votre service Gestion des API se trouve dans un réseau virtuel interne, et que vous y accédez par le biais d’Application Gateway à partir d’Internet, veillez à activer la connectivité au portail des développeurs et aux points de terminaison de gestion de Gestion des API. Vous devrez peut-être désactiver les règles de pare-feu d’applications web. Pour plus d’informations, consultez [cet article de la documentation](api-management-howto-integrate-internal-vnet-appgateway.md).

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>J’ai attribué un domaine de Gestion des API personnalisé et le portail publié ne fonctionne pas

Après avoir mis à jour le domaine, vous devez [republier le portail](api-management-howto-developer-portal-customize.md#publish) pour que les modifications prennent effet.

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>J’ai ajouté un fournisseur d’identité mais il n’apparaît pas dans le portail

Après avoir configuré un fournisseur d’identité (par exemple, Azure AD, Azure AD B2C), vous devez [republier le portail](api-management-howto-developer-portal-customize.md#publish) pour que les modifications prennent effet. Assurez-vous que les pages du portail des développeurs incluent le widget de boutons OAuth.

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>J’ai configuré la délégation mais le portail ne l’utilise pas

Après avoir configuré la délégation, vous devez [republier le portail](api-management-howto-developer-portal-customize.md#publish) pour que les modifications prennent effet.

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Les autres modifications apportées à la configuration de la gestion des API n’ont pas été propagées dans le portail des développeurs

La plupart des modifications de configuration (par exemple, réseau virtuel, connexion et conditions de produit) nécessitent une [republication du portal](api-management-howto-developer-portal-customize.md#publish).

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> J’obtiens une erreur CORS lorsque j’utilise la console interactive

La console interactive effectue une requête d’API côté client à partir du navigateur. Résolvez le problème CORS en ajoutant une [stratégie CORS ](api-management-cross-domain-policies.md#CORS)sur vos API.

Vous pouvez vérifier l’état de la stratégie CORS dans la section **Vue d’ensemble du portail** de votre service Gestion des API dans le Portail Azure. Une zone d’avertissement indique une stratégie absente ou mal configurée.

> [!NOTE]
> 
> Une seule stratégie CORS est exécutée. Si vous avez spécifié plusieurs stratégies CORS (par exemple, au niveau de l’API et au niveau de toutes les API), votre console interactive pourrait ne pas fonctionner comme prévu.

![Capture d’écran montrant où vous pouvez vérifier l’état de votre stratégie CORS.](media/developer-portal-faq/cors-azure-portal.png)

Appliquez automatiquement la stratégie CORS en cliquant sur le bouton **Activer CORS**.

Vous pouvez également activer CORS manuellement.

1. Sélectionnez le lien **L’appliquer manuellement au niveau global** pour afficher le code de stratégie généré.
2. Accédez à **Toutes les API** dans la section **API** de votre service Gestion des API dans le Portail Azure.
3. Sélectionnez l’icône **</>** dans la section **Traitement entrant**.
4. Insérez la stratégie dans la section **<inbound>** du fichier XML. Assurez-vous que la valeur **<origin>** correspond au domaine de votre portail des développeurs.

> [!NOTE]
> 
> Si vous appliquez la stratégie CORS dans l’étendue du produit, au lieu de l’étendue de l’API, et que votre API utilise l’authentification par clé d’abonnement via un en-tête, votre console ne fonctionnera pas.
>
> Le navigateur émet automatiquement une requête HTTP `OPTIONS`, qui ne contient pas d’en-tête avec la clé d’abonnement. La clé d’abonnement étant absente, la Gestion des API ne peut pas associer l’appel `OPTIONS` à un produit et, par conséquent, ne peut pas appliquer la stratégie CORS.
>
> Pour contourner le problème, vous pouvez transmettre la clé d’abonnement dans un paramètre de requête.

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>Qu’est-ce que la fonctionnalité de proxy CORS et quand dois-je l’utiliser ?

Sélectionnez l’option **Utiliser le proxy CORS** dans la configuration du widget de détails d’opération d’API pour acheminer les appels d’API de la console interactive via le serveur principal du portail dans votre service Gestion des API. Dans cette configuration, vous n’avez plus besoin d’appliquer une stratégie CORS pour vos API, et la connectivité au point de terminaison de la passerelle à partir de l’ordinateur local n’est pas nécessaire. Si les API sont exposées par le biais d’une passerelle auto-hébergée ou si votre service se trouve dans un réseau virtuel, la connectivité du service principal de la Gestion des API à la passerelle est requise. Si vous utilisez le portail auto-hébergé, spécifiez le point de terminaison principal du portail à l’aide de l’option `backendUrl` dans les fichiers de configuration. Dans le cas contraire, le portail auto-hébergé n’est pas conscient de l’emplacement du service principal.

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Quelles sont les autorisations requises pour modifier le portail des développeurs ?

Si l’erreur `Oops. Something went wrong. Please try again later.` apparaît lorsque vous ouvrez le portail en mode d’administration, vous ne disposez peut-être pas des autorisations requises (Azure RBAC).

Les portails hérités exigeaient l’autorisation `Microsoft.ApiManagement/service/getssotoken/action` sur l’étendue du service (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) pour que l’administrateur d’utilisateurs puisse y accéder. Le nouveau portail exige l’autorisation `Microsoft.ApiManagement/service/users/token/action` sur l’étendue `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`.

Vous pouvez utiliser le script PowerShell suivant pour créer un rôle avec l’autorisation requise. Veillez à changer le paramètre `<subscription-id>`. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Une fois le rôle créé, il peut être attribué à n’importe quel utilisateur de la section **Contrôle d’accès (IAM)** du portail Azure. Le fait d’attribuer ce rôle à un utilisateur a pour effet d’attribuer l’autorisation sur l’étendue du service. L’utilisateur pourra générer des jetons SAS au nom de *n’importe quel* utilisateur du service. Au minimum, ce rôle doit être attribué à l’administrateur du service. La commande PowerShell suivante montre comment attribuer le rôle à un utilisateur `user1` sur l’étendue la plus basse pour éviter d’octroyer des autorisations inutiles à l’utilisateur : 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Une fois les autorisations octroyées à un utilisateur, celui-ci doit se déconnecter et se reconnecter au portail Azure pour que les nouvelles autorisations prennent effet.

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>L’erreur `Unable to start the portal. See if settings are specified correctly (...)` apparaît

Cette erreur s’affiche lorsqu’un appel `GET` à `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` échoue. L’appel est émis à partir du navigateur par l’interface d’administration du portail.

Si votre service de Gestion des API est un réseau virtuel, reportez-vous à la [question sur la connectivité du réseau virtuel](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies).

L’échec de l’appel peut également être dû au fait qu’un certificat TLS/SSL attribué à un domaine personnalisé ne soit pas approuvé par le navigateur. Pour remédier à ce problème, vous pouvez supprimer le domaine personnalisé de point de terminaison de gestion. La Gestion des API reviendra au point de terminaison par défaut avec un certificat approuvé.

## <a name="whats-the-browser-support-for-the-portal"></a>Quelle est la prise en charge du navigateur pour le portail ?

| Browser                     | Prise en charge       |
|-----------------------------|-----------------|
| Apple Safari                | Oui<sup>1</sup> |
| Google Chrome               | Oui<sup>1</sup> |
| Microsoft Edge              | Oui<sup>1</sup> |
| Microsoft Internet Explorer | Non              |
| Mozilla Firefox             | Oui<sup>1</sup> |

 <small><sup>1</sup> Pris en charge dans les deux dernières versions de production.</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>Le développement local de mon portail auto-hébergé ne fonctionne plus

Si votre version locale du portail des développeurs ne peut pas enregistrer ou récupérer des informations à partir du compte de stockage ou de l’instance Gestion des API, les jetons SAS peuvent avoir expiré. Vous pouvez résoudre ce problème en générant de nouveaux jetons. Pour obtenir des instructions, reportez-vous au didacticiel sur [Auto-hébergement du portail des développeurs](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings).

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>Comment puis-je supprimer le contenu du portail des développeurs configuré dans mon service Gestion des API ?

Fournissez les paramètres requis dans le script `scripts.v3/cleanup.bat` dans le [référentiel GitHub](https://github.com/Azure/api-management-developer-portal) du portail des développeurs, puis exécutez le script

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>Comment activer l’authentification unique (SSO) sur le portail des développeurs auto-hébergé ?

Parmi d’autres méthodes d’authentification, le portail des développeurs prend en charge l’authentification unique (SSO). Pour vous authentifier avec cette méthode, vous devez effectuer un appel à `/signin-sso` avec le jeton dans le paramètre de requête :

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>Générer des jetons d’utilisateur
Vous pouvez générer des *jetons spécifiques à l’utilisateur* (y compris des jetons d’administration) à l’aide de l’opération [Obtenir un jeton d’accès partagé](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) de l’[API REST Gestion des API](/rest/api/apimanagement/apimanagementrest/api-management-rest).

> [!NOTE]
> Le jeton doit être encodé en URL.

## <a name="next-steps"></a>Étapes suivantes

Découvrez le nouveau portail des développeurs :

- [Accéder au portail managé des développeurs et le personnaliser](api-management-howto-developer-portal-customize.md)
- [Configurer la version auto-hébergée du portail](developer-portal-self-host.md)
- [Implémenter votre propre widget](developer-portal-implement-widgets.md)

Parcourez d’autres ressources :

- [Référentiel GitHub avec le code source](https://github.com/Azure/api-management-developer-portal)

