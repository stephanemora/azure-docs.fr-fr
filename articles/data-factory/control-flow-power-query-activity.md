---
title: Activité Power Query dans Azure Data Factory
description: Découvrez comment utiliser l’activité Power Query pour les fonctionnalités de data wrangling dans un pipeline Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 08/12/2021
ms.openlocfilehash: be5c2cbe0d4f364abb68ad8b01f00ca2c8396806
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129358526"
---
# <a name="power-query-activity-in-azure-data-factory"></a>Activité Power Query dans Azure Data Factory

L’activité Power Query vous permet de créer et d’exécuter des agrégateurs Power Query pour exécuter du data wrangling à grande échelle dans un pipeline Data Factory. Vous pouvez créer un agrégateur Power Query à partir de l’option de menu Nouvelles ressources ou en ajoutant une activité Power Query à votre pipeline.

![Capture d’écran montrant Power Query dans le volet de ressources de la fabrique.](media/data-flow/power-query-activity-1.png)

Vous pouvez travailler directement dans l’éditeur d’agrégation Power Query pour effectuer une exploration interactive des données, puis enregistrer votre travail. Une fois l’opération terminée, vous pouvez prendre votre activité Power Query et l’ajouter à un pipeline. Azure Data Factory effectuera automatiquement un scale-out et rendra opérationnel votre data wrangling à l’aide de l’environnement Spark de flux de données d’Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Traduction en script de flux de données

Pour atteindre l’échelle avec votre activité Power Query, Azure Data Factory traduit votre script ```M``` en script de flux de données afin que vous puissiez exécuter vos Power Query à grande échelle à l’aide de l’environnement Spark de flux de données d’Azure Data Factory. Créez votre flux de données de wrangling à l’aide de la préparation des données sans code. Pour obtenir la liste des fonctions disponibles, consultez les [fonctions de transformation](wrangling-functions.md).

## <a name="settings"></a>Paramètres

* Power Query : choisissez une Power Query existante à exécuter ou créez-en une.
* Exécuter sur Azure IR : choisissez un Azure Integration Runtime existant pour définir l’environnement Compute pour votre Power Query ou en créez-en un.
* Type de calcul : si vous choisissez le runtime d’intégration de résolution automatique par défaut, vous pouvez sélectionner le type de calcul à appliquer au calcul de cluster Spark pour votre exécution de Power Query.
* Nombre de cœurs : si vous choisissez le runtime d’intégration de résolution automatique par défaut, vous pouvez sélectionner le nombre de cœurs à appliquer au calcul de cluster Spark pour votre exécution de Power Query.

## <a name="sink"></a>Récepteur

Choisissez le jeu de données que vous souhaitez utiliser pour le lancement de vos données transformées une fois que le script Power Query M a été exécuté sur Spark. Pour plus d’informations sur la configuration des récepteurs, consultez la documentation sur les [récepteurs de flux de données](data-flow-sink.md).

## <a name="mapping"></a>Mappage

Sous l’onglet Mappage, vous pouvez configurer le mappage de colonnes à partir de la sortie de votre activité Power Query vers le schéma cible du récepteur que vous avez choisi. Pour en savoir plus sur le mappage de colonnes, consultez la [documentation sur le mappage du récepteur de flux de données](data-flow-sink.md#field-mapping).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les concepts du data wrangling à l’aide de [Power Query dans Azure Data Factory](wrangling-tutorial.md)
