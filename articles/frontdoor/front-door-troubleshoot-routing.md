---
title: Résoudre les problèmes de configuration d’Azure Front Door
description: Dans ce tutoriel, vous allez apprendre à résoudre vous-même certains des problèmes courants que vous pouvez rencontrer concernant votre porte d’entrée.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: f4310350e83284d6a2839f8c3d86526d3cda74ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743573"
---
# <a name="troubleshooting-common-routing-issues"></a>Résolution des problèmes de routage courants

Cet article explique comment résoudre certains des problèmes de routage courants que vous pouvez rencontrer concernant votre configuration Azure Front Door.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>Réponse 503 de Front Door après quelques secondes

### <a name="symptom"></a>Symptôme

- Les demandes ordinaires envoyées au back-end sans passer par Front Door réussissent, mais le passage par Front Door génère des réponses d’erreur 503.

- L’échec lié à Front Door s’affiche après quelques secondes (généralement après 30 secondes environ)

### <a name="cause"></a>Cause

Ce problème se produit quand le back-end met plus de temps que ne le prévoit la configuration du délai d’expiration (par défaut, 30 secondes) pour recevoir la demande provenant de Front Door ou s’il met plus que cette valeur de temps pour envoyer une réponse à la demande provenant de Front Door. 

### <a name="troubleshooting-steps"></a>Étapes de dépannage

- Envoyez la demande directement au back-end (sans passer par Front Door) et vérifiez le temps de réponse habituel du back-end.
- Envoyez la demande via Front Door et vérifiez si vous voyez des réponses 503. Si ce n’est pas le cas, il est possible qu’il ne s’agisse pas d’un problème de délai d’expiration. Contactez le support.
- Si le passage par Front Door entraîne le code de réponse d’erreur 503, configurez le champ sendReceiveTimeout pour votre porte d’entrée de manière à étendre le délai d’expiration par défaut jusqu’à 4 minutes (240 secondes). Le paramètre se trouve sous `backendPoolSettings` et est appelé `sendRecvTimeoutSeconds`. 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Les demandes envoyées au domaine personnalisé retournent le code d’état 400

### <a name="symptom"></a>Symptôme

- Vous avez créé une porte d’entrée, mais une demande au domaine ou à l’hôte front-end retourne un code d’état HTTP 400.

- Vous avez créé un mappage DNS à partir d’un domaine personnalisé à l’hôte frontend que vous avez configuré. Toutefois, l’envoi d’une requête au nom d’hôte du domaine personnalisé retourne un code d’état HTTP 400 et ne semble pas router vers le ou les backends que vous avez configurés.

### <a name="cause"></a>Cause

Ce symptôme peut se produire si vous n’avez pas configuré une règle de routage pour le domaine personnalisé que vous avez ajouté comme hôte frontend. Une règle de routage doit être ajoutée explicitement pour cet hôte frontend, même si une règle a déjà été configurée pour l’hôte frontend sous le sous-domaine Front Door (*.azurefd.net) auquel votre domaine personnalisé a un mappage DNS.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

Ajoutez une règle de routage du domaine personnalisé vers le pool de backends de votre choix.

## <a name="front-door-is-not-redirecting-http-to-https"></a>Front Door ne redirige pas le trafic HTTP vers HTTPS

### <a name="symptom"></a>Symptôme

Votre porte d’entrée a une règle de routage qui indique de rediriger le trafic HTTP vers HTTPS, mais l’accès au domaine conserve toujours HTTP comme protocole.

### <a name="cause"></a>Cause

Ce comportement peut se produire si vous n’avez pas correctement configuré les règles de routage pour votre porte d’entrée. En gros, votre configuration actuelle n’est pas spécifique et a peut-être des règles en conflit.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Une requête à un nom d’hôte frontend retourne le code d’état 404

### <a name="symptom"></a>Symptôme

- Vous avez créé une porte d’entrée et configuré un hôte frontend, un pool de backends contenant au moins un backend, ainsi qu’une règle de routage qui connecte l’hôte frontend au pool de backends. Votre contenu ne semble pas être disponible quand une requête est envoyée à l’hôte frontend configuré, car un code d’état HTTP 404 est retourné.

### <a name="cause"></a>Cause

Ce symptôme peut avoir plusieurs causes :

- Le back-end n’est pas un back-end destiné au public et n’est pas visible pour la porte d’entrée.
- Le back-end est mal configuré. De ce fait, la porte d’entrée envoie la mauvaise requête (autrement dit, votre back-end accepte uniquement les requêtes HTTP, mais comme vous n’avez pas désactivé l’autorisation du trafic HTTPS, Front Door tente de transférer des requêtes HTTPS).
- Le backend rejette l’en-tête d’hôte qui a été transféré avec la requête au backend.
- La configuration du backend n’a pas encore été entièrement déployée.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

1. Moment du déploiement
   - Veillez attendre environ 10 minutes pour que la configuration soit déployée.

2. Vérifier les paramètres du backend
    - Accédez au pool de backends vers lequel la requête doit router (dépend de la configuration de la règle de routage) et vérifiez que le _type de l’hôte backend_ et que le nom de l’hôte backend sont corrects. Si le backend est un hôte personnalisé, vérifiez que vous l’avez orthographié correctement. 

    - Vérifiez vos ports HTTP et HTTPS. Dans la plupart des cas, 80 et 443 (respectivement) sont appropriés et aucun changement n’est nécessaire. Toutefois, il est possible que votre backend ne soit pas configuré de cette façon et qu’il écoute sur un autre port.

        - Vérifiez _l’en-tête de l’hôte backend_  configuré pour les backends vers lesquels l’hôte frontend doit router. Dans la plupart des cas, cet en-tête doit être identique au _nom de l’hôte backend_. Toutefois, une valeur incorrecte peut provoquer différents codes d’état HTTP 4xx si le backend attend autre chose. Si vous entrez l’adresse IP de votre backend, vous devrez peut-être définir le nom d’hôte du backend comme _en-tête de l’hôte backend_.


3. Vérifier les paramètres de règle de routage
    - Accédez à la règle de routage qui doit router du nom d’hôte frontend en question vers un pool backend. Vérifiez que les protocoles acceptés sont correctement configurés, ou dans le cas contraire, vérifiez que le protocole que Front Door utilisera lors du transfert de la requête est correctement configuré. Le champ _Protocoles acceptés_ détermine quelles requêtes Front Door doit accepter et le _Protocole de transfert_ détermine quel protocole Front Door doit utiliser pour transférer la requête au back-end.
         - Par exemple, si le backend accepte uniquement les requêtes HTTP, les configurations suivantes sont valides :
            - Les _Protocoles acceptés_ sont HTTP et HTTPS. Le _Protocole de transfert_ est HTTP. La mise en correspondance de la requête ne fonctionnera pas, car HTTPS est un protocole autorisé et si une requête est arrivée en tant que HTTPS, Front Door tente de la transférer en utilisant HTTPS.

            - Les _Protocoles acceptés_ sont HTTP. La valeur de _Protocole de transfert_ est soit la correspondance de la requête, soit HTTPS.

    - Le champ _Réécriture d’URL_ est désactivé par défaut, et vous devez l’utiliser uniquement si vous souhaitez limiter les ressources hébergées par le backend que vous souhaitez rendre disponibles. Quand il est désactivé, Front Door transfère le même chemin de requête que celui qu’il reçoit. Il est possible que ce champ soit mal configuré et que Front Door demande une ressource du backend qui n’est pas disponible, ce qui retourne un code d’état HTTP 404.

