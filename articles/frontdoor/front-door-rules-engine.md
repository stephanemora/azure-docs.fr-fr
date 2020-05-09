---
title: Azure Front Door | Microsoft Docs
description: Cet article fournit une vue d’ensemble d’Azure Front Door. Déterminez s’il représente un choix adapté à l’équilibrage de charge du trafic utilisateur pour votre application.
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
ms.openlocfilehash: 19deb763c8e750490854892c90d0293d3e209c09
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515794"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Qu’est-ce que le moteur de règles pour Azure Front Door ? 

Le moteur de règles vous permet de personnaliser la façon dont les requêtes HTTP sont gérées à la périphérie et fournit davantage de contrôle sur le comportement de votre application web. Le moteur de règles pour Azure Front Door comprend plusieurs fonctionnalités clés, notamment :

- Routage basé sur l’en-tête – routez les requêtes en fonction des séquences dans le contenu des en-têtes, des cookies et des chaînes de requête.
- Routage basé sur les paramètres – tirez parti d’une série de conditions de correspondance, notamment les arguments Post, les chaînes de requête, les cookies et les méthodes de requête, pour router les requêtes en fonction des paramètres de requête HTTP. 
- Remplacements de configurations de route : 
    - Utilisez les fonctionnalités de redirection pour retourner les redirections 301/302/307/308 au client afin de rediriger vers de nouveaux noms d’hôte, chemins et protocoles. 
    - Utilisez les fonctionnalités de transfert pour réécrire le chemin de l’URL de la requête sans effectuer de redirection classique et transférer la requête au back-end approprié dans votre pool de back-ends configuré. 
    - Personnalisez votre configuration de mise en cache et changez dynamiquement une route en passant du transfert à la mise en cache en fonction des conditions de correspondance. 

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="architecture"></a>Architecture 

Le moteur de règles gère les requêtes à la périphérie. Une fois que vous avez configuré le moteur de règles, quand une requête atteint votre point de terminaison Front Door, WAF est exécuté en premier, suivi de la configuration du moteur de règles associée à votre front-end/domaine. Quand une configuration de moteur de règles est exécutée, cela signifie que la règle de routage parente est déjà une correspondance. L’exécution de toutes les actions de chacune des règles au sein de la configuration du moteur de règles a lieu si toutes les conditions de correspondance au sein de cette règle sont remplies. Si une requête ne correspond à aucune des conditions de la configuration de votre moteur de règles, la règle de routage par défaut est exécutée. 

Par exemple, dans la configuration ci-dessous, un moteur de règles est configuré pour ajouter un en-tête de réponse qui change l’âge maximal du contrôle de cache si la condition de correspondance est remplie. 

![action d’en-tête de réponse](./media/front-door-rules-engine/rules-engine-architecture-3.png)

Dans un autre exemple, nous voyons que le moteur de règles est configuré pour envoyer un utilisateur vers une version mobile du site si la condition de correspondance, type d’appareil, est vraie. 

![remplacement de configuration de route](./media/front-door-rules-engine/rules-engine-architecture-1.png)

Dans ces deux exemples, si aucune des conditions de correspondance n’est remplie, la règle de routage spécifiée est celle qui est exécutée. 

## <a name="terminology"></a>Terminologie 

Avec le moteur de règles AFD, vous pouvez créer une série de configurations de moteur de règles, chacune composée d’un ensemble de règles. Ce qui suit présente une terminologie utile que vous rencontrerez lors de la configuration de votre moteur de règles. 

- *Configuration de moteur de règles* : Ensemble de règles appliquées à une seule règle de routage. Chaque configuration est limitée à 5 règles. Vous pouvez créer jusqu’à 10 configurations. 
- *Règle du moteur de règles* : Chaque règle peut avoir jusqu’à 10 conditions de correspondance et 5 actions.
- *Condition de correspondance* : Il existe de nombreuses conditions de correspondance qui peuvent être utilisées pour analyser vos requêtes entrantes. Une règle peut contenir jusqu’à 10 conditions de correspondance. Les conditions de correspondance sont évaluées avec un opérateur **AND**. Vous trouverez la liste complète des conditions de correspondance [ici](front-door-rules-engine-match-conditions.md). 
- *Action* : Les actions dictent ce qui arrive à vos requêtes entrantes : actions d’en-tête de requête/réponse, transfert, redirection et réécriture sont toutes disponibles aujourd’hui. Une règle peut contenir jusqu’à 5 actions, mais seulement 1 remplacement de configuration de route.  Vous trouverez la liste complète des actions [ici](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment définir votre première [Configuration du moteur de règles](front-door-tutorial-rules-engine.md). 
- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).
