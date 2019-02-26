---
title: Transformation de clé de substitution de Data Flow de mappage pour Azure Data Factory
description: Transformation de clé de substitution de Data Flow de mappage pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 62e9879f6bc75f042669ecb931ca7459d1317cc7
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271255"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Transformation de clé de substitution de Data Flow de mappage pour Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez la transformation de clé de substitution pour ajouter une valeur de clé arbitraire non professionnelle à incrémentation à l’ensemble de lignes de votre flux de données. Cela est utile lors de la conception de tables de dimension dans un modèle de données analytique de schéma en étoile où chaque membre de vos tables de dimension doit avoir une clé unique qui est une clé non professionnelle, faisant partie de la méthodologie Kimball DW.

![Transformation de la clé de substitution](media/data-flow/surrogate.png "Transformation de la clé de substitution")

« Colonne clé » est le nom que vous donnerez à votre nouvelle colonne de clé de substitution.

« Valeur de début » est le point de début de la valeur incrémentielle.
