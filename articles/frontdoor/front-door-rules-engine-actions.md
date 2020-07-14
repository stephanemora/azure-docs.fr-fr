---
title: Azure Front Door
description: Cet article fournit la liste des différentes actions que vous pouvez effectuer avec le moteur de règles Azure Front Door.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: e11555e883a323bcb5b0be1c62b2825bce77524e
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85313992"
---
# <a name="azure-front-door-rules-engine-actions"></a>Actions du moteur de règles Azure Front Door

Dans le [moteur de règles AFD](front-door-rules-engine.md), une règle se compose de zéro, d’une ou de plusieurs conditions de correspondance et actions. Cet article fournit les descriptions détaillées des actions que vous pouvez utiliser dans le moteur de règles AFD.

Une action définit le comportement appliqué au type de requête qu’une condition de correspondance ou un ensemble de conditions de correspondance identifie. Dans le moteur de règles AFD, une règle peut contenir jusqu’à cinq actions, dont une seule peut être une action de remplacement de configuration de route (transfert ou redirection).

Les actions suivantes sont prêtes à être utilisées dans le moteur de règles Azure Front Door.  

## <a name="modify-request-header"></a>Modifier l’en-tête de requête

Utilisez cette action pour modifier les en-têtes présents dans les requêtes envoyées à votre origine.

### <a name="required-fields"></a>Champs obligatoires

Action | Nom de l’en-tête HTTP | Valeur
-------|------------------|------
Ajouter | Lorsque cette option est sélectionnée et que la règle correspond, l’en-tête spécifié dans **Nom de l’en-tête** est ajouté à la requête avec la valeur spécifiée. Si l’en-tête est déjà présent, la valeur sera ajoutée à la valeur existante. | String
Remplacer | Lorsque cette option est sélectionnée et que la règle correspond, l’en-tête spécifié dans **Nom de l’en-tête** est ajouté à la requête avec la valeur spécifiée. Si l’en-tête est déjà présent, la valeur spécifiée remplace la valeur existante. | String
DELETE | Lorsque cette option est sélectionnée, que la règle correspond et que l’en-tête spécifié dans la règle est présent, l’en-tête est supprimé de la requête. | String

## <a name="modify-response-header"></a>Modifier l’en-tête de réponse

Utilisez cette action pour modifier les en-têtes présents dans les réponses retournées à vos clients.

### <a name="required-fields"></a>Champs obligatoires

Action | Nom de l’en-tête HTTP | Valeur
-------|------------------|------
Ajouter | Lorsque cette option est sélectionnée et que la règle correspond, l’en-tête spécifié dans **Nom de l’en-tête** est ajouté à la réponse à l’aide de la **Valeur** spécifiée. Si l’en-tête est déjà présent, **Valeur** est ajoutée à la valeur existante. | String
Remplacer | Lorsque cette option est sélectionnée et que la règle correspond, l’en-tête spécifié dans **Nom de l’en-tête** est ajouté à la réponse à l’aide de la **Valeur** spécifiée. Si l’en-tête est déjà présent, **Valeur** remplace la valeur existante. | String
DELETE | Lorsque cette option est sélectionnée, que la règle correspond et que l’en-tête spécifié dans la règle est présent, l’en-tête est supprimé de la réponse. | String

## <a name="route-configuration-overrides"></a>Remplacements de configuration de route 

### <a name="route-type-redirect"></a>Type de route : Rediriger

Utilisez cette action pour rediriger les clients vers une nouvelle URL. 

#### <a name="required-fields"></a>Champs obligatoires

