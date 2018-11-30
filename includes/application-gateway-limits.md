---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 3d66d825306c5183bdd8d8e611d98904eef2022a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440133"
---
| Ressource | Limite par défaut | Remarque |
| --- | --- | --- |
| Application Gateway |1 000 par abonnement | |
| Configurations d’adresses IP frontales |2 |1 publique et 1 privée |
| Ports de serveur frontal |40 | |
| Pools d’adresses principales |40 | |
| Serveurs principaux par pool |1 200 | |
| Écouteurs HTTP |40 | |
| Règles d’équilibrage de charge HTTP |400 |Nb d’écouteurs HTTP * n |
| Paramètres HTTP principaux |40 | |
| Instances par passerelle |75 | |
| Certificats SSL |40 |1 par écouteur HTTP |
| Certificats d’authentification |40 | |
| Délai minimum d’expiration de la demande |1 seconde | |
| Délai maximum d’expiration de la demande |24 heures | |
| Nombre de sites |40 |1 par écouteur HTTP |
| Mappages d’URL par écouteur |1 | |
| Nombre maximal de règles basées sur le chemin par mappage d’URL|100|
| Configurations de redirection |40| |
| Connexions WebSocket simultanées |5 000| |
|Longueur maximale d’URL|8000|
| Taille de téléchargement maximale Standard |2 Go | |
| Taille de téléchargement maximale WAF |Passerelles WAF moyennes : 100 Mo<br>Grandes passerelles WAF : 500 Mo| |
|Limite de taille de corps de WAF (sans les fichiers)|128 Ko|
