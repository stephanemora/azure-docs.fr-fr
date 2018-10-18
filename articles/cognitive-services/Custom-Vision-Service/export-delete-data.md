---
title: Exporter ou supprimer vos données - Service Vision personnalisée
titlesuffix: Azure Cognitive Services
description: Apprenez à exporter ou à supprimer vos données à partir du service Vision personnalisée.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: c98c72101d893f107d4a6c3185836a75bfe41007
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367489"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exporter ou supprimer des données utilisateur dans Custom Vision

Content Moderator collecte des données utilisateur pour faire fonctionner le service, mais les clients contrôlent totalement l’affichage, l’exportation et la suppression des données à l’aide de l’API [Formation](https://go.microsoft.com/fwlink/?linkid=865446) du Service Vision personnalisée.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Pour plus d’informations sur la façon d’exporter et supprimer des données utilisateur dans le Service Vision personnalisée, voir le tableau suivant.

| Données | Opération d’exportation | Opération de suppression |
| ---- | ---------------- | ---------------- |
| Informations sur le compte (Clés d’abonnement) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Supprimer via le portail Azure (Abonnements Azure). Vous pouvez également utiliser le bouton « Supprimer votre compte » dans la page des paramètres de CustomVision.ai (abonnements de compte Microsoft) |
| Détails de migration | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Détails des performances d’itération | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Liste des itérations | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projets et détails des projets | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) et [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) |
| Balises d’image | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) et [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) |
| Images | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fournit l’URI pour le téléchargement de l’image) et [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (fournit l’URI pour le téléchargement de l’image) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) |
| Modèles exportés | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Supprimé en cas de suppression du compte |
