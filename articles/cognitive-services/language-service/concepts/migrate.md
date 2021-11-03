---
title: 'Migrer vers Azure Cognitive Service for Language à partir de : LUIS, QnA Maker et Analyse de texte'
titleSuffix: Azure Cognitive Services
description: Utilisez cet article pour savoir si vous devez migrer vos applications à partir de LUIS, QnA Maker et Analyse de texte.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1e2a2945700e0537b7ae4b220f97eafb1ce19a69
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096348"
---
# <a name="migrating-to-azure-cognitive-service-for-language"></a>Migration vers Azure Cognitive Service for Language

Le 2 novembre 2021, Azure Cognitive Service for Language a été publié en préversion publique. Ce service de langage réunit les offres de service Analyse de texte, QnA Maker et LUIS tout en fournissant plusieurs nouvelles fonctionnalités. 

## <a name="do-i-need-to-migrate-to-the-language-service-if-i-am-using-text-analytics"></a>Dois-je effectuer une migration vers le service de langage si j’utilise Analyse de texte ?

Analyse de texte a été incorporé au service de langage et ses fonctionnalités sont toujours disponibles. Si vous utilisiez Analyse de texte, vos applications devraient continuer à fonctionner sans changement cassant. Vous pouvez également consulter le [Guide de migration d’Analyse de texte v2](migrate-from-text-analytics-v2.md), si vous devez mettre à jour une application plus ancienne. 

Nous mettons à votre disposition des articles de démarrage rapide que vous pouvez consulter pour connaître les informations les plus récentes sur les points de terminaison de service et les appels d’API. 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-luis"></a>Comment effectuer une migration vers le service de langage si j’utilise LUIS ?

Si vous utilisez Language Understanding (LUIS), vous pouvez [importer votre fichier JSON LUIS](../conversational-language-understanding/concepts/backwards-compatibility.md) vers la nouvelle fonctionnalité de compréhension du langage courant. 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-qna-maker"></a>Comment effectuer une migration vers le service de langage si j’utilise QnA Maker ?

Si vous utilisez QnA Maker, consultez le [guide de migration](../question-answering/how-to/migrate-qnamaker.md) pour plus d’informations sur la migration de bases de connaissances depuis QnA Maker vers les réponses aux questions.

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble d’Azure Cognitive Service for Language](../overview.md)
* [Vue d’ensemble de la compréhension du langage courant](../conversational-language-understanding/overview.md)
* [Vue d’ensemble des réponses aux questions](../question-answering/overview.md)
