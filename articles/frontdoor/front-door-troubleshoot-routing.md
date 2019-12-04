---
title: Détecter des problèmes de configuration d’Azure Front Door Service
description: Dans ce tutoriel, vous allez apprendre à résoudre vous-même certains des problèmes courants que vous pouvez rencontrer concernant votre porte d’entrée.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: c0d6303620b92368e422b54beab4f9c346d022a5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184555"
---
# <a name="troubleshooting-common-routing-issues"></a>Résolution des problèmes de routage courants
Cet article explique comment résoudre certains des problèmes de routage courants que vous pouvez rencontrer concernant votre configuration Azure Front Door Service. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>Nom d’hôte ne routant pas vers le backend et retournant un code d’état 400


### <a name="symptom"></a>Symptôme
- Vous avez créé une porte d’entrée, mais une requête à l’hôte frontend retourne un code d’état HTTP 400.

  - Vous avez créé un mappage DNS à partir d’un domaine personnalisé à l’hôte frontend que vous avez configuré. Toutefois, l’envoi d’une requête au nom d’hôte du domaine personnalisé retourne un code d’état HTTP 400 et ne semble pas router vers le ou les backends que vous avez configurés.

### <a name="cause"></a>Cause :
- Ce symptôme peut se produire si vous n’avez pas configuré une règle de routage pour le domaine personnalisé que vous avez ajouté comme hôte frontend. Une règle de routage doit être ajoutée explicitement pour cet hôte frontend, même si une règle a déjà été configurée pour l’hôte frontend sous le sous-domaine Front Door (*.azurefd.net) auquel votre domaine personnalisé a un mappage DNS.

### <a name="troubleshooting-steps"></a>Procédure de résolution du problème
- Ajoutez une règle de routage du domaine personnalisé vers le pool de backends de votre choix.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Une requête à un nom d’hôte frontend retourne le code d’état 404

### <a name="symptom"></a>Symptôme
- Vous avez créé une porte d’entrée et configuré un hôte frontend, un pool de backends contenant au moins un backend, ainsi qu’une règle de routage qui connecte l’hôte frontend au pool de backends. Votre contenu ne semble pas être disponible quand une requête est envoyée à l’hôte frontend configuré, car un code d’état HTTP 404 est retourné.

### <a name="cause"></a>Cause :
Ce symptôme peut avoir plusieurs causes :
 - Le backend n’est pas un backend destiné au public et n’est pas visible pour le service Front Door.

- Le backend est mal configuré. De ce fait, le service Front Door envoie la mauvaise requête (autrement dit, votre backend accepte uniquement les requêtes HTTP, mais comme vous n’avez pas désactivé l’autorisation du trafic HTTPS, Front Door tente de transférer des requêtes HTTPS).
- Le backend rejette l’en-tête d’hôte qui a été transféré avec la requête au backend.
- La configuration du backend n’a pas encore été entièrement déployée.

### <a name="troubleshooting-steps"></a>Procédure de résolution du problème
1. Moment du déploiement
    - Veillez attendre environ 10 minutes pour que la configuration soit déployée.

2. Vérifier les paramètres du backend
   - Accédez au pool de backends vers lequel la requête doit router (dépend de la configuration de la règle de routage) et vérifiez que le _type de l’hôte backend_ et que le nom de l’hôte backend sont corrects. Si le backend est un hôte personnalisé, vérifiez que vous l’avez orthographié correctement. 

   - Vérifiez vos ports HTTP et HTTPS. Dans la plupart des cas, 80 et 443 (respectivement) sont appropriés et aucun changement n’est nécessaire. Toutefois, il est possible que votre backend ne soit pas configuré de cette façon et qu’il écoute sur un autre port.

     - Vérifiez _l’en-tête de l’hôte backend_  configuré pour les backends vers lesquels l’hôte frontend doit router. Dans la plupart des cas, cet en-tête doit être identique au _nom de l’hôte backend_. Toutefois, une valeur incorrecte peut provoquer différents codes d’état HTTP 4xx si le backend attend autre chose. Si vous entrez l’adresse IP de votre backend, vous devrez peut-être définir le nom d’hôte du backend comme _en-tête de l’hôte backend_.


3. Vérifier les paramètres de règle de routage
     - Accédez à la règle de routage qui doit router du nom d’hôte frontend en question vers un pool backend. Vérifiez que les protocoles acceptés sont correctement configurés, ou dans le cas contraire, vérifiez que le protocole que Front Door utilisera lors du transfert de la requête est correctement configuré. Les _Protocoles acceptés_ déterminent quelles requêtes Front Door doit accepter et le _Protocole de transfert_ détermine quel protocole Front Door doit utiliser pour transférer la requête au backend.
          - Par exemple, si le backend accepte uniquement les requêtes HTTP, les configurations suivantes sont valides :
               - Les _Protocoles acceptés_ sont HTTP et HTTPS. Le _Protocole de transfert_ est HTTP. La mise en correspondance de la requête ne fonctionnera pas, car HTTPS est un protocole autorisé et si une requête est arrivée en tant que HTTPS, Front Door tente de la transférer en utilisant HTTPS.

               - Les _Protocoles acceptés_ sont HTTP. La valeur de _Protocole de transfert_ est soit la correspondance de la requête, soit HTTPS.

   - Le champ _Réécriture d’URL_ est désactivé par défaut, et vous devez l’utiliser uniquement si vous souhaitez limiter les ressources hébergées par le backend que vous souhaitez rendre disponibles. Quand il est désactivé, Front Door transfère le même chemin de requête que celui qu’il reçoit. Il est possible que ce champ soit mal configuré et que Front Door demande une ressource du backend qui n’est pas disponible, ce qui retourne un code d’état HTTP 404.

