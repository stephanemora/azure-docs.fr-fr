---
title: Actions du moteur des règles standard Azure CDN de Microsoft | Microsoft Docs
description: Documentation de référence pour les actions du moteur des règles standard Azure CDN de Microsoft.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: dbde93cc7ffd21e341653407e6e4f910e4620974
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615848"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-actions"></a>Actions du moteur des règles standard Azure CDN de Microsoft

Cet article fournit les descriptions détaillées des actions disponibles pour le [Moteur des règles standard](cdn-standard-rules-engine.md) Azure Content Delivery Network (CDN) de Microsoft.

La deuxième partie d’une règle est une action. Une action définit le comportement appliqué au type de requête qui est identifié par un ensemble de conditions de correspondance.

## <a name="actions"></a>Actions

Les actions suivantes peuvent être utilisées. 

## <a name="cache-expiration"></a>Expiration du cache

Cette action vous permet de remplacer la durée de vie du point de terminaison pour les requêtes spécifiées par les conditions de correspondance des règles.

**Champs obligatoires**

Comportement du cache |                
---------------|----------------
Ignorer le cache | Lorsque cette option est sélectionnée et que la règle correspond, le contenu n’est pas mis en cache.
Écraser | Lorsque cette option est sélectionnée et que la règle correspond, la valeur TTL retournée par origine est remplacée par la valeur spécifiée dans l’action.
Définir en cas d’absence | Lorsque cette option est sélectionnée et que la règle correspond, s’il n’y a aucune valeur TTL retournée par origine, la règle définira la durée de vie sur la valeur spécifiée dans l’action.

**Champs supplémentaires**

Jours | Heures | Minutes | Secondes
-----|-------|---------|--------
Int | Int | Int | Int 

## <a name="cache-key-query-string"></a>Chaîne de requête de clé de cache

Cette action vous permet de modifier la clé de cache en fonction des chaînes de requête.

**Champs obligatoires**

Comportement | Description
---------|------------
Inclure | Lorsque cette option est sélectionnée et que la règle correspond, les chaînes de requête spécifiées dans les paramètres sont incluses lors de la génération de la clé de cache. 
Mettre en cache chaque URL unique | Lorsque cette option est sélectionnée et que la règle correspond, chaque URL unique aura sa propre clé de cache. 
Exclure | Lorsque cette option est sélectionnée et que la règle correspond, les chaînes de requête spécifiées dans les paramètres sont excluses lors de la génération de la clé de cache.
Ignorer les chaînes de requête | Lorsque cette option est sélectionnée et que la règle correspond, les chaînes de requête sont considérées lors de la génération de la clé de cache. 

## <a name="modify-request-header"></a>Modifier l'en-tête de requête

Cette action vous permet de modifier les en-têtes présents dans les requêtes envoyées à votre origine.

**Champs obligatoires**

Action | Nom de l’en-tête HTTP | Valeur
-------|------------------|------
Append | Lorsque cette option est sélectionnée et que la règle correspond, l’en-tête spécifié dans Nom de l’en-tête est ajouté à la requête avec la valeur spécifiée. Si l’en-tête est déjà présent, la valeur sera ajoutée à la valeur existante. | Chaîne
Remplacer | Lorsque cette option est sélectionnée et que la règle correspond, l’en-tête spécifié dans Nom de l’en-tête est ajouté à la requête avec la valeur spécifiée. Si l’en-tête est déjà présent, la valeur sera remplacée par la valeur existante. | Chaîne
Supprimer | Lorsque cette option est sélectionnée et que la règle correspond et que l’en-tête spécifié dans la règle est présent, elle est supprimée de la requête. | Chaîne

## <a name="modify-response-header"></a>Modifier l'en-tête de réponse

Cette action vous permet de modifier les en-têtes présents dans les réponses retournées à vos clients finaux

**Champs obligatoires**

Action | Nom de l’en-tête HTTP | Valeur
-------|------------------|------
Append | Lorsque cette option est sélectionnée et que la règle correspond, l’en-tête spécifié dans Nom de l’en-tête est ajouté à la réponse avec la valeur spécifiée. Si l’en-tête est déjà présent, la valeur sera ajoutée à la valeur existante. | Chaîne
Remplacer | Lorsque cette option est sélectionnée et que la règle correspond, l’en-tête spécifié dans Nom de l’en-tête est ajouté à la réponse avec la valeur spécifiée. Si l’en-tête est déjà présent, la valeur sera remplacée par la valeur existante. | Chaîne
Supprimer | Lorsque cette option est sélectionnée et que la règle correspond et que l’en-tête spécifié dans la règle est présent, elle est supprimée de la réponse. | Chaîne

## <a name="url-redirect"></a>Redirection d'URL

Cette action vous permet de rediriger les clients finaux vers une nouvelle URL. 

**Champs obligatoires**

Champ | Description 
------|------------
Type | Sélectionnez le type de réponse qui sera renvoyé au demandeur. Les options disponibles sont : 302 Trouvé, 301 Déplacé, 307 Redirection temporaire et 308 Redirection permanente
Protocol | Requête correspondante, HTTP ou HTTPS
Nom d’hôte | Sélectionnez le nom d’hôte vers lequel la requête sera redirigée. Laissez vide pour conserver l’hôte entrant.
Path | Définissez le chemin d’accès à utiliser dans la redirection. Laissez vide pour conserver le chemin d'accès entrant.  
Chaîne de requête | Définissez la chaîne de requête utilisée dans la redirection. Laissez vide pour conserver la chaîne de requête entrante. 
Fragment | Définissez le fragment à utiliser dans la redirection. Laissez vide pour conserver le fragment entrant. 

Il est fortement recommandé d’utiliser une URL absolue. L’utilisation d’une URL relative peut rediriger les URL CDN vers un chemin d’accès non valide. 

## <a name="url-rewrite"></a>Réécriture d’URL

Cette action vous permet de réécrire le chemin d’accès d’une requête en cours d’acheminement vers votre origine.

**Champs obligatoires**

Champ | Description 
------|------------
Modèle source | Définissez le modèle source dans le chemin d’accès de l’URL à remplacer. Actuellement, le modèle source utilise une correspondance basée sur un préfixe. Pour correspondre à tous les chemins d’accès d’URL, utilisez « / » comme valeur de modèle source.
Destination | Définissez le chemin d’accès de destination à utiliser lors de la réécriture. Cela remplacera le modèle source
Conserver le chemin d’accès sans correspondance | Si c’est le cas, le chemin d’accès restant après le modèle source est ajouté au nouveau chemin d’accès de destination. 


[Revenir en haut](#actions)

</br>

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble d’Azure Content Delivery Network](cdn-overview.md)
- [Informations de référence du moteur de règles](cdn-standard-rules-engine-reference.md)
- [Conditions de correspondance du moteur de règles](cdn-standard-rules-engine-match-conditions.md)
- [Contraindre HTTPS à l’aide du moteur de règles standard](cdn-standard-rules-engine.md)
