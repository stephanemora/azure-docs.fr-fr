---
title: Guide de référence de la traduction de documentation de l’API REST
titleSuffix: Azure Cognitive Services
description: Affichez une liste avec des liens vers les API REST de la traduction de documentation.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/21/2021
ms.author: lajanuar
ms.openlocfilehash: 38757efc32a90a74cdf73bc84af28b80f8402948
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112543880"
---
# <a name="document-translation-rest-api-reference-guide"></a>Guide de référence de la traduction de documentation de l’API REST

Document Translation est une fonctionnalité basée sur le cloud du service Azure Translator qui fait partie de la famille Azure Cognitive Service des API REST. L’API de Document Translation traduit les documents dans la totalité des [langues et dialectes pris en charge](../../language-support.md) tout en préservant la structure des documents et le format des données. Les méthodes disponibles sont répertoriées dans la table ci-dessous :

| Requête| Description|
|---------|--------------|
| [**Obtenir les formats de document pris en charge**](get-supported-document-formats.md)| Cette méthode retourne une liste des formats de documents pris en charge.|
|[**Obtenir les formats de glossaire pris en charge**](get-supported-glossary-formats.md)|Cette méthode retourne une liste des formats de glossaires pris en charge.|
|[**Obtenir les sources de stockage prises en charge**](get-supported-storage-sources.md)| Cette méthode retourne une liste des sources de stockage/options prises en charge.|
|[**Commencer la traduction (POST)** ](start-translation.md)|Cette méthode démarre une tâche de traduction de documentation. |
|[**Obtenir l’état des documents**](get-documents-status.md)|Cette méthode retourne l’état de tous les documents d’une tâche de traduction.|
|[**Obtenir l’état d’un document**](get-document-status.md)| La méthode retourne l’état d’un document spécifique dans une tâche. |
|[**Obtenir l’état des traductions**](get-translations-status.md)| Cette méthode retourne une liste de toutes les demandes de traduction soumises par un utilisateur et l’état de chaque demande.|
|[**Obtenir l’état de la traduction**](get-translation-status.md) | Cette méthode retourne un résumé de l’état d’une demande de traduction de documentation spécifique. Cela comprend l’état global de la demande et l’état des documents en cours de traduction dans le cadre de cette demande.|
|[**Annuler la traduction (DELETE)** ](cancel-translation.md)| Cette méthode annule une traduction de documentation en cours de traitement ou en file d’attente. |

> [!div class="nextstepaction"]
> [Explorez nos bibliothèques clientes et kits de développement logiciel (SDK) pour C# et Python.](../client-sdks.md)