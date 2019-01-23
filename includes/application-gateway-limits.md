---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 66dea07a1ff725c6707b19bc6ebdc5563f1b158b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211838"
---
| Ressource | Limite par défaut | Remarque |
| --- | --- | --- |
| Application Gateway |1 000 par abonnement | |
| Configuration d’adresses IP frontales |2 |1 publique et 1 privée |
| Ports frontaux |100<sup>1</sup> | |
| Pool d'adresses principales |100<sup>1</sup> | |
| Serveurs principaux par pool |1 200 | |
| Écouteurs HTTP |100<sup>1</sup> | |
| Règles d’équilibrage de charge HTTP |100<sup>1</sup> | |
| Paramètres HTTP du serveur principal |100<sup>1</sup> | |
| Instances par passerelle |32 | |
| Certificats SSL |100<sup>1</sup> |1 par écouteur HTTP |
| Certificats d’authentification |100 | |
| Certificats racines approuvés |100 | |
| Délai minimum d’expiration de la demande |1 seconde | |
| Délai maximum d’expiration de la demande |24 heures | |
| Nombre de sites |100<sup>1</sup> |1 par écouteur HTTP |
| Mappages d’URL par écouteur |1 | |
| Nombre maximal de règles basées sur le chemin par mappage d’URL|100||
| Configurations de redirection |100<sup>1</sup>| |
| Connexions WebSocket simultanées |5 000| |
| Longueur maximale d’URL|8000||
| Taille de téléchargement maximale Standard |2 Go | |
| Taille de téléchargement maximale WAF |Passerelles WAF moyennes : 100 Mo<br>Grandes passerelles WAF : 500 Mo| |
| Limite de taille de corps de WAF (sans les fichiers)|128 Ko||

<sup>1</sup> En cas de références SKU avec WAF est activé, il vous est conseillé de limiter le nombre de ressources à 40 pour des performances optimales.
