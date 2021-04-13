---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: c98d88d66e89df3efe186212d8c004ec2a65158d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106450562"
---
| Ressource | Limite | Remarque |
| --- | --- | --- |
| Azure Application Gateway |1 000 par abonnement | |
| Configuration d’adresses IP frontales |2 |1 publique et 1 privée |
| Ports frontaux |100<sup>1</sup> | |
| Pool d'adresses principales |100<sup>1</sup> | |
| Serveurs principaux par pool |1,200 | |
| Écouteurs HTTP |200<sup>1</sup> |Limité à 100 écouteurs actifs qui routent le trafic. Écouteurs actifs = nombre total d’écouteurs - écouteurs non actifs.<br>Si une configuration par défaut à l’intérieur d’une règle de routage est définie pour router le trafic (avec, par exemple, un écouteur, un pool de back-ends et des paramètres HTTP), elle est également considérée comme un écouteur.|
| Règles d’équilibrage de charge HTTP |100<sup>1</sup> | |
| Paramètres HTTP du serveur principal |100<sup>1</sup> | |
| Instances par passerelle |Référence SKU V1 - 32<br>Référence SKU V2 - 125 | |
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
| Nombre d’ensembles de règles de réécriture |400| |
| Nombre de configurations d’en-tête ou d’URL par ensemble de règles de réécriture|40| |
| Nombre de conditions par ensemble de règles de réécriture|40| |
| Connexions WebSocket simultanées |Passerelles moyennes 20 000<sup>2</sup><br> Grandes passerelles 50 000<sup>2</sup>| |
| Longueur maximale d’URL|32 Ko| |
| Taille d’en-tête maximale pour HTTP/2 |4 Ko| |
| Taille de téléchargement maximale Standard |2 Go | |
| Taille de téléchargement maximale WAF |Passerelles v1 WAF moyennes, 100 Mo<br>Grandes passerelles v1 WAF, 500 Mo<br>WAF v2, 750 Mo| |
| Limite de taille de corps de WAF, sans les fichiers|128 Ko||
| Règles personnalisées WAF maximales|100||
| Exclusions WAF maximales par passerelle applicative|40||

<sup>1</sup> Dans le cas des références SKU compatibles WAF, vous devez limiter le nombre de ressources à 40.

<sup>2</sup> La limite est par instance Application Gateway, et non par ressource Application Gateway.
