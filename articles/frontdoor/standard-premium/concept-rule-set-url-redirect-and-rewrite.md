---
title: Redirection d’URL et réécriture d’URL avec Azure Front Door Standard/Premium (préversion)
description: Cet article vous aide à comprendre comment Azure Front Door prend en charge la redirection d’URL et la réécriture d’URL avec un ensemble de règles Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 382a4c040c7a519462ee3e35119b9471031e0724
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101097714"
---
# <a name="url-redirect-and-url-rewrite-with-azure-front-door-standardpremium-preview"></a>Redirection d’URL et réécriture d’URL avec Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Cet article vous aide à comprendre comment Azure Front Door Standard/Premium prend en charge la redirection d’URL et la réécriture d’URL dans un ensemble de règles.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="url-redirect"></a>Redirection d’URL

Azure Front Door peut rediriger le trafic à chacun des niveaux suivants : protocole, nom d’hôte, chemin d’accès, chaîne de requête et fragment. Ces fonctionnalités peuvent être configurées pour un microservice individuel dans la mesure où la redirection est basée sur le chemin d’accès. Avec la redirection d’URL, vous pouvez simplifier la configuration de l’application en optimisant l’utilisation des ressources et prendre en charge de nouveaux scénarios de redirection, notamment la redirection globale et basée sur le chemin d’accès.

Vous pouvez configurer la redirection d’URL par le biais d’un ensemble de règles.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-redirect.png" alt-text="Capture d’écran de la création d’une redirection d’URL avec un ensemble de règles" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-redirect-expanded.png":::

### <a name="redirection-types"></a>Types de redirection
Un type de redirection définit le code d’état de réponse pour aider les clients à comprendre l’objectif de la redirection. Les types suivants de redirection sont pris en charge :

* **301 Déplacé de façon permanente** : Indique que la ressource cible a été attribuée à un nouvel URI permanent. Toute référence future à cette ressource utilisera l'un des URI inclus. Utilisez le code d’état 301 pour la redirection du protocole HTTP vers HTTPS.
* **302 Trouvé** : Indique que la ressource cible se trouve temporairement sous un autre URI. Étant donné que la redirection peut changer à l'occasion, le client doit continuer à utiliser l'URI de requête effectif pour les demandes ultérieures.
* **307 Redirection temporaire** : Indique que la ressource cible se trouve temporairement sous un autre URI. L'agent utilisateur NE DOIT PAS modifier la méthode de demande s'il procède à une redirection automatique vers cet URI. Étant donné que la redirection peut changer au fil du temps, le client doit continuer à utiliser l’URI de requête en effet d’origine pour les requêtes ultérieures.
* **308 Redirection permanente** : Indique que la ressource cible a été attribuée à un nouvel URI permanent. Toute référence future à cette ressource devra utiliser l'un des URI inclus.

### <a name="redirection-protocol"></a>Protocole de redirection
Vous pouvez définir le protocole qui sera utilisé pour la redirection. Les cas d'usage les plus courants de la fonctionnalité de redirection sont la redirection du trafic HTTP vers HTTPS.

* **HTTPS uniquement** : Définissez le protocole sur HTTPS uniquement si vous cherchez à rediriger le trafic HTTP vers HTTPS. Azure Front Door recommande de toujours définir la redirection vers HTTPS uniquement.
* **HTTP uniquement** : Redirige la requête entrante vers HTTP. Utilisez cette valeur uniquement si vous souhaitez conserver votre trafic HTTP tel qu’il est, à savoir non chiffré.
* **Faire correspondre la requête** : Cette option conserve le protocole utilisé par la requête entrante. Par conséquent, une requête HTTP reste HTTP et une requête HTTPS reste une redirection HTTPS.

### <a name="destination-host"></a>Hôte de destination
Dans le cadre de la configuration d’un routage de redirection, vous pouvez également modifier le nom d’hôte ou le domaine pour la requête de redirection. Vous pouvez définir ce champ pour modifier le nom d’hôte dans l’URL pour la redirection, ou bien conserver le nom d’hôte de la demande entrante. Ainsi, avec ce champ, vous pouvez rediriger toutes les requêtes envoyées sur `https://www.contoso.com/*` vers `https://www.fabrikam.com/*`.

