---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 0ba21fe3789fba03cd4814d19fb103c3a2559c13
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75662904"
---
| Ressource | Limite par défaut/maximale | Remarque |
| --- | --- | --- |
| Azure Application Gateway |1 000 par abonnement | |
| Configuration d’adresses IP frontales |2 |1 publique et 1 privée |
| Ports frontaux |100<sup>1</sup> | |
| Pool d'adresses principales |100<sup>1</sup> | |
| Serveurs principaux par pool |1,200 | |
| Écouteurs HTTP |100<sup>1</sup> | |
| Règles d’équilibrage de charge HTTP |100<sup>1</sup> | |
| Paramètres HTTP du serveur principal |100<sup>1</sup> | |
| Instances par passerelle |32 | |
| Certificats SSL |100<sup>1</sup> |1 par écouteur HTTP |
| Taille maximale du certificat SSL |RÉFÉRENCE SKU V1 : 10 Ko<br>RÉFÉRENCE SKU V2 : 16 Ko| |
| Certificats d’authentification |100 | |
| Certificats racines approuvés |100 | |
| Délai minimum d’expiration de la demande |1 seconde | |
| Délai maximum d’expiration de la demande |24 heures | |
| Nombre de sites |100<sup>1</sup> |1 par écouteur HTTP |
| Mappages d’URL par écouteur |1 | |
| Nombre maximal de règles basées sur le chemin par mappage d’URL|100||
| Configurations de redirection |100<sup>1</sup>| |
| Connexions WebSocket simultanées |Passerelles moyenne 20 Ko<br> Passerelles volumineuses 50 Ko| |
| Longueur maximale d’URL|32 Ko| |
| Taille d’en-tête maximale pour HTTP/2 |4 Ko| |
| Taille de téléchargement maximale Standard |2 Go | |
| Taille de téléchargement maximale WAF |Passerelles v1 WAF moyennes, 100 Mo<br>Grandes passerelles v1 WAF, 500 Mo<br>WAF v2, 750 Mo| |
| Limite de taille de corps de WAF, sans les fichiers|128 Ko||
| Règles personnalisées WAF maximales|100||
| Nombre maximal d’exclusions WAF|100||

<sup>1</sup> En cas de références SKU avec WAF activé, nous vous recommandons de limiter le nombre de ressources à 40 pour des performances optimales.
