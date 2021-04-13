---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105543553"
---
| Propriété | Description | Exemple | Obligatoire |
|---|---|---|---|
| `app_location` | Emplacement du code de votre application. | Entrez `/` si le code source de votre application se trouve à la racine du dépôt, ou `/app` si le code de votre application se trouve dans un répertoire appelé `app`. | Oui |
| `api_location` | Emplacement de votre code Azure Functions. | Entrez `/api` si le code de votre application se trouve dans un dossier appelé `api`. Si aucune application Azure Functions n’est détectée dans le dossier, la génération n’échoue pas, le flux de travail suppose que vous ne voulez pas d’API. | Non |
| `output_location` | Emplacement du répertoire de sortie de compilation par rapport à `app_location`. | Si le code source de votre application se trouve dans `/app`, et que le script de build place les fichiers dans le dossier `/app/build`, définissez `build` comme valeur `output_location`. | Non |