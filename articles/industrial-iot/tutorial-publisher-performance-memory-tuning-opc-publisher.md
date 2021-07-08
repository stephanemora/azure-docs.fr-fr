---
title: Réglage de la mémoire et des performances de Microsoft OPC Publisher
description: Dans ce tutoriel, vous allez apprendre à régler la mémoire et les performances d’OPC Publisher.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 98bff6a72d35e2cee3157b997796bbe51795e1ea
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110677852"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>Tutoriel : Réglage de la mémoire et des performances d’OPC Publisher

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Régler les performances
> * Régler le flux de messages vers les ressources mémoire

Lors de l’exécution d’OPC Publisher dans les configurations de production, les besoins de performances réseau (débit et latence) et les ressources mémoire doivent être pris en compte. OPC Publisher expose les paramètres de ligne de commande suivants pour répondre à ces besoins :

* Capacité de la file d’attente des messages (`mq` pour les versions 2.5 et antérieures, non disponible dans la version 2.6, `om` pour la version 2.7)
* Intervalle d’envoi à IoT Hub (`si`)
* Taille des messages IoT Hub (`ms`)

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>Réglage de l’intervalle d’envoi à IoT Hub et de la taille des messages IoT Hub

Le paramètre `mq/om` contrôle la limite supérieure de la capacité de la file d’attente de messages interne. Cette file d’attente met en mémoire tampon tous les messages avant leur envoi à IoT Hub. La taille par défaut de la file d’attente est de 2 Mo pour OPC Publisher versions 2.5 et antérieures et 4000 messages IoT Hub pour la version 2.7 (autrement dit, si le paramètre de la taille des messages IoT Hub est de 256 Ko, la taille de la file d’attente peut atteindre 1 Go). Si OPC Publisher n’est pas en mesure d’envoyer des messages à IoT Hub suffisamment rapidement, le nombre d’éléments de cette file d’attente augmente. Si cela se produit pendant les séries de tests, vous pouvez effectuer l’une des opérations suivantes, ou les deux, en guise d’atténuation :

* Réduire l’intervalle d’envoi à IoT Hub (`si`)

* Augmenter la taille des messages IoT Hub (`ms`, la valeur maximale pouvant être définie à 256 Ko). Dans la version 2.7 ou une version ultérieure, la valeur par défaut est déjà définie sur 256 Ko.

Si la file d’attente continue de croître même si les paramètres `si` et `ms` ont été réglés, la capacité maximale de la file d’attente finit par être atteinte et les messages perdus. En effet, les paramètre `si` et `ms` ont des limites physiques et la connexion Internet entre OPC Publisher et IoT Hub n’est pas assez rapide pour le nombre de messages qui doivent être envoyés dans un scénario donné. Dans ce cas, le simple fait d’installer plusieurs serveurs OPC Publisher parallèles s’avère utile. Le paramètre `mq/om` a également l’impact le plus important sur la consommation de mémoire par OPC Publisher. 

Le paramètre `si` force OPC Publisher à envoyer des messages à IoT Hub à l’intervalle spécifié. Un message est envoyé quand la taille de message IoT Hub maximale de 256 Ko de données est disponible (déclenchant la réinitialisation de l’intervalle d’envoi) ou que l’intervalle de temps spécifié s’est écoulé.

Le paramètre `ms` permet le traitement par lot des messages envoyés à IoT Hub. Dans la plupart des configurations réseau, la latence de l’envoi d’un message unique à IoT Hub est élevée, par rapport au temps nécessaire pour transmettre la charge utile. Cela est principalement dû aux exigences de qualité de service (QoS), car les messages ne sont reconnus qu’une fois traités par IoT Hub). Ainsi, si un délai est acceptable quant à l’arrivée des données à IoT Hub, vous devez affecter la valeur 0 au paramètre `ms` afin de configurer OPC Publisher pour qu’il utilise la taille de message maximale de 256 Ko. C’est en outre la méthode la plus économique pour utiliser OPC Publisher.

Dans la version 2.5, la configuration par défaut envoie des données à IoT Hub toutes les 10 secondes (`si=10`) ou dès que 256 Ko de données de message IoT Hub sont disponibles (`ms=0`). Elle ajoute un délai maximal de 10 secondes, mais présente la faible probabilité de perdre des données en raison de la grande taille de messages. Dans la version 2.7 et versions ultérieures, la configuration par défaut est de 500 ms pour le mode Orchestré, et de 0 pour le mode Autonome (aucun intervalle d’envoi). Les métriques `monitored item notifications enqueue failure` dans OPC Publisher versions 2.5 et antérieures et `messages lost` dans la version 2.7 d’OPC Publisher indiquent le nombre de messages qui ont été perdus.

Quand les paramètres `si` et `ms` ont la valeur 0, OPC Publisher envoie un message à IoT Hub dès que des données sont disponibles. Cela aboutit à une taille moyenne de message IoT Hub de plus de 200 octets. Toutefois, l’avantage de cette configuration est qu’OPC Publisher envoie les données à partir de la ressource connectée sans délai. Le nombre de messages perdus est élevé pour les cas d’usage où une grande quantité de données doit être publiée ; cette configuration n’est donc pas recommandée dans ces scénarios.

Pour mesurer les performances d’OPC Publisher, vous pouvez utiliser le paramètre `di` afin d’afficher les métriques de performances dans le journal de suivi selon l’intervalle spécifié (en secondes).

## <a name="next-steps"></a>Étapes suivantes
Le réglage des performances et de la mémoire d’OPC Publisher n’ayant plus de secret pour vous, vous pouvez consulter le dépôt GitHub OPC Publisher afin d’obtenir des ressources supplémentaires :

> [!div class="nextstepaction"]
> [Dépôt GitHub OPC Publisher](https://github.com/Azure/Industrial-IoT)