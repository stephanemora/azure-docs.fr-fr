---
title: Interface d’API web - API du service d’exploration des connaissances
titlesuffix: Azure Cognitive Services
description: Utilisez l’interface d’API web pour créer une expérience de recherche sémantique enrichie dans l’API Service d’exploration des connaissances (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 970db5984eedebf98bbb087cfd0b4a35a21a0f54
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861185"
---
# <a name="web-api-interface"></a>Interface d’API web

Les fichiers de modèle générés par le Service d’exploration des connaissances peuvent être hébergés et sont accessibles via un ensemble d’API web.  Les API peuvent être hébergées sur l’ordinateur local à l’aide de la commande [`host_service`](CommandLine.md#host_service-command), ou peuvent être déployées sur un service cloud Azure à l’aide de la commande [`deploy_service`](CommandLine.md#deploy_service-command).  Ces deux techniques exposent les points de terminaison d’API suivants :

* [*interpret*](interpretMethod.md) : interprète une chaîne de requête en langage naturel. Retourne des interprétations annotées pour activer des expériences enrichies de saisie semi-automatique dans les zones de recherche qui anticipent la saisie utilisateur.
* [*evaluate*](evaluateMethod.md) : évalue et retourne le résultat d’une expression de requête structurée.
* [*calchistogram*](calchistogramMethod.md) : calcule un histogramme de valeurs d’attribut pour les objets retournés par une expression de requête structurée.

Utilisées conjointement, ces méthodes d’API permettent la création d’une expérience de recherche sémantique enrichie.  Pour une chaîne de requête en langage naturel, la méthode *interpret* propose des versions annotées de la requête d’entrée avec des expressions de requête structurée, en fonction de la grammaire sous-jacente et des données d’index.  La méthode *evaluate* évalue l’expression de requête structurée et retourne les objets d’index correspondants à afficher.  La méthode *calchistogram* calcule les distributions de valeur d’attribut pour activer le filtrage et l’affinement.

**Exemple**

Dans un domaine de publications académiques, si un utilisateur tape la chaîne « latente s », la méthode *interpret* propose un ensemble d’interprétations classées, suggérant que l’utilisateur peut être en train de rechercher le mot-clé « latent semantic analysis » (analyse sémantique latente), le titre « latent structure analysis » (analyse de structure latente) ou d’autres expressions commençant par « latent s ».  Ces informations peuvent être utilisées pour guider rapidement l’utilisateur vers les résultats de recherche souhaités.

Pour ce domaine, la méthode *evaluate* peut être utilisée pour récupérer un ensemble de publications correspondantes dans l’index académique, et la méthode *calchistogram* peut être utilisée pour calculer la distribution des valeurs d’attribut pour les publications correspondantes, qui peuvent être utilisées pour filtrer et affiner davantage les résultats de recherche.

Notez que pour améliorer la lisibilité des exemples, les appels d’API REST contiennent des caractères (tels que des espaces) qui n’ont pas été codés URL. Votre code doit appliquer les codages URL appropriés.
