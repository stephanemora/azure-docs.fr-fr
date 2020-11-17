---
title: Résoudre les problèmes de configuration d’Azure Front Door
description: Dans ce tutoriel, vous allez apprendre à résoudre vous-même certains des problèmes courants que vous pouvez rencontrer concernant votre porte d’entrée.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 8e810a31fab4457e47329e37f54b16e6f488c9da
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337625"
---
# <a name="troubleshooting-common-routing-issues"></a>Résolution des problèmes de routage courants

Cet article explique comment résoudre certains des problèmes de routage courants que vous pouvez rencontrer concernant votre configuration Azure Front Door.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>Réponse 503 de Front Door après quelques secondes

### <a name="symptom"></a>Symptôme

* Les demandes ordinaires envoyées au back-end sans passer par Front Door réussissent, mais le passage par Front Door génère des réponses d’erreur 503.
* L’échec lié à Front Door s’affiche après quelques secondes (généralement après 30 secondes environ)

### <a name="cause"></a>Cause

La cause de ce problème peut être l’une des deux possibilités suivantes :
 
* Votre back-end prend plus de temps que le délai d’expiration configuré (la valeur par défaut est de 30 secondes) pour recevoir la requête de l’instance Front Door.
* Le temps nécessaire pour envoyer une réponse à la requête provenant de Front Door prend plus de temps que la valeur du délai d’attente. 

### <a name="troubleshooting-steps"></a>Étapes de dépannage

* Envoyez la demande directement au back-end (sans passer par Front Door) et vérifiez le temps de réponse habituel du back-end.
* Envoyez la requête via Front Door et vérifiez si vous obtenez des réponses 503. Si ce n’est pas le cas, il est possible qu’il ne s’agisse pas d’un problème de délai d’expiration. Contactez le support technique.
* Si le passage par Front Door aboutit au code de réponse d’erreur 503, configurez le champ `sendReceiveTimeout` pour votre instance Front Door. Vous pouvez étendre le délai d’expiration par défaut jusqu’à 4 minutes (240 secondes). Le paramètre se trouve sous `backendPoolSettings` et est appelé `sendRecvTimeoutSeconds`. 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Les demandes envoyées au domaine personnalisé retournent le code d’état 400

### <a name="symptom"></a>Symptôme

* Vous avez créé une instance Front Door, mais une requête auprès du domaine ou de l’hôte front-end retourne un code d’état HTTP 400.
* Vous avez créé un mappage DNS pour un domaine personnalisé vers l’hôte front-end que vous avez configuré. Toutefois, l’envoi d’une requête auprès du nom d’hôte du domaine personnalisé retourne un code d’état HTTP 400. Ce qui ne semble pas router vers le back-end que vous avez configuré.

### <a name="cause"></a>Cause

Le problème se produit si vous n’avez pas configuré de règle de routage pour le domaine personnalisé qui a été ajouté en tant qu’hôte front-end. Une règle de routage doit être explicitement ajoutée pour cet hôte front-end. Et ce, même si une règle a déjà été configurée pour l’hôte front-end sous le sous-domaine Front Door (*.azurefd.net).

### <a name="troubleshooting-steps"></a>Étapes de dépannage

Ajoutez une règle de routage pour le domaine personnalisé, afin de diriger le trafic vers le pool de back-ends sélectionné.

## <a name="front-door-doesnt-redirect-http-to-https"></a>Front Door ne redirige pas HTTP vers HTTPS

### <a name="symptom"></a>Symptôme

Votre porte d’entrée a une règle de routage qui indique de rediriger le trafic HTTP vers HTTPS, mais l’accès au domaine conserve toujours HTTP comme protocole.

### <a name="cause"></a>Cause

Ce comportement peut se produire si vous n’avez pas configuré les règles de routage correctement pour votre instance Front Door. En gros, votre configuration actuelle n’est pas spécifique et a peut-être des règles en conflit.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Une requête à un nom d’hôte frontend retourne le code d’état 404

### <a name="symptom"></a>Symptôme

 Vous avez créé une instance Front Door en configurant un hôte front-end, un pool de back-ends contenant au moins un back-end, ainsi qu’une règle de routage qui connecte l’hôte front-end au pool de back-ends. Votre contenu n’est pas disponible quand vous effectuez une requête auprès de l’hôte front-end configuré, par conséquent un code d’état HTTP 404 est retourné.

### <a name="cause"></a>Cause

Ce symptôme peut avoir plusieurs causes :

* Le back-end n’est pas un back-end destiné au public et n’est pas visible pour l’instance Front Door.
* Le back-end n’est pas correctement configuré, ce qui amène l’instance Front Door à envoyer une requête incorrecte. En d’autres termes, votre back-end accepte uniquement le protocole HTTP et vous n’avez pas désactivé l’autorisation du protocole HTTPS. Front Door tente donc de transférer des requêtes HTTPS.
* Le backend rejette l’en-tête d’hôte qui a été transféré avec la requête au backend.
* La configuration du back-end n’a pas encore été entièrement déployée.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

