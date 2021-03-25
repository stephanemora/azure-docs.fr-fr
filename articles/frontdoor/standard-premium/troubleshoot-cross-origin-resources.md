---
title: Utilisation d’Azure Front Door Standard/Premium avec le partage des ressources cross-origin
description: Découvrez comment utiliser Azure Front Door (AFD) avec le partage de ressources cross-origin (CORS).
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097603"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>Utilisation d’Azure Front Door Standard/Premium avec le partage des ressources cross-origin (CORS)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

## <a name="what-is-cors"></a>Présentation de CORS

CORS (Cross Origin Resource Sharing) est une fonctionnalité HTTP qui permet à une application web exécutée dans un domaine d’accéder aux ressources d’un autre domaine. Pour réduire le risque d’attaques de script entre sites, tous les navigateurs web modernes implémentent une restriction de sécurité appelée [stratégie de même origine](https://www.w3.org/Security/wiki/Same_Origin_Policy). Celle-ci empêche une page web d’appeler des API dans un autre domaine.  CORS permet à une origine (le domaine d’origine) d’appeler des API dans un autre domaine de façon sécurisée.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-it-works"></a>Fonctionnement

Il existe deux types de demandes CORS : les *demandes simples* et *les demandes complexes*.

### <a name="for-simple-requests"></a>Pour les demandes simples :

1. Le navigateur envoie la demande CORS avec un autre en-tête de demande HTTP d’**origine**. La valeur de cet en-tête est l’origine qui a servi la page mère, définie comme la combinaison du *protocole, du* *domaine* et du *port*.  Lorsqu’une page du site https\://www.contoso.com tente d’accéder aux données d’un utilisateur dans l’origine fabrikam.com, l’en-tête de requête suivant est envoyé à fabrikam.com :

   `Origin: https://www.contoso.com`

2. Le serveur peut renvoyer les éléments suivants :

   * Un en-tête **Access-Control-Allow-Origin** indiquant le site d’origine autorisé. Par exemple :

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Un code d’erreur HTTP comme 403 si le serveur n’autorise pas la demande d’origine croisée après avoir vérifié l’en-tête d’origine.

   * Un en-tête **Access-Control-Allow-Origin** avec un caractère générique qui autorise toutes les origines :

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Pour les demandes complexes :

Une demande complexe est une demande CORS où le navigateur est nécessaire pour envoyer une *demande préliminaire* (c’est-à-dire une sonde préliminaire) avant d’envoyer la demande CORS. La demande préliminaire demande l’autorisation au serveur si la demande CORS d’origine peut se poursuivre et est une demande `OPTIONS` transmise à la même URL.

> [!TIP]
> Pour plus d’informations sur les flux CORS et les pièges les plus courants, consultez le [Authoritative guide to CORS (Cross-Origin Resource Sharing) for REST APIs (Guide de référence sur CORS (Cross-Origin Resource Sharing) pour les API REST)](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Scénarios avec caractère générique ou à origine unique
CORS sur Azure Front Door fonctionne automatiquement sans aucune configuration supplémentaire quand l’en-tête **Access-Control-Allow-Origin** est défini sur le caractère générique (*) ou une origine unique.  Azure Front Door met en cache la première réponse, et les demandes suivantes utilisent le même en-tête.

Si les demandes sont communiquées à Azure Front Door avant que CORS ne soit défini sur l’origine, vous devez vider le contenu de votre point de terminaison pour recharger le contenu avec l’en-tête **Access-Control-Allow-Origin**.

## <a name="multiple-origin-scenarios"></a>Scénarios avec plusieurs origines
Si une liste d’origines spécifique doit être autorisée pour CORS, les choses se compliquent un peu plus. Le problème se produit quand le CDN met en cache l’en-tête **Access-Control-Allow-Origin** pour la première origine CORS.  Quand une autre origine CORS envoie une autre demande, le CDN utilise l’en-tête **Access-Control-Allow-Origin** mis en cache, qui ne correspond pas. Il existe plusieurs façons de résoudre ce problème.

### <a name="azure-front-door-rule-set"></a>Ensemble de règles Azure Front Door

Sur Azure Front Door, vous pouvez créer une règle dans le [jeu de règles](concept-rule-set.md) Azure Front Door pour vérifier l’en-tête **Origin** dans la demande. S’il s’agit d’une origine valide, votre règle définit l’en-tête **Access-Control-Allow-Origin** avec la valeur correcte. Dans ce cas, l’en-tête **Access-Control-Allow-Origin** issu du serveur d’origine du fichier est ignoré et le moteur de règles d’AFD gère entièrement les origines CORS autorisées.

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="Capture d’écran de l’exemple de règles avec l’ensemble de règles":::

> [!TIP]
> Vous pouvez ajouter des actions supplémentaires à votre règle pour modifier des en-têtes de réponse supplémentaires, par exemple **Access-Control-Allow-Methods**.
> 

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer une porte d’entrée](create-front-door-portal.md).
