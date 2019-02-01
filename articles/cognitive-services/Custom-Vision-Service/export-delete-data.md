---
title: Exporter ou supprimer vos données - Service Vision personnalisée
titlesuffix: Azure Cognitive Services
description: Apprenez à exporter ou à supprimer vos données à partir du service Vision personnalisée.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: f4b83c332387e7ea19028efec76771ace42f612b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209891"
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
