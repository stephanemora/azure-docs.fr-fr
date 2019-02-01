---
title: Résumé des démarrages rapides de Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Dans ces guides de démarrage rapide, vous analysez une image, créez une miniature et extrayez du texte imprimé ou manuscrit à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 79ca49ddeb6f9b7f9f3f74c41d20f57d49b5ab86
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225793"
---
# <a name="quickstart-summary"></a>Démarrage rapide : Résumé

Ces démarrages rapides fournissent des informations et des exemples de code pour une prise en main rapide du service de l’API Vision par ordinateur.

Les exemples effectuent des appels HTTP directs vers l’API Vision par ordinateur. Consultez la section *Démarrages rapides du kit de développement logiciel (SDK)* pour obtenir des exemples d’utilisation des bibliothèques clientes de l’API Vision par ordinateur, qui fournissent des méthodes pratiques qui encapsulent les appels HTTP.

Pour tester rapidement les API Vision par ordinateur, essayez la [console de test d’API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

Vous pouvez utiliser le service de l’API Vision par ordinateur pour effectuer les tâches suivantes :

* Analyser une image distante
* Analyser une image locale
* Détecter les célébrités et les points de repère (modèles de domaine)
* Générer une miniature de façon intelligente
* Détecter et extraire le texte imprimé (OCR) à partir d’une image
* Détectez et extrayez le texte manuscrit à partir d’une image

Le code est similaire dans chaque exemple. Toutefois, chaque exemple met l’accent sur différentes fonctionnalités de l’API Vision par ordinateur, ainsi que sur différentes techniques d’échange des données avec le service, telles que :

* _Générer une miniature_ : renvoie une image sous la forme d’un tableau d’octets dans le corps de la réponse.
* _Analyser une image locale_ : l’image doit être incluse dans la requête en tant que tableau d’octets.
* _Extraire du texte manuscrit_ : nécessite deux appels pour récupérer le texte.

## <a name="summary"></a>Résumé

| Démarrage rapide               | Paramètres de la requête                          | response          |
| ------------------------ | ------------------------------------------- | ----------------  |
| Analyser une image distante   | visualFeatures=Categories,Description,Color | Chaîne JSON       |
| Analyser une image locale    | data=image_data (tableau d’octets)                | Chaîne JSON       |
| Détecter les célébrités       | model=celebrities                           | Chaîne JSON       |
| Générer une miniature     | width=200&height=150&smartCropping=true     | tableau d’octets        |
| Extraire le texte imprimé     | language=unk&detectOrientation=true         | Chaîne JSON       |
| Extraire le texte manuscrit | handwriting=true                            | URL, chaîne JSON  |

## <a name="next-steps"></a>Étapes suivantes

Explorez les API Vision par ordinateur utilisées pour analyser une image, détecter des célébrités et des points de repère, créer une miniature et extraire le texte imprimé et manuscrit.

> [!div class="nextstepaction"]
> [Explorer les API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