1. Moment du déploiement
   * Veillez à attendre environ 10 minutes pour que la configuration soit déployée.

2. Vérifier les paramètres du backend
    * Accédez au pool de back-ends vers lequel la requête doit router (dépend de la configuration de la règle de routage). Vérifiez que le *type de l’hôte back-end* et le nom de l’hôte back-end sont corrects. Si le back-end est un hôte personnalisé, vérifiez que vous l’avez orthographié correctement. 

    * Vérifiez vos ports HTTP et HTTPS. Dans la plupart des cas, 80 et 443 (respectivement) sont appropriés et aucun changement n’est nécessaire. Toutefois, il est possible que votre back-end ne soit pas configuré de cette façon et qu’il écoute sur un autre port.

        * Vérifiez _l’en-tête de l’hôte backend_  configuré pour les backends vers lesquels l’hôte frontend doit router. Dans la plupart des cas, cet en-tête doit être identique au *nom de l’hôte backend*. Toutefois, une valeur incorrecte peut provoquer différents codes d’état HTTP 4xx si le backend attend autre chose. Si vous entrez l’adresse IP de votre backend, vous devrez peut-être définir le nom d’hôte du backend comme *en-tête de l’hôte backend*.

3. Vérifier les paramètres de la règle de routage
    * Accédez à la règle de routage qui doit router du nom d’hôte frontend en question vers un pool backend. Assurez-vous que les protocoles acceptés sont configurés correctement lors du transfert de la requête. Le champ des *protocoles acceptés* détermine quelles requêtes Front Door doit accepter. Le *protocole de transfert* détermine quel protocole Front Door doit utiliser pour transférer la requête au back-end.
         * Par exemple, si le backend accepte uniquement les requêtes HTTP, les configurations suivantes sont valides :
            * Les *Protocoles acceptés* sont HTTP et HTTPS. Le *Protocole de transfert* est HTTP. La mise en correspondance de la requête ne fonctionnera pas, car HTTPS est un protocole autorisé et si une requête est arrivée sous la forme HTTPS, Front Door tentera de la transférer en utilisant HTTPS.

            * Les *Protocoles acceptés* sont HTTP. La valeur de *Protocole de transfert* est soit la correspondance de la requête, soit HTTP.
    - Le champ *Réécriture d’URL* est désactivé par défaut. Ce champ n’est utilisé que si vous voulez limiter l’étendue des ressources que vous souhaitez rendre disponibles et qui sont hébergées par le back-end. Quand il est désactivé, Front Door transfère le même chemin de requête que celui qu’il reçoit. Il est possible de configurer ce champ de façon incorrecte. Ainsi, lorsque Front Door demande une ressource du back-end qui n’est pas disponible, un code d’état HTTP 404 est retourné.

## <a name="request-to-frontend-host-name-returns-411-status-code"></a>Une demande à un nom d’hôte frontal retourne le code d’état 411

### <a name="symptom"></a>Symptôme

Vous avez créé une porte d’entrée et configuré un hôte frontend, un pool de backends contenant au moins un backend, ainsi qu’une règle de routage qui connecte l’hôte frontend au pool de backends. Votre contenu ne semble pas être disponible quand une demande est envoyée à l’hôte frontal configuré, car un code d’état HTTP 411 est retourné.

Les réponses à ces demandes peuvent également contenir une page d’erreur HTML dans le corps de la réponse, qui inclut une déclaration d’explication. Par exemple : `HTTP Error 411. The request must be chunked or have a content length`

### <a name="cause"></a>Cause

Ce symptôme peut avoir plusieurs causes. Toutefois, la raison est généralement que votre requête HTTP n’est pas entièrement conforme au RFC. 

Un exemple de non-conformité est une demande `POST` envoyée sans en-tête `Content-Length` ou `Transfer-Encoding` (par exemple, à l’aide de `curl -X POST https://example-front-door.domain.com`). Cette demande ne répond pas aux exigences du [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2) et serait bloquée par votre Front Door avec une réponse HTTP 411.

Ce comportement est indépendant de la fonctionnalité WAF de Front Door. Actuellement, il n’existe aucun moyen de désactiver ce comportement. Toutes les requêtes HTTP doivent répondre aux exigences, même si la fonctionnalité WAF n’est pas utilisée.

### <a name="troubleshooting-steps"></a>Étapes de dépannage

- Vérifiez que vos demandes sont conformes aux exigences des RFC applicables.

- Notez tout corps de message HTML retourné en réponse à votre demande, car celui-ci explique souvent précisément *pourquoi* votre demande n’est pas conforme.
