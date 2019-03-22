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
ms.openlocfilehash: b34ab417ab1d9ef77c3141d5aa130c338fb89188
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726326"
---
# <a name="azure-front-door-service---http-headers-protocol-support"></a>Azure Front Door Service - Prise en charge du protocole d’en-têtes HTTP
Ce document décrit le protocole pris en charge par Azure Front Door Service avec les différentes parties du chemin d’appel représentées dans l’image ci-dessous. Les sections suivantes permettent d’en savoir plus sur les en-têtes HTTP pris en charge par Front Door.

![Protocole d’en-têtes HTTP Azure Front Door Service][1]

>[!WARNING]
>Azure Front Door Service n’offre aucune garantie quand aux en-têtes HTTP qui ne sont pas documentés ici.

## <a name="1-client-to-front-door"></a>1. Du client à Front Door
Front Door accepte la plupart des en-têtes de la requête entrante (sans les modifier). Toutefois, il existe certains en-têtes réservés qui sont supprimés de la requête entrante s’ils sont envoyés. Cela inclut les en-têtes ayant les préfixes suivants :
 - X-FD-*

## <a name="2-front-door-to-backend"></a>2. De la porte d’entrée au backend

Front Door inclut les en-têtes de la requête entrante, sauf s’ils ont été supprimés en raison des restrictions mentionnées ci-dessus. Front Door ajoute également les en-têtes suivants :

| En-tête  | Exemple et description |
| ------------- | ------------- |
| Via |  *Par le biais de : 1.1 azure* </br> Porte d’entrée ajoute la version du client HTTP suivie de « Azure » comme valeur pour Via l’en-tête. Cela est ajouté pour indiquer la version du client HTTP et qui porte d’entrée Azure a été un destinataire intermédiaire pour la demande entre le client et le serveur principal.  |
| X-Azure-ClientIP | *X-Azure-ClientIP : 127.0.0.1* </br> Représente l’adresse du protocole Internet de « client » associé à la demande en cours de traitement. Par exemple, une requête en provenance d’un proxy peut ajouter l’en-tête X-Forwarded-For pour indiquer l’adresse IP de l’appelant d’origine. |
| X-Azure-SocketIP |  *X-Azure-SocketIP : 127.0.0.1* </br> Représente l’adresse du protocole Internet de socket associé à la connexion TCP, la requête actuelle provient. Adresse IP du Client d’une requête n’est peut-être pas égal à son adresse IP de Socket, car il peut être arbitrairement remplacé par un utilisateur final.|
| X-Azure-Ref |  *X-Azure-Ref : 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Il s’agit d’une chaîne de référence unique qui identifie une requête prise en charge par Front Door. Elle est essentielle pour la résolution des problèmes, car elle est utilisée pour effectuer des recherches dans les journaux d’accès.|
| X-Azure-RequestChain |  *X-Azure-RequestChain : sauts = 1* </br> Il s’agit d’un en-tête que Front Door utilise pour détecter les boucles de requête, et pour lequel les utilisateurs ne doivent pas établir de dépendance. |
| X-Forwarded-For | *X-Forwarded-For : 127.0.0.1* </br> Le champ d’en-tête x (XFF) HTTP est une méthode courante pour identifier l’adresse IP d’origine d’un client qui se connecte à un serveur web via un équilibreur de charge ou un proxy HTTP. S’il y avait un en-tête XFF existant, puis de la porte d’entrée y ajoute l’adresse IP du socket client autre ajoute l’en-tête XFF avec l’adresse IP du socket client. |
| Hôte X transférés | *Hôte X transféré : contoso.azurefd.net* </br> Le champ d’en-tête X-transférés-Host HTTP est une méthode courante pour identifier l’hôte d’origine demandée par le client dans l’en-tête de demande HTTP de l’hôte, dans la mesure où le nom d’hôte à partir de la porte d’entrée peut-être être différentes pour le serveur principal gère la requête. |
| X-Forwarded-Proto | *X-Forwarded-Proto : http* </br> Le champ d’en-tête X-Forwarded-Proto HTTP est une méthode courante pour identifier le protocole d’origine d’une requête HTTP, étant donné que selon la configuration porte d’entrée peuvent communiquer avec le serveur principal à l’aide de HTTPS même si la demande pour le proxy inverse est HTTP. |

## <a name="3-front-door-to-client"></a>3. De la porte d’entrée au client

Les en-têtes ci-dessous sont ceux envoyés de la porte d’entrée aux clients. Les en-têtes envoyés à la porte d’entrée à partir du backend sont également transmis au client.

| En-tête  | Exemples |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref : 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Il s’agit d’une chaîne de référence unique qui identifie une requête prise en charge par Front Door. Elle est essentielle pour la résolution des problèmes, car elle est utilisée pour effectuer des recherches dans les journaux d’accès.|

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png