### <a name="destination-path"></a>Chemin de destination
Pour les cas où vous souhaiteriez remplacer le segment de chemin d’accès d’une URL dans le cadre de la redirection, vous pouvez définir ce champ avec la nouvelle valeur de chemin d’accès. Sinon, vous pouvez choisir de conserver la valeur de chemin d’accès dans le cadre de la redirection. Ainsi, avec ce champ, vous pouvez rediriger toutes les requêtes envoyées à `https://www.contoso.com/\*` vers `https://www.contoso.com/redirected-site`.

### <a name="query-string-parameters"></a>Paramètres de chaîne de requête
Vous pouvez également remplacer les paramètres de chaîne de requête dans l’URL redirigée. Pour remplacer une chaîne de requête existante de l'URL de requête entrante, définissez ce champ sur « Remplacer », puis définissez la valeur appropriée. Sinon, vous pouvez conserver le jeu initial de chaînes de requête en définissant le champ sur « Conserver ». Par exemple, à l’aide de ce champ, vous pouvez rediriger tout le trafic envoyé à `https://www.contoso.com/foo/bar` vers `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`. 

### <a name="destination-fragment"></a>Fragment de destination
Le fragment de destination est la partie de l'URL figurant après « # », que les navigateurs utilisent pour accéder à une section spécifique d'une page web. Vous pouvez définir ce champ pour ajouter un fragment à l’URL de redirection.

## <a name="url-rewrite"></a>Réécrire URL

Azure Front Door prend en charge la réécriture d’URL pour réécrire le chemin d’une demande en cours d’acheminement vers votre origine. La réécriture d’URL vous permet d’ajouter des conditions assurant que l’URL ou les en-têtes spécifiés sont réécrits uniquement quand certaines conditions sont remplies. Ces conditions sont basées sur les informations de requête et de réponse.

Avec cette fonctionnalité, vous pouvez rediriger les utilisateurs vers différentes origines en fonction du scénario, du type d’appareil et du type de fichier demandé.

Vous pouvez configurer la redirection d’URL par le biais d’un ensemble de règles.

:::image type="content" source="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite.png" alt-text="Capture d’écran de la création d’une réécriture d’URL avec un ensemble de règles" lightbox="../media/concept-url-redirect-and-rewrite/front-door-url-rewrite-expanded.png":::

### <a name="source-pattern"></a>Modèle source

Le modèle source est le chemin de l’URL dans la demande source à remplacer. Actuellement, le modèle source utilise une correspondance basée sur un préfixe. Pour correspondre à tous les chemins d’URL, utilisez une barre oblique (/) comme valeur de modèle source.

### <a name="destination"></a>Destination

Vous pouvez définir le chemin de destination à utiliser lors de la réécriture. Le chemin d’accès de destination remplace le modèle source.

### <a name="preserve-unmatched-path"></a>Conserver le chemin d’accès sans correspondance

Le fait de conserver le chemin sans correspondance vous permet d’ajouter le chemin restant après le modèle source au nouveau chemin.

Par exemple, si je définis **Conserver le chemin d’accès sans correspondance sur Oui** :
* Si la demande entrante est `www.contoso.com/sub/1.jpg`, le modèle source est défini sur `/`, la destination est définie sur `/foo/` et le contenu est servi à partir de `/foo/sub/1`.jpg depuis l’origine.

* Si la demande entrante est `www.contoso.com/sub/image/1.jpg`, le modèle source est défini sur `/sub/`, la destination est définie sur `/foo/` et le contenu est servi à partir de `/foo/image/1.jpg` depuis l’origine.

Par exemple, si je définis **Conserver le chemin d’accès sans correspondance sur Non** :
* Si la demande entrante est `www.contoso.com/sub/image/1.jpg`, le modèle source est défini sur `/sub/`, la destination est définie sur `/foo/2.jpg` et le contenu est toujours servi à partir de `/foo/2.jpg` depuis l’origine quels que soient les chemins suivis dans `wwww.contoso.com/sub/`.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez-en plus sur l’[ensemble de règles Azure Front Door Standard/Premium](concept-rule-set.md).
