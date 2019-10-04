---
title: Évolutivité et performance - Personalizer
titleSuffix: Azure Cognitive Services
description: 'Les sites web et applications à trafic élevé et hautes performances ont deux principaux facteurs à prendre en considération avec Personalizer pour l’évolutivité et les performances : la latence et le rythme d’apprentissage.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: diberry
ms.openlocfilehash: d116f6bd389b1404ea723c965111cd05880e6c30
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662818"
---
# <a name="scalability-and-performance"></a>Extensibilité et performance

Les sites web et applications à trafic élevé et hautes performances ont deux principaux facteurs à prendre en considération avec Personalizer pour l’évolutivité et les performances :

* Maintenir une faible latence lors des appels à l’API de classement
* S’assurer que le rythme d’apprentissage s’adapte à l’entrée des événements

La personnalisation peut retourner un classement très rapidement, la plus grande partie de la durée de l’appel étant dédiée à la communication via l’API REST. Azure mettra à l’échelle la capacité à répondre rapidement aux demandes.

##  <a name="low-latency-scenarios"></a>Scénarios de faible latence

Certaines applications nécessitent des latences faibles lors du retour d’un classement. Cela est nécessaire :

* Pour empêcher l’utilisateur d’avoir à attendre avant d’afficher un contenu classé.
* Pour aider un serveur qui subit un trafic extrême à éviter de mobiliser le temps de calcul et les connexions réseau.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Extensibilité et rythme d’apprentissage

Personalizer fonctionne en mettant à jour un modèle qui est reformé en fonction des messages envoyés de façon asynchrone par Personalizer après les API de classement et de récompense. Ces messages sont envoyés à l’aide d’un EventHub Azure pour l’application.

 La plupart des applications n’atteindront probablement pas les niveaux de jointure et de rythme d’apprentissage maximaux de Personalizer. Bien qu’atteindre ce maximum ne ralentirait pas l’application, cela signifierait que les files d’attente d’Event Hub se rempliraient en interne plus rapidement qu’elles peuvent être nettoyées.

## <a name="how-to-estimate-your-throughput-requirements"></a>Comment estimer vos besoins en débit

* Estimez le nombre moyen d’octets par événement de classement en ajoutant les longueurs des documents JSON de contexte et d’action.
* Divisez 20 Mo par seconde par ce nombre moyen d’octets estimé.

Par exemple, si votre charge utile moyenne comprend 500 fonctionnalités et que chacune est estimée à 20 caractères, chaque événement est d’environ 10 ko. Avec ces estimations, 20 000 000/10 000 = 2 000 événements par seconde, ce qui équivaut à environ 173 millions d’événements par jour. 

Si vous atteignez ces limites, contactez notre équipe de support pour des conseils d’architecture.

## <a name="next-steps"></a>Étapes suivantes

[Créer et configurer Personalizer](how-to-settings.md).