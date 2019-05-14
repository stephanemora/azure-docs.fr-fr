---
title: Exporter votre modèle sur votre mobile - Service Vision personnalisée
titlesuffix: Azure Cognitive Services
description: Découvrez comment exporter votre modèle afin de l’utiliser pour créer des applications mobiles.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 7bf8217f5076c0a95d4db6c1c7cbea7bc93b91f3
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550539"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>Exporter votre modèle pour l’utiliser avec des appareils mobiles

Le service Vision personnalisée permet d’exporter des classifieurs pour les exécuter en mode hors connexion. Vous pouvez incorporer un classifieur exporté dans une application et l’exécuter localement sur un appareil pour la classification en temps réel.

Le service Vision personnalisée prend en charge les exportations suivantes :

* __Tensorflow__ pour __Android__.
* __CoreML__ pour __iOS11__.
* __ONNX__ pour __Windows ML__.
* __Conteneur__ Windows ou Linux. Le conteneur inclut un modèle Tensorflow et le code du service pour utiliser l’API Service Vision personnalisée. 

> [!IMPORTANT]
> Le service Vision personnalisée exporte uniquement des domaines __compacts__. Les modèles générés par des domaines compacts sont optimisés en fonction des contraintes de la classification en temps réel sur les appareils mobiles. Les classifieurs conçus avec un domaine compact sont parfois légèrement moins précis qu’avec un domaine standard, pour une même quantité de données d’apprentissage.
>
> Pour plus d’informations sur l’amélioration des classifieurs, consultez l’article [Améliorer votre classifieur](getting-started-improving-your-classifier.md).

## <a name="convert-to-a-compact-domain"></a>Convertir un domaine en domaine compact

> [!NOTE]
> Les étapes décrites dans cette section s’appliquent uniquement si vous utilisez un classifieur qui n’est pas défini avec un domaine compact.

Pour convertir le domaine d’un classifieur existant, effectuez les étapes suivantes :

1. À partir de la [page Vision personnalisée](https://customvision.ai), sélectionnez l’icône __Accueil__ pour afficher la liste de vos projets. Vous pouvez également utiliser le lien [https://customvision.ai/projects](https://customvision.ai/projects) pour voir vos projets.

    ![Image de l’icône Accueil et de la liste des projets](./media/export-your-model/projects-list.png)

2. Sélectionnez un projet, puis sélectionnez l’icône __d’engrenage__ en haut à droite de la page.

    ![Image de l’icône d’engrenage](./media/export-your-model/gear-icon.png)

3. Dans la section __Domaines__, sélectionnez un domaine __compact__. Sélectionnez __Enregistrer les modifications__.

    ![Image de la sélection des domaines](./media/export-your-model/domains.png)

4. En haut de la page, sélectionnez __Entraîner__ pour recommencer l’entraînement avec le nouveau domaine.

## <a name="export-your-model"></a>Exporter votre modèle

Pour exporter le modèle après le nouvel entraînement, effectuez les étapes suivantes :

1. Accédez à l’onglet **Performances** et sélectionnez __Exporter__. 

    ![Image de l’icône Exporter](./media/export-your-model/export.png)

    > [!TIP]
    > Si l’option __Exporter__ n’est pas disponible, cela signifie que l’itération sélectionnée n’utilise pas un domaine compact. Dans la section __Itérations__ de cette page, sélectionnez une itération qui utilise un domaine compact, puis sélectionnez __Exporter__.

2. Sélectionnez le format d’exportation, puis sélectionnez __Exporter__ pour télécharger le modèle.

## <a name="next-steps"></a>Étapes suivantes

Intégrer votre modèle exporté dans une application en explorant un des exemples ou des articles suivants :

* [Utilisation de votre modèle Tensorflow avec Python](export-model-python.md)
* [Utilisez votre modèle ONNX avec l’apprentissage de Windows](custom-vision-onnx-windows-ml.md)
* Consultez l’exemple [modèle CoreML dans une application iOS](https://go.microsoft.com/fwlink/?linkid=857726) pour la classification d’images en temps réel avec Swift.
* Consultez l’exemple [modèle Tensorflow dans une application Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) pour la classification d’image en temps réel sur Android.
* Consultez l’exemple [modèle CoreML avec Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) pour la classification d’image en temps réel dans une application Xamarin iOS.
