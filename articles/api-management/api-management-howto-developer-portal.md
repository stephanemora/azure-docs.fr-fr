---
title: Vue d’ensemble du portail des développeurs Gestion des API Azure
titleSuffix: Azure API Management
description: Découvrez le portail des développeurs dans la Gestion des API Azure. Le portail des développeurs est la plateforme sur laquelle les consommateurs peuvent trouver vos API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/28/2020
ms.author: apimpm
ms.openlocfilehash: 3642b95f5bd6d0207508ca85f1d22ce20b44eae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715454"
---
# <a name="azure-api-management-developer-portal-overview"></a>Vue d’ensemble du portail des développeurs Gestion des API Azure

Le portail des développeurs est un site web généré automatiquement et entièrement personnalisable avec la documentation de vos API. C’est là que les consommateurs d’API peuvent découvrir vos API, apprendre à les utiliser, y demander l’accès et les essayer.

Cet article décrit les différences entre la version auto-hébergée et la version managée du portail des développeurs dans la Gestion des API. Il explique également son architecture et répond aux questions fréquentes.

![Portail des développeurs Gestion des API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> <a name="migrate-from-legacy"></a> Le nouveau portail des développeurs est incompatible avec l’ancien ; la migration automatisée n’est pas possible. Vous devez recréer manuellement le contenu (pages, texte, fichiers multimédias) et personnaliser l’apparence du nouveau portail. Pour obtenir de l’aide, consultez le [tutoriel sur le portail des développeurs](api-management-howto-developer-portal-customize.md).

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a> Version managée et version auto-hébergée

Vous pouvez générer votre portail des développeurs de deux manières :

- **Version managée**, via la modification et la personnalisation du portail : intégrée dans votre instance de Gestion des API et accessible via l’URL `<your-api-management-instance-name>.developer.azure-api.net` ; Pour savoir comment accéder au portail managé et le personnaliser, voir [cet article de documentation](api-management-howto-developer-portal-customize.md).
- **version auto-hébergée**, via le déploiement et l’auto-hébergement de votre portail en dehors d’une instance de gestion des API. Cette approche vous permet de modifier le code base du portail et d’étendre les principales fonctionnalités fournies, par exemple d’implémenter des widgets personnalisés pour les intégrations à des systèmes tiers. Dans ce scénario, vous êtes le chargé de maintenance du portail et vous êtes responsable de la mise à niveau du portail vers la toute dernière version. Pour obtenir plus de détails et d’instructions, reportez-vous au [dépôt GitHub avec le code source du portail][1] et au [tutoriel sur l’implémentation d’un widget][3]. Le [tutoriel de la version managée](api-management-howto-developer-portal-customize.md) décrit le panneau d’administration du portail, commun aux versions auto-hébergée et managée.

## <a name="portal-architectural-concepts"></a>Concepts architecturaux du portail

Les composants du portail se divisent en deux catégories logiques : le *code* et le *contenu*.

Le *code*, conservé dans le [référentiel GitHub][1], comprend les éléments suivants :

- les widgets, qui représentent des éléments visuels et associent du code HTML, du code JavaScript, des possibilités de stylisation, des paramètres et un mappage du contenu (exemples : image, paragraphe de texte, formulaire, liste d’API, etc.) ;
- les définitions de style, qui spécifient les styles possibles des widgets ;
- le moteur, écrit en JavaScript, qui génère des pages web statiques à partir du contenu du portail ;
- l’éditeur visuel, qui offre une expérience de personnalisation et de création au sein du navigateur.

Le *contenu* est divisé en deux sous-catégories : le *contenu du portail* et le *contenu de la Gestion des API*.

Le *contenu du portail*, propre au portail, comprend les éléments suivants :

- les pages (exemples : page de destination, tutoriels sur les API, billets de blog) ;
- les contenus multimédias (exemples : images, animations et autres contenus basés sur des fichiers) ;
- les dispositions : modèles qui sont confrontés avec une URL et définissent le mode d’affichage des pages ;
- les styles : valeurs des définitions de style (exemples : polices, couleurs, bordures) ;
- les paramètres : configuration (exemples : favicon, métadonnées du site web).

Le *contenu du portail*, à l’exception des contenus multimédias, est exprimé sous la forme de documents JSON.

Le *contenu Gestion des API* comprend des entités telles que les API, les Opérations, les Produits et les Abonnements.

Le portail est basé sur une fourche adaptée du [framework Paperbits](https://paperbits.io/). La fonctionnalité Paperbits d’origine a été étendue de façon à fournir des widgets propres à la Gestion des API (par exemple, une liste d’API ou une liste de Produits) et un connecteur vers le service Gestion des API permettant d’enregistrer et de récupérer du contenu.

## <a name="frequently-asked-questions"></a><a name="faq"></a> Forum aux questions

Dans cette section, nous répondons aux questions courantes d’ordre général sur le portail des développeurs. Pour toute question spécifique à la version auto-hébergée, reportez-vous à la [section wiki du référentiel GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Comment migrer à partir de la préversion du portail ?

Lors du lancement initial de la préversion du portail des développeurs, vous avez provisionné la préversion de son contenu par défaut dans votre service Gestion des API. Le contenu par défaut a été sensiblement modifié dans la version en disponibilité générale. Par exemple, la préversion du contenu par défaut n’inclut pas les boutons OAuth dans les pages de connexion ; elle utilise différents widgets pour l’affichage des API et s’appuie sur des fonctionnalités limitées pour la structuration des pages du portail des développeurs. Même s’il existe des différences de contenu, le moteur du portail (y compris les widgets sous-jacents) est mis à jour automatiquement chaque fois que vous publiez votre portail des développeurs.

Si vous avez fortement personnalisé votre portail en fonction de la préversion du contenu, vous pouvez continuer à l’utiliser tel quel et placer de nouveaux widgets manuellement dans les pages du portail. Dans le cas contraire, nous vous recommandons de remplacer le contenu de votre portail par le nouveau contenu par défaut.

Pour réinitialiser le contenu d’un portail managé, cliquez sur **Réinitialiser le contenu** dans la section de menu **Opérations**. Cette opération supprimera tout le contenu du portail et provisionnera le nouveau contenu par défaut. Vous perdrez toutes les personnalisations et modifications apportées au portail des développeurs. **Vous ne pouvez pas annuler cette action**.

![Réinitialiser le contenu du portail](media/api-management-howto-developer-portal/reset-content.png)

Si vous utilisez la version auto-hébergée, utilisez les scripts `scripts.v2/cleanup.bat` et `scripts.v2/generate.bat` à partir du dépôt GitHub pour supprimer le contenu actuel et provisionner du nouveau contenu. Veillez au préalable à effectuer une mise à niveau vers la dernière version du code de votre portail à partir du dépôt GitHub.

Si vous avez accédé au portail pour la première fois après l’annonce de la disponibilité générale en novembre 2019, le nouveau contenu par défaut doit déjà être présent et aucune action supplémentaire n’est requise.

### <a name="does-the-portal-have-all-the-features-of-the-legacy-portal"></a>Le portail comporte-t-il toutes les fonctionnalités de l’ancien portail ?

Le portail des développeurs ne prend plus en charge les *Applications*, les *Problèmes* et l’intégration directe avec Facebook, Microsoft, Twitter et Google en tant que fournisseurs d’identité (vous pouvez utiliser Azure AD B2C à la place).

### <a name="has-the-legacy-portal-been-deprecated"></a>L’ancien portail est-il déconseillé ?

L’ancien portail des développeurs et l’ancien portail des éditeurs constituent à présent des fonctionnalités *héritées*, qui ne recevront que des correctifs de sécurité. Les nouvelles fonctionnalités ne seront implémentées que dans le nouveau portail des développeurs.

La dépréciation des portails hérités sera annoncée séparément. Si vous avez des questions, des doutes ou des commentaires, signalez-les dans un [problème GitHub dédié](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>Les fonctionnalités dont j’ai besoin ne sont pas prises en charge dans le portail

Vous pouvez ouvrir une [demande de fonctionnalité](https://aka.ms/apimwish) ou [implémenter vous-même la fonctionnalité manquante][3]. Si vous implémentez la fonctionnalité vous-même, vous pouvez auto-héberger le portail des développeurs ou ouvrir une requête de tirage (pull request) sur GitHub pour inclure les modifications à la version managée.

### <a name="how-can-i-automate-portal-deployments"></a>Comment automatiser les déploiements de portails ?

Vous pouvez accéder programmatiquement au contenu du portail des développeurs et le gérer avec l’API REST, que vous utilisiez une version managée ou auto-hébergée.

L’API est documentée dans la [section wiki du référentiel GitHub][2]. Elle peut servir à automatiser les migrations du contenu du portail entre différents environnements, par exemple d’un environnement de test à l’environnement de production. Pour plus d’informations sur ce processus, lisez [cet article de documentation](https://aka.ms/apimdocs/migrateportal) sur GitHub.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Comment passer de la version gérée à la version auto-hébergée ?

Consultez l’article détaillé dans la [section Wiki du référentiel du portail des développeurs sur GitHub][2].

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Puis-je avoir plusieurs portails des développeurs dans un même service Gestion des API ?

Vous pouvez avoir un portail géré et plusieurs portails auto-hébergés. Le contenu de tous les portails étant stocké dans le même service Gestion des API, ils sont identiques. Si vous souhaitez différencier leur apparence et leurs fonctionnalités, vous pouvez les auto-héberger en utilisant vos propres widgets personnalisés pour personnaliser dynamiquement les pages à l’exécution, par exemple, en fonction de l’URL.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Le portail prend-il en charge les modèles Azure Resource Manager ? Est-il compatible avec le kit de ressources DevOps Gestion des API ?

Non.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Le contenu du portail est-il enregistré avec la fonctionnalité de sauvegarde/restauration de la Gestion des API ?

Non.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Faut-il activer une connectivité de réseau virtuel supplémentaire pour les dépendances du portail managé ?

Dans la plupart des cas, non.

Si votre service de gestion des API se trouve dans un réseau virtuel interne, votre portail des développeurs est accessible uniquement à partir de ce réseau. Le nom d’hôte du point de terminaison de gestion doit être résolu en adresse IP virtuelle interne du service à partir de l’ordinateur utilisé pour accéder à l’interface d’administration du portail. Veillez à ce que le point de terminaison de gestion soit inscrit dans le DNS. En cas d’erreur de configuration, l’erreur suivante apparaît : `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

Si votre service Gestion des API se trouve dans un réseau virtuel interne, et que vous y accédez par le biais d’Application Gateway à partir d’Internet, veillez à activer la connectivité au portail des développeurs et aux points de terminaison de gestion de Gestion des API.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>J’ai attribué un domaine de gestion des API personnalisé et le portail publié ne fonctionne pas

Après avoir mis à jour le domaine, vous devez [republier le portail](api-management-howto-developer-portal-customize.md#publish) pour que les modifications prennent effet.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>J’ai ajouté un fournisseur d’identité mais il n’apparaît pas dans le portail

Après avoir configuré un fournisseur d’identité (par exemple, AAD, AAD B2C), vous devez [republier le portail](api-management-howto-developer-portal-customize.md#publish) pour que les modifications prennent effet.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>J’ai configuré la délégation mais le portail ne l’utilise pas

Après avoir configuré la délégation, vous devez [republier le portail](api-management-howto-developer-portal-customize.md#publish) pour que les modifications prennent effet.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Les autres modifications apportées à la configuration de la gestion des API n’ont pas été propagées dans le portail des développeurs

La plupart des modifications de configuration (par exemple, réseau virtuel, connexion et conditions de produit) nécessitent une [republication du portal](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> J’obtiens une erreur CORS lorsque j’utilise la console interactive

La console interactive effectue une requête d’API côté client à partir du navigateur. Résolvez le problème CORS en ajoutant une [stratégie CORS ](api-management-cross-domain-policies.md#CORS)sur vos API.

Vous pouvez vérifier l’état de la stratégie CORS dans la section **Vue d’ensemble du portail** de votre service Gestion des API dans le Portail Azure. Une zone d’avertissement indique une stratégie absente ou mal configurée.

![Portail des développeurs Gestion des API](media/api-management-howto-developer-portal/cors-azure-portal.png)

Appliquez automatiquement la stratégie CORS en cliquant sur le bouton **Activer CORS**.

Vous pouvez également activer CORS manuellement.

1. Cliquez sur le lien **L’appliquer manuellement au niveau global** pour afficher le code de stratégie généré.
2. Accédez à **Toutes les API** dans la section **API** de votre service Gestion des API dans le Portail Azure.
3. Cliquez sur l’icône **</>** dans la section **Traitement entrant**.
4. Insérez la stratégie dans la section **<inbound>** du fichier XML. Assurez-vous que la valeur **<origin>** correspond au domaine de votre portail des développeurs.

> [!NOTE]
> 
> Si vous appliquez la stratégie CORS dans l’étendue du produit, au lieu de l’étendue de l’API, et que votre API utilise l’authentification par clé d’abonnement via un en-tête, votre console ne fonctionnera pas.
>
> Le navigateur émet automatiquement une requête HTTP OPTIONS, qui ne contient pas d’en-tête avec la clé d’abonnement. La clé d’abonnement étant absente, la gestion des API ne peut pas associer l’appel OPTIONS à un produit et, par conséquent, ne peut pas appliquer la stratégie CORS.
>
> Pour contourner le problème, vous pouvez transmettre la clé d’abonnement dans un paramètre de requête.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Quelles sont les autorisations requises pour modifier le portail des développeurs ?

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

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>L’erreur `Unable to start the portal. See if settings are specified correctly (...)` apparaît

Cette erreur s’affiche lorsqu’un appel `GET` à `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` échoue. L’appel est émis à partir du navigateur par l’interface d’administration du portail.

Si votre service de gestion des API est un réseau virtuel, reportez-vous à la question sur la connectivité du réseau virtuel ci-dessus.

L’échec de l’appel peut également être dû au fait qu’un certificat TLS/SSL attribué à un domaine personnalisé ne soit pas approuvé par le navigateur. Pour remédier à ce problème, vous pouvez supprimer le domaine personnalisé de point de terminaison de gestion. La gestion des API reviendra au point de terminaison par défaut avec un certificat approuvé.

### <a name="whats-the-browser-support-for-the-portal"></a>Quelle est la prise en charge du navigateur pour le portail ?

| Browser                     | Prise en charge       |
|-----------------------------|-----------------|
| Apple Safari                | Oui<sup>1</sup> |
| Google Chrome               | Oui<sup>1</sup> |
| Microsoft Edge              | Oui<sup>1</sup> |
| Microsoft Internet Explorer | Non              |
| Mozilla Firefox             | Oui<sup>1</sup> |

 <small><sup>1</sup> Pris en charge dans les deux dernières versions de production.</small>

## <a name="next-steps"></a>Étapes suivantes

Découvrez le nouveau portail des développeurs :

- [Accéder au portail managé des développeurs et le personnaliser](api-management-howto-developer-portal-customize.md)
- [Configurer la version auto-hébergée du portail][2]
- [Implémenter votre propre widget][3]

Parcourez d’autres ressources :

- [Référentiel GitHub avec le code source][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
