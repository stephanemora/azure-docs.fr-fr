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
ms.openlocfilehash: 0dcb769627714be9da55faf2a8e82c8750789498
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038848"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service - Prise en charge du protocole d’en-têtes HTTP
Ce document décrit le protocole pris en charge par Azure Front Door Service avec les différentes parties du chemin d’appel représentées dans l’image ci-dessous. Les sections suivantes permettent d’en savoir plus sur les en-têtes HTTP pris en charge par Front Door.

![Protocole d’en-têtes HTTP Azure Front Door Service][1]

>[!WARNING]
>Azure Front Door Service n’offre aucune garantie quand aux en-têtes HTTP qui ne sont pas documentés ici.

## <a name="1-client-to-front-door"></a>1. Du client à la porte d’entrée
Front Door accepte la plupart des en-têtes de la requête entrante (sans les modifier). Toutefois, il existe certains en-têtes réservés qui sont supprimés de la requête entrante s’ils sont envoyés. Cela inclut les en-têtes ayant les préfixes suivants :
 - X-FD*
 - X-MS*

## <a name="2-front-door-to-backend"></a>2. De la porte d’entrée au backend

Front Door inclut les en-têtes de la requête entrante, sauf s’ils ont été supprimés en raison des restrictions mentionnées ci-dessus. Front Door ajoute également les en-têtes suivants :

| En-tête  | Exemple et description |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Il s’agit d’une chaîne de référence unique qui identifie une requête prise en charge par Front Door. Elle est essentielle pour la résolution des problèmes, car elle est utilisée pour effectuer des recherches dans les journaux d’accès.|
| X-MS-RequestChain |  *X-MS-RequestChain: hops=1* </br> Il s’agit d’un en-tête que Front Door utilise pour détecter les boucles de requête, et pour lequel les utilisateurs ne doivent pas établir de dépendance. |
| X-MS-Via |  *X-MS-Via: Azure* </br> Il est ajouté par Front Door pour indiquer qu’Azure/Front Door était un destinataire intermédiaire pour la requête entre le client et le backend. |

## <a name="3-front-door-to-client"></a>3. De la porte d’entrée au client

Les en-têtes ci-dessous sont ceux envoyés de la porte d’entrée aux clients. Les en-têtes envoyés à la porte d’entrée à partir du backend sont également transmis au client.

| En-tête  | Exemples |
| ------------- | ------------- |
| X-MS-Ref |  *X-MS-Ref: 0WrHgWgAAAACFupORp/8MS6vxhG/WUvawV1NURURHRTAzMjEARWRnZQ==* </br> Il s’agit d’une chaîne de référence unique qui identifie une requête prise en charge par Front Door. Elle est essentielle pour la résolution des problèmes, car elle est utilisée pour effectuer des recherches dans les journaux d’accès.|

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png