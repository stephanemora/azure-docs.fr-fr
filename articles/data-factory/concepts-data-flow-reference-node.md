---
title: Nœud de référence de la fonctionnalité de mappage de flux de données Azure Data Factory
description: Le flux de données Data Factory ajoutera un nœud de référence pour les jointures, recherches et unions
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 641375c2b848957ffc0f5ad092a28460d91b6690
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212407"
---
# <a name="azure-data-factory-mapping-data-flow-reference-node"></a>Nœud de référence de la fonctionnalité de mappage de flux de données Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Nœud de référence](media/data-flow/referencenode.png "Nœud de référence")

Un nœud de référence est automatiquement ajouté au canevas pour indiquer que le nœud auquel il est attaché renvoie à un autre nœud existant sur le canevas. Un nœud de référence peut être considéré comme un pointeur ou une référence à une autre transformation de flux de données.

Par exemple :  Lorsque vous effectuez une opération Join ou Union sur plusieurs flux de données, le canevas Data Flow peut ajouter un nœud de référence qui reflète le nom et les paramètres du flux entrant non-primaire.

Le nœud de référence ne peut pas être déplacé ou supprimé. Vous pouvez toutefois cliquer dans le nœud pour modifier les paramètres de transformation d’origine.

Les règles d’interface utilisateur qui régissent le moment où Data Flow ajoute le nœud de référence dépendent de l’espace disponible et de l’espacement vertical entre les lignes.
