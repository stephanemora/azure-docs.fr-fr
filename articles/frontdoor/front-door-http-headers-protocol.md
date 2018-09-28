---
title: Azure Front Door Service - Prise en charge du protocole d’en-têtes HTTP | Microsoft Docs
description: Cet article vous aide à comprendre le protocole d’en-têtes HTTP pris en charge par Front Door
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: efb3ffeb2e5fdde79fe3741377b8a06074671fea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951132"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service - Prise en charge du protocole d’en-têtes HTTP
Ce document décrit le protocole pris en charge par Azure Front Door Service avec les différentes parties du chemin d’appel représentées dans l’image ci-dessous. Les sections suivantes permettent d’en savoir plus sur les en-têtes HTTP pris en charge par Front Door.

![Protocole d’en-têtes HTTP Azure Front Door Service][1]

>[!WARNING]
>Azure Front Door Service n’offre aucune garantie quand aux en-têtes HTTP qui ne sont pas documentés ici.

## <a name="1-client-to-front-door"></a>1. Du client à la porte d’entrée

Les en-têtes suivants seront analysés par la porte d’entrée. Pour tous les autres en-têtes, la porte d’entrée fait office d’intermédiaire et envoie les en-têtes au backend d’application.

| En-tête  | Exemple et description |
| ------------- | ------------- |
| X-MSEdge-IG  | *X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br> GUID qui identifie une ou plusieurs requêtes HTTP visant à afficher une page web unique. La porte d’entrée transfère le GUID au backend en utilisant un en-tête X-FD-ImpressionGuid. </br> Si le client n’envoie pas cet en-tête, la porte d’entrée génère automatiquement un nouveau GUID et l’envoie au backend. |

## <a name="2-front-door-to-backend"></a>2. De la porte d’entrée au backend

Les en-têtes suivants sont envoyés de la porte d’entrée au backend d’application.

| En-tête  | Exemple et description |
| ------------- | ------------- |
| X-MS-Ref |  *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> ID identifiant une requête traitée par la porte d’entrée. Cette valeur d’en-tête représente l’information la plus importante pour la résolution de problèmes. Cet ID permet de rechercher les journaux d’accès. La porte d’entrée envoie le même en-tête au client et au backend. |
| X-FD-ClientHttpVersion | *X-FD-ClientHttpVersion: 1.1* </br>Version du protocole HTTP entre le client et la porte d’entrée. Les valeurs possibles sont : 1.1, 2.0, etc. |
| X-FD-ClientIP | *X-FD-ClientIP: 131.107.192.35* </br>Adresse du protocole Internet « client » associé à la demande traitée. |
| X-FD-EdgeEnvironment | *X-FD-EdgeEnvironment: Edge-Prod-WSTr3* </br>Nœud Front Door qui a traité la requête. Cet en-tête peut être journalisé par le backend pour faciliter le débogage. Cependant, il ne doit exister aucune dépendance vis-à-vis des conventions d’affectation de noms Front Door, de la distribution du trafic entre les nœuds Front Door ou de la disponibilité d’un nœud individuel spécifique. |
| X-FD-EventID | *X-FD-EventId: FB805145C0124400915BE0E180F3A159* </br>Identificateur unique (GUID) créé au début du traitement de la demande. Les journaux d’accès éventuellement créées pendant le traitement de cette demande sont associés au même GUID. |
| X-FD-ImpressionGuid | *X-FD-ImpressionGuid: 2BC2B67F49ED47DFA47FBE2E962AC81F* </br>GUID qui identifie une ou plusieurs requêtes HTTP visant à afficher une page web unique. Côté client, ce GUID peut être spécifié des façons suivantes : </br>- En envoyant l’en-tête de demande *X-MSEdge-IG*. Par exemple : X-MSEdge-IG: 2BC2B67F49ED47DFA47FBE2E962AC81F </br>- En envoyant la chaîne de requête « ig ». Par exemple : *?ig=2BC2B67F49ED47DFA47FBE2E962AC81F*. Si le client n’envoie pas un GUID, la porte d’entrée en génère un nouveau. |
| X-FD-OriginalURL | *X-FD-OriginalURL: http://www.contoso.com:80/* </br> URL de la demande d’origine formulée par le client. |
| X-FD-Partner | *X-FD-Partner: ARM-contoso.azurefd.net_Default* </br>Identificateur du profil Front Door pour lequel la demande est traitée. Peut être utilisé par les backends qui hébergent plusieurs applications pour différentes portes d’entrée comme un moyen de déterminer la configuration de porte d’entrée pour laquelle ils traitent la demande. |
| X-FD-RequestHadClientId | Réservé |
| X-FD-ResponseHasClientId | Réservé |
| X-FD-SocketIP | *X-FD-SocketIP: 131.107.192.35* </br>Identique à l’en-tête X-FD-ClientIP décrit plus haut |

## <a name="3-front-door-to-client"></a>3. De la porte d’entrée au client

Les en-têtes ci-dessous sont ceux envoyés de la porte d’entrée aux clients. Les en-têtes envoyés à la porte d’entrée à partir du backend sont également transmis au client.

| En-tête  | Exemples |
| ------------- | ------------- |
| X-MS-Ref  | *X-FD-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br>ID identifiant une demande traitée par la porte d’entrée. Cette valeur d’en-tête représente l’information la plus importante pour la résolution de problèmes. Cet ID permet de rechercher les journaux d’accès. La porte d’entrée envoie le même en-tête au client et au backend. |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer un porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne une porte d’entrée](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png