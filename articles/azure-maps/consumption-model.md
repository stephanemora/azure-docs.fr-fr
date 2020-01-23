---
title: Modèles de consommation du véhicule pour le routage | Microsoft Azure Maps
description: Dans cet article, vous découvrirez les modèles de consommation du véhicule pour le routage dans Microsoft Azure Maps.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5a8a0778ce279846b0d7a66b1729b6898e80a4b5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911713"
---
# <a name="consumption-model"></a>Modèle de consommation

Les itinéraires en ligne fournissent un ensemble de paramètres pour une description détaillée du modèle de consommation spécifique du véhicule.
Selon la valeur de **vehicleEngineType**, deux grands modèles de consommation sont pris en charge : _Combustion_ et _Electric_. Il n’est pas possible de spécifier les paramètres appartenant à des modèles différents dans la même requête.
Le modèle de consommation ne peut pas être utilisé avec les valeurs **travelMode**_bicycle_ et _pedestrian_.

## <a name="parameter-constraints-for-consumption-model"></a>Contraintes des paramètres pour le modèle de consommation

Dans les deux modèles de consommation, la spécification explicite de certains paramètres requiert la spécification d’autres paramètres. Ces dépendances sont les suivantes :

* Tous les paramètres nécessitent que l’utilisateur spécifie **constantSpeedConsumption**. Il n’est pas possible de spécifier un autre paramètre de modèle de consommation, à l’exception de **vehicleWeight**si **constantSpeedConsumption** n’est pas spécifié.
* **accelerationEfficiency** et **decelerationEfficiency** doivent toujours être spécifiés en tant que paire (autrement dit, les deux ou aucun).
* Si **accelerationEfficiency** et **decelerationEfficiency** sont spécifiés, le produit de leurs valeurs ne doit pas être supérieur à 1 (pour empêcher tout mouvement perpétuel).
* **uphillEfficiency** et **downhillEfficiency** doivent toujours être spécifiés en tant que paire (autrement dit, les deux ou aucun).
* Si **uphillEfficiency** et **downhillEfficiency** sont spécifiés, le produit de leurs valeurs ne doit pas être supérieur à 1 (pour empêcher tout mouvement perpétuel).
* Si les paramètres \*__Efficiency__ sont spécifiés par l’utilisateur, **vehicleWeight** doit également être spécifié. Lorsque **vehicleEngineType** a pour valeur _combustion_, **fuelEnergyDensityInMJoulesPerLiter** doit également être spécifié.
* **maxChargeInkWh** et **currentChargeInkWh** doivent toujours être spécifiés en tant que paire (autrement dit, les deux ou aucun).

> [!NOTE]
> Si seul **constantSpeedConsumption** est spécifié, aucun autre aspect de la consommation, comme la déclivité ou l’accélération du véhicule, n’est pris en compte dans les calculs de consommation.

## <a name="combustion-consumption-model"></a>Modèle de consommation Combustion

Le modèle de consommation Combustion est utilisé lorsque **vehicleEngineType** a la valeur _combustion_.
La liste des paramètres appartenant à ce modèle figure ci-dessous. Reportez-vous à la section Paramètres pour obtenir une description détaillée.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Modèle de consommation Electric

Le modèle de consommation Electric est utilisé lorsque **vehicleEngineType** a la valeur _electric_.
La liste des paramètres appartenant à ce modèle figure ci-dessous. Reportez-vous à la section Paramètres pour obtenir une description détaillée.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Valeurs sensibles des paramètres de consommation

Un ensemble spécifique de paramètres de consommation peut être rejeté, même s’il est susceptible de répondre à toutes les exigences explicites spécifiées ci-dessus. Cela se produit lorsque la valeur d’un paramètre spécifique, ou une combinaison de valeurs de plusieurs paramètres, est censée engendrer des amplitudes déraisonnables pour les valeurs de consommation. Cela indique probablement une erreur d’entrée, car la prise en charge de toutes les valeurs sensibles des paramètres de consommation se fait de manière appropriée. En cas de rejet d’un ensemble spécifique de paramètres de consommation, le message d’erreur associé contient une description textuelle de la ou des raisons de ce rejet.
Les descriptions détaillées des paramètres présentent des exemples de valeurs sensibles pour les deux modèles.
