---
title: Vue d’ensemble de l’enrichissement de message Azure IoT Hub
description: Vue d’ensemble de l’enrichissement de message pour les messages Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 13e35ab93fc37541548785c6355489eaf3a3efc2
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754551"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Enrichissement de message pour les messages de IoT Hub-appareil-à-cloud (version préliminaire)

*Enrichissement de message* est la capacité du IoT Hub pour *tampon* messages avec des informations supplémentaires avant que les messages sont envoyés au point de terminaison désigné. Il est souhaitable d’utiliser d’enrichissement de message pour inclure des données qui peuvent être utilisées pour simplifier le traitement en aval. Par exemple, enrichir les messages de télémétrie d’appareil avec une balise de jumeau d’appareil peut réduire la charge sur les clients à tirer de jumeau d’appareil d’appels d’API pour obtenir ces informations.

![Flux d’enrichissement de message](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Un enrichissement de message comporte trois éléments clés :

* Nom de l’enrichissement ou la clé

* Une valeur

* Un ou plusieurs [points de terminaison](iot-hub-devguide-endpoints.md) pour lequel l’enrichissement doit être appliquée.

La clé peut être n’importe quelle chaîne.

La valeur peut être un des exemples suivants :

* N’importe quelle chaîne statique. Les valeurs dynamiques telles que les conditions, logique et les fonctions ne sont pas autorisés. Par exemple, si vous développez une application SaaS utilisée par plusieurs clients, vous pouvez affecter un identificateur à chaque client et rendre cet identificateur soit disponible dans l’application. Lorsque l’application s’exécute, IoT Hub marque l’appareil messages de télémétrie avec l’identificateur du client, ce qui permet de traiter les messages différemment pour chaque client.

* Informations du jumeau d’appareil, telles que son chemin d’accès. Exemples serait *$twin.tags.field* et *$twin.tags.latitude*.

* Le nom de l’IoT hub envoie le message. Cette valeur est *$iothubname*.

## <a name="applying-enrichments"></a>Application : enrichissements

Les messages peuvent provenir de n’importe quelle source de données pris en charge par [le routage des messages IoT Hub](iot-hub-devguide-messages-d2c.md), y compris les exemples suivants :

* télémétrie d’appareil, telles que la température ou la pression
* modification jumeau d’appareil--modifications dans la représentation d’appareil
* événements de cycle de vie du périphérique, telles que lorsque l’appareil est créée ou supprimée

Vous pouvez ajouter des enrichissements de messages qui vont du point de terminaison intégré d’un IoT Hub, ou les messages qui sont acheminées vers des points de terminaison personnalisés tels que le stockage Blob Azure, une file d’attente Service Bus ou une rubrique Service Bus.

Vous pouvez également ajouter des enrichissements de messages qui sont publiés dans Event Grid en sélectionnant le point de terminaison sous forme de grille d’événement. Pour plus d’informations, consultez [Iot Hub et Event Grid](iot-hub-event-grid.md).

Enrichissement est appliquées par le point de terminaison. Si vous spécifiez enrichissements de cinq à indiquer un point de terminaison spécifique, tous les messages acheminés vers ce point de terminaison sont marqués avec les mêmes enrichissements de cinq.

Pour voir comment essayer d’enrichissement de message, consultez le [didacticiel d’enrichissement de message](tutorial-message-enrichments.md)

## <a name="limitations"></a>Limites

* Vous pouvez ajouter jusqu'à 10 enrichissement par IoT Hub pour ces hubs au niveau de base ou standard. Pour les Hubs IoT dans le niveau gratuit, vous pouvez ajouter jusqu'à 2 enrichissement.

* Dans certains cas, si vous appliquez un enrichissement avec une valeur définie sur une balise ou une propriété du jumeau d’appareil, la valeur sera marquée comme valeur de chaîne. Par exemple, si une valeur de l’enrichissement est définie sur $twin.tags.field, les messages sont alors marqués avec la chaîne « $twin.tags.field » au lieu de la valeur de ce champ à partir de la représentation. Cela se produit dans les cas suivants :

   * Votre IoT Hub est au niveau de base. Hubs IoT de niveau de base ne gèrent pas les représentations d’appareil.

   * Votre IoT Hub est au niveau standard, mais l’appareil envoie le message n’a aucune représentation d’appareil.

   * Votre IoT Hub est au niveau standard, mais le chemin d’accès d’appareil jumeau utilisé pour la valeur de l’enrichissement n’existe pas. Par exemple, si la valeur de l’enrichissement est définie sur $twin.tags.location et la représentation d’appareil n’a pas d’une propriété d’emplacement sous les balises, le message est marqué avec la chaîne « $twin.tags.location ». 

* Mises à jour à une représentation d’appareil peuvent prendre jusqu'à cinq minutes pour être reflétée dans la valeur d’enrichissement correspondant.

* La taille totale du message, y compris l’enrichissement, ne peut pas dépasser 256 Ko. Si une taille de message dépasse 256 Ko, le IoT Hub supprimera le message. Vous pouvez utiliser [métriques IoT Hub](iot-hub-metrics.md) pour identifier et déboguer des erreurs lorsque les messages sont supprimés. Par exemple, vous pouvez surveiller d2c.telemetry.egress.invalid.

## <a name="pricing"></a>Tarifs

Enrichissement de message est disponibles sans frais supplémentaires. Actuellement, vous êtes facturé lorsque vous envoyez un message à un IoT Hub. Vous êtes facturé uniquement une fois pour ce message, même si le message est envoyé à plusieurs points de terminaison.

## <a name="availability"></a>Disponibilité

Cette fonctionnalité est disponible en version préliminaire et est disponible dans toutes les régions à l’exception des États-Unis, ouest des États-Unis, Europe de l’ouest, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china), et [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour plus d’informations sur le routage des messages à un IoT Hub :

* [Utiliser le routage des messages IoT Hub pour envoyer des messages appareil-à-cloud à différents points de terminaison](iot-hub-devguide-messages-d2c.md)

* [Tutoriel : Routage d’IoT Hub](tutorial-routing.md)