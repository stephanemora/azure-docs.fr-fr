---
title: Prise en charge pour les en-têtes HTTP dans Azure porte Service de protocole | Microsoft Docs
description: Cet article décrit les protocoles d’en-tête HTTP qui prend en charge du Service de la porte d’entrée.
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
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861840"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Prise en charge de protocole pour les en-têtes HTTP dans le Service de porte d’entrée Azure
Cet article décrit le protocole prend en charge de Service de la porte d’entrée avec le chemin d’appel (voir l’image). Les sections suivantes fournissent plus d’informations sur les en-têtes HTTP pris en charge par le Service de la porte d’entrée.

![Protocole d’en-têtes HTTP Azure Front Door Service][1]

>[!IMPORTANT]
>Service de la porte d’entrée ne certifier les en-têtes HTTP qui ne sont pas documentées ici.

## <a name="client-to-front-door-service"></a>Client de Service de la porte d’entrée
Service de la porte d’entrée accepte la plupart des en-têtes de la demande entrante sans les modifier. Certains en-têtes réservés sont supprimés de la demande entrante si envoyé, y compris les en-têtes avec un X - FD-* préfixe.

## <a name="front-door-service-to-backend"></a>Service de porte d’entrée vers le serveur principal

Service de la porte d’entrée inclut les en-têtes à partir d’une demande entrante, sauf si supprimée en raison de restrictions. Porte d’entrée ajoute également les en-têtes suivants :

| En-tête  | Exemple et description |
| ------------- | ------------- |
| Via |  Par le biais de : 1.1 azure </br> Porte d’entrée ajoute la version du client HTTP suivie *Azure* comme valeur pour l’en-tête Via. Cela indique la version du client HTTP et qui porte d’entrée a été un destinataire intermédiaire pour la demande entre le client et le serveur principal.  |
| X-Azure-ClientIP | X-Azure-ClientIP : 127.0.0.1 </br> Représente l’adresse IP du client associé à la demande en cours de traitement. Par exemple, une requête en provenance d’un proxy peut ajouter l’en-tête X-Forwarded-For pour indiquer l’adresse IP de l’appelant d’origine. |
| X-Azure-SocketIP |  X-Azure-SocketIP : 127.0.0.1 </br> Représente l’adresse IP de socket associé à la connexion TCP qui provient de la requête actuelle. Adresse IP du client d’une requête n’est peut-être pas égal à son adresse IP de socket, car il peut être arbitrairement remplacé par un utilisateur.|
| X-Azure-Ref |  X-Azure-Ref : 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Une chaîne de référence unique qui identifie une demande pris en charge par la porte d’entrée. Il est utilisé pour rechercher les journaux d’accès et critique pour la résolution des problèmes.|
| X-Azure-RequestChain |  X-Azure-RequestChain : sauts = 1 </br> Un en-tête que porte d’entrée pour détecter les boucles de demande, et les utilisateurs ne doivent pas prendre une dépendance sur celui-ci. |
| X-Forwarded-For | X-Forwarded-For : 127.0.0.1 </br> Souvent, le champ d’en-tête x (XFF) HTTP identifie l’adresse IP d’origine d’un client qui se connecte à un serveur web via un équilibreur de charge ou un proxy HTTP. S’il existe un en-tête XFF existant, la porte d’entrée y ajoute l’adresse IP du socket client ou ajoute l’en-tête XFF avec l’adresse IP du socket client. |
| Hôte X transférés | Hôte X transféré : contoso.azurefd.net </br> Le champ d’en-tête X-transférés-Host HTTP est une méthode courante utilisée pour identifier l’hôte d’origine demandée par le client dans l’en-tête de demande HTTP de l’hôte. Il s’agit, car le nom d’hôte à partir de la porte d’entrée peut-être être différentes pour le serveur principal gère la requête. |
| X-Forwarded-Proto | X-Forwarded-Proto : http </br> Le champ d’en-tête X-Forwarded-Proto HTTP est souvent utilisé pour identifier le protocole d’origine d’une requête HTTP, car la porte d’entrée, en fonction de configuration, peut communiquer avec le serveur principal à l’aide de HTTPS. Cela est vrai même si la demande pour le proxy inverse est HTTP. |

## <a name="front-door-service-to-client"></a>Service de porte d’entrée au client

Tous les en-têtes envoyés à la porte d’entrée depuis le serveur principal sont également transmises au client. En-têtes envoyés à partir de la porte d’entrée pour les clients sont les suivantes :

| En-tête  | Exemples |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref : 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Il s’agit d’une chaîne de référence unique qui identifie une requête prise en charge par Front Door. Cela est essentiel pour la résolution des problèmes qu’il est utilisé pour rechercher les journaux d’accès.|

## <a name="next-steps"></a>Étapes suivantes

- [Créer une Front Door](quickstart-create-front-door.md)
- [Fonctionnement de la porte d’entrée](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png