---
title: Langage de requête
titleSuffix: Azure Digital Twins
description: Découvrez les principes de base du langage des requêtes Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d656f19f6f4030025ff1393c3e5017466b3333fd
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044392"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>À propos du langage de requête pour Azure Digital Twins

N’oubliez pas que le centre d’Azure Digital Twins est le [**graphique de jumeaux**](concepts-twins-graph.md) construit à partir des  **jumeaux numériques** et des **relations**. Ce graphique peut être interrogé pour obtenir des informations sur les jumeaux numériques et les relations qu’il contient. Ces requêtes sont écrites dans un langage de requête de type SQL personnalisé, appelé **langage des requêtes Azure Digital Twins**.

Pour soumettre une requête au service à partir d’une application cliente, vous allez utiliser l’[**API de requête**](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview) Azure Digital Twins. Ceci permet aux développeurs d’écrire des requêtes et d’appliquer des filtres pour rechercher des ensembles de jumeaux numériques dans le graphique des jumeaux, ainsi que d’autres informations sur le scénario Azure Digital Twins.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Étapes suivantes

Apprenez à écrire des requêtes et consultez des exemples de codes clients dans [*Guide pratique : Interroger le graphique de jumeaux*](how-to-query-graph.md).