Champ | Description 
------|------------
Type de redirection | Sélectionnez le type de réponse à renvoyer au demandeur : Trouvé (302), Déplacé (301), Redirection temporaire (307) et Redirection permanente (308).
Protocole de redirection | Requête correspondante, HTTP ou HTTPS.
Hôte de destination | Sélectionnez le nom d’hôte vers lequel vous souhaitez rediriger la requête. Laissez vide pour conserver l’hôte entrant.
Chemin de destination | Définissez le chemin d’accès à utiliser dans la redirection. Laissez vide pour conserver le chemin d’accès entrant.  
Chaîne de requête | Définissez la chaîne de requête utilisée dans la redirection. Laissez vide pour conserver la chaîne de requête entrante. 
Fragment de destination | Définissez le fragment à utiliser dans la redirection. Laissez vide pour conserver le fragment entrant. 


### <a name="route-type-forward"></a>Type de route : Transférer

Utilisez cette action pour transférer les clients vers une nouvelle URL. Cette action contient aussi des sous-actions pour les réécritures et mises en cache d’URL. 

Champ | Description 
------|------------
Pool principal | Sélectionnez le pool de back-ends à remplacer et à partir duquel servir les requêtes. Cela montre tous vos pools de back-ends préconfigurés qui sont actuellement dans votre profil Front Door. 
Protocole de transfert | Requête correspondante, HTTP ou HTTPS.
Réécrire URL | Utilisez cette action pour réécrire le chemin d’accès d’une requête en cours d’acheminement vers votre origine. S’il est activé, voir ci-dessous pour les autres champs obligatoires
Mise en cache | Activé, Désactivé. Voir ci-dessous pour les autres champs obligatoires s’il est activé. 

#### <a name="url-rewrite"></a>Réécrire URL

Utilisez ce paramètre pour configurer un **chemin de transfert personnalisé** facultatif à utiliser lors de la construction d’une requête à transférer au back-end.

Champ | Description 
------|------------
Chemin de transfert personnalisé | Définissez le chemin vers lequel transférer les requêtes. 

#### <a name="caching"></a>Mise en cache

Utilisez ces paramètres pour contrôler la façon dont les fichiers sont mis en cache pour les demandes qui contiennent des chaînes de requête et si vous voulez mettre en cache votre contenu en fonction de tous les paramètres ou des paramètres sélectionnés. Vous pouvez utiliser des paramètres supplémentaires pour remplacer la valeur de durée de vie (TTL) pour contrôler la durée pendant laquelle le contenu reste dans le cache pour les demandes que les conditions de correspondance de règles spécifient. Pour forcer la mise en cache comme action, définissez le champ de mise en cache sur « Activé ». Si vous effectuez cette opération, les options suivantes s’affichent : 

Comportement du cache |  Description              
---------------|----------------
Ignorer les chaînes de requête | Une fois que la ressource est mise en cache, toutes les demandes qui suivent ignorent les chaînes de requête jusqu’à l’expiration de la ressource mise en cache.
Mettre en cache chaque URL unique | Chaque demande contenant une URL unique, y compris la chaîne de requête, est traitée comme une ressource unique avec son propre cache.
Ignorer les chaînes de requête spécifiées | Les chaînes de requête d’URL de demande listées dans le paramètre « Paramètres de requête » sont ignorées pour la mise en cache.
Inclure les chaînes de requête spécifiées | Les chaînes de requête d’URL de demande listées dans le paramètre « Paramètres de requête » sont utilisées pour la mise en cache.

Champs supplémentaires |  Description 
------------------|---------------
Compression dynamique | Front Door peut compresser dynamiquement le contenu en périphérie, ce qui a pour effet de réduire la taille et le délai de la réponse.
Paramètres de requête | Liste séparée par des virgules des paramètres autorisés (ou non autorisés) à utiliser comme base pour la mise en cache.
Durée du cache | Durée d’expiration du cache en jours, heures, minutes, secondes. Toutes les valeurs doivent être des entiers. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment définir votre première [Configuration du moteur de règles](front-door-tutorial-rules-engine.md). 
- En savoir plus sur les [Conditions de correspondance du moteur de règles](front-door-rules-engine-match-conditions.md)
- En savoir plus sur le [Moteur de règles Azure Front Door](front-door-rules-engine.md)
