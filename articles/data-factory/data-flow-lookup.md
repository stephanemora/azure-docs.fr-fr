---
title: Transformation de recherche de mappage de Data Flow pour Azure Data Factory
description: Transformation de recherche de mappage de Data Flow pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b156917a9987b023a9bf94e51c0cc14aebb133c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738383"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Transformation de recherche de mappage de Data Flow pour Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez la recherche pour ajouter des données de référence d’une autre source à votre Data Flow. La transformation de recherche requiert une source définie qui pointe vers votre table de référence et qui correspond aux champs clés.

![Transformation de recherche](media/data-flow/lookup1.png "Recherche")

Sélectionnez les champs clés que vous souhaitez mettre en correspondance entre les champs de flux de données entrantes et les champs provenant de la source de référence. Vous devez d’abord créer une nouvelle source sur le canevas de conception du Data Flow à utiliser comme partie droite de la recherche.

Lorsque les correspondances sont trouvées, les lignes et les colonnes résultantes provenant de la source de référence seront ajoutés à votre flux de données. Vous pouvez choisir les champs d’intérêt que vous souhaitez inclure dans votre récepteur à la fin de votre Data Flow.
