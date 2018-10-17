---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 420757c5b7f6d19bb0abe87232e75497b7b97322
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019096"
---
1. Ouvrez Visual Studio 2017 Community Edition.
1. Créez un nouveau projet **Application de console (.NET Core)** et nommez-le `QnaMakerQuickstart`. Acceptez les valeurs par défaut des autres paramètres.
1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, **QnaMakerQuickstart**, puis sélectionnez **Gérer les packages NuGet…**.
1. Dans la fenêtre NuGet, sélectionnez **Navigateur**, puis recherchez **Newtonsoft.JSON** et installez le package. Ce package sert à analyser les données JSON retournées à partir de la réponse HTTP de QnaMaker. 