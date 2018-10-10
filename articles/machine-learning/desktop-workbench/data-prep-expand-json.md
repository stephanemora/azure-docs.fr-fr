---
title: Transformation Étendre JSON à l’aide d’Azure Machine Learning Workbench
description: Document de référence pour la transformation « Étendre JSON ».
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 0a5cbca114b220686d656f93edb00a199e3cbeeb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989819"
---
# <a name="expand-json-transformation"></a>Transformation Étendre JSON

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


La transformation **Étendre JSON** permet aux utilisateurs d’étendre une colonne existante qui contient du texte JSON valide sur plusieurs colonnes.

## <a name="how-to-perform-this-transformation"></a>Comment effectuer cette transformation

Pour effectuer cette transformation, exécutez les étapes suivantes :
1. Sélectionnez la colonne source qui contient le texte JSON.
2. Sélectionnez **Expand JSON** (Étendre JSON) dans le menu **Transforms** (Transformations). Vous pouvez aussi cliquer avec le bouton droit sur la colonne source et sélectionner **Expand JSON** (Étendre JSON) dans le menu contextuel. 
3. Cliquez sur **OK**. 
 
Les nouvelles colonnes sont ajoutées en regard de la colonne source. Ces colonnes contiennent les propriétés du niveau de hiérarchie suivant dans le texte JSON. La clé de propriété, le cas échéant, est utilisée pour créer le nom de la colonne correspondante. Les propriétés imbriquées sont conservées sous forme de texte JSON que l’utilisateur peut étendre de manière itérative ou ignorer en fonction des besoins.

## <a name="examples"></a>Exemples

La colonne source *Client* est étendue sur deux colonnes *Nom.Client* et *Téléphone.Client*.

| Client                                                | Nom.Client   | Téléphone.Client |
|---------------------------------------------------------|-----------------|----------------|
| { "Nom" : "Carrie Dodson", "Téléphone" : "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| { "Nom" : "Leonard Robledo", "Téléphone" : "456-7890-123"} | Leonard Robledo | 456-7890-123   |

