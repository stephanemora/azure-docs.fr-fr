---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998118"
---
L’étape suivante configure l’application pour qu’elle utilise votre identificateur et votre clé de compte. Vous les avez copiés dans un éditeur de texte pendant la [configuration de la ressource Spatial Anchors](#create-a-spatial-anchors-resource).

Dans le volet **Projet**, accédez à `Assets\AzureSpatialAnchors.SDK\Resources`. Sélectionnez `SpatialAnchorConfig`. Ensuite, dans le volet **Inspecteur**, entrez la `Account Key` comme valeur de la `Spatial Anchors Account Key` et le `Account ID` comme valeur de `Spatial Anchors Account Id`.

Ensuite, ouvrez `SpatialAnchorManager.cs`. Recherchez `CreateSessionAsync()` et ajoutez la ligne suivante, en remplaçant le domaine de votre compte : `session.Configuration.AccountDomain = "MyAccountDomain";`. Vous pouvez ajouter cette ligne directement avant ce commentaire `// Configure authentication`.
