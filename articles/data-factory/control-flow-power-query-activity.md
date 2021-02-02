---
title: Activité Power Query dans Azure Data Factory
description: Découvrez comment utiliser l’activité Power Query pour les fonctionnalités de data wrangling dans un pipeline Data Factory.
services: data-factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: c0ad769ceba4fc3fa7f602d70188ea1942ca73aa
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791692"
---
# <a name="power-query-activity-in-data-factory"></a>Activité Power Query dans une fabrique de données

L’activité Power Query vous permet de créer et d’exécuter des agrégateurs Power Query pour exécuter du data wrangling à grande échelle dans un pipeline Data Factory. Vous pouvez créer un agrégateur Power Query à partir de l’option de menu Nouvelles ressources ou en ajoutant une activité Power Query à votre pipeline.

![Capture d’écran montrant Power Query dans le volet de ressources de la fabrique.](media/data-flow/power-query-wrangling.png)

Auparavant, le data wrangling dans Azure Data Factory était créé à partir de l’option de menu Data Flow. Cela a été modifié pour créer à partir d’une nouvelle activité Power Query. Vous pouvez travailler directement dans l’éditeur d’agrégation Power Query pour effectuer une exploration interactive des données, puis enregistrer votre travail. Une fois l’opération terminée, vous pouvez prendre votre activité Power Query et l’ajouter à un pipeline. Azure Data Factory effectuera automatiquement un scale-out et rendra opérationnel votre data wrangling à l’aide de l’environnement Spark de flux de données d’Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Traduction en script de flux de données

Pour atteindre l’échelle avec votre activité Power Query, Azure Data Factory traduit votre script ```M``` en script de flux de données afin que vous puissiez exécuter vos Power Query à grande échelle à l’aide de l’environnement Spark de flux de données d’Azure Data Factory. Créez votre flux de données de wrangling à l’aide de la préparation des données sans code. Pour obtenir la liste des fonctions disponibles, consultez les [fonctions de transformation](wrangling-functions.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les concepts du data wrangling à l’aide de [Power Query dans Azure Data Factory](wrangling-tutorial.md)
