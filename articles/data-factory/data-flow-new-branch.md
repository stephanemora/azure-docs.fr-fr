---
title: Plusieurs branches dans le flux de données de mappage
description: Réplication de flux de données dans le flux de données de mappage avec plusieurs branches
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834498"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Création d’une nouvelle branche dans le flux de données de mappage

Ajoutez une nouvelle branche pour effectuer plusieurs ensembles d’opérations et de transformations sur le même flux de données. L’ajout d’une nouvelle branche est utile lorsque vous souhaitez utiliser la même source pour plusieurs récepteurs ou pour effectuer la jointure réflexive des données.

Une nouvelle branche peut être ajoutée à partir de la liste des transformations de la même façon que d’autres transformations. L’action **Nouvelle branche** est disponible uniquement lorsqu’une transformation existante suit la transformation pour laquelle vous tentez de créer une branche.

![Ajout d’une nouvelle branche](media/data-flow/new-branch2.png "Ajout d’une nouvelle branche")

Dans l’exemple ci-dessous, le flux de données lit des données de trajet en taxi. La sortie agrégée par jour et par fournisseur est requise. Au lieu de créer deux flux de données distincts qui lisent à partir de la même source, il est possible d’ajouter une nouvelle branche. De cette façon, les deux agrégations peuvent être exécutées dans le cadre du même flux de données. 

![Ajout d’une nouvelle branche](media/data-flow/new-branch.png "Ajout d’une nouvelle branche")
