---
title: Créer une solution de vision sans code dans Azure Percept Studio
description: Découvrez comment créer une solution de vision sans code dans Azure Percept Studio et comment la déployer dans votre DK Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 54d4f1fe983cf20b734351754bb8eba191894dbc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678060"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Créer une solution de vision sans code dans Azure Percept Studio

Azure Percept Studio vous permet de créer et de déployer des solutions de vision par ordinateur personnalisées, sans nécessiter de code. En lisant cet article, vous pourrez :

- Créer un projet de vision dans [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)
- Collecter des images d’entraînement avec votre devkit
- Étiqueter vos images d’entraînement dans [Custom Vision](https://www.customvision.ai/)
- Entraîner votre modèle de détection ou de classification des objets personnalisé
- Déployer votre modèle sur votre devkit

Ce tutoriel est conçu pour les développeurs qui ont peu d’expérience en IA, voire aucune, et qui débutent avec Azure Percept.

## <a name="prerequisites"></a>Prérequis

- DK (devkit) Azure Percept
- [Abonnement Azure](https://azure.microsoft.com/free/)
- Expérience OOBE : vous avez connecté votre devkit à un réseau Wi-Fi, créé un hub IoT et connecté votre devkit à ce hub IoT

## <a name="create-a-vision-prototype"></a>Créer un prototype de vision

1. Démarrez votre navigateur pour accéder à [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Dans la page Vue d’ensemble, cliquez sur l’onglet **Tutoriels et démonstrations**.

    :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Écran de vue d’ensemble d’Azure Percept Studio." lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. Dans **Tutoriels et démonstrations de vision**, cliquez sur **Créer un prototype de vision**.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Écran des tutoriels et démonstrations Azure Percept Studio." lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. Dans la page **Nouveau prototype Custom Vision Azure Percept**, effectuez les étapes suivantes :

    1. Dans la zone **Nom du projet**, entrez un nom pour votre prototype de vision.

    1. Entrez la description du prototype de vision dans la zone **Description du projet**.

    1. Sélectionnez **DK Azure Percept** dans le menu déroulant **Type d’appareil**.

    1. Sélectionnez une ressource dans le menu déroulant **Ressources**, ou cliquez sur **Créer une ressource**. Si vous choisissez de créer une ressource, effectuez les étapes suivantes dans la fenêtre **Créer** :
        1. Entrez le nom de votre nouvelle ressource.
        1. Sélectionnez votre abonnement Azure.
        1. Sélectionnez un groupe de ressources ou créez-en un.
        1. Sélectionnez votre région préférée.
        1. Sélectionnez votre niveau tarifaire (nous vous recommandons S0).
        1. En bas de la fenêtre, cliquez sur **Créer**.

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="Fenêtre Créer une ressource.":::

    1. Pour **Type de projet**, indiquez si votre projet de vision implique la détection d’objets ou la classification d’images. Pour plus d’informations sur les types de projets, cliquez sur **M’aider à choisir**.

    1. Pour **Optimisation**, indiquez si vous souhaitez optimiser votre projet afin de bénéficier d’une justesse optimale, d’une faible latence réseau, ou d’un équilibre entre les deux.

    1. Cliquez sur le bouton **Créer**.

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Écran Créer un prototype de vision personnalisé.":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>Connecter un appareil à votre projet et capturer des images

Après avoir créé une solution de vision, vous devez lui ajouter votre devkit et le hub IoT correspondant.

1. Mettez votre devkit sous tension.

1. Dans le menu déroulant **IoT Hub**, sélectionnez le hub IoT auquel votre devkit était connecté lors de l’expérience OOBE.

1. Dans le menu déroulant **Appareils**, sélectionnez votre devkit.

Ensuite, vous devez charger ou capturer des images en vue de l’entraînement de votre modèle d’intelligence artificielle. Nous vous recommandons de charger au moins 30 images par type d’étiquette. Par exemple, si vous souhaitez créer un détecteur de chiens et de chats, vous devez charger au moins 30 images de chiens et 30 images de chats. Pour capturer des images avec le SoM de vision de votre devkit, effectuez les étapes suivantes :

1. Dans la fenêtre **Capture d’image**, sélectionnez **Afficher le flux de l’appareil** pour afficher le flux vidéo du SoM de vision.

1. Vérifiez le flux vidéo afin de vous assurer que la caméra de votre SoM de vision est correctement alignée pour prendre les photos d’entraînement. Apportez les modifications nécessaires.

1. Dans la fenêtre **Capture d’image**, cliquez sur **Prendre une photo**.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="Écran Capture d’image.":::

1. Vous pouvez également configurer une capture d’image automatisée pour collecter une grande quantité d’images à la fois. Pour cela, cochez la case **Capture d’image automatique**. Sélectionnez votre taux d’acquisition d’images par défaut sous **Taux de capture**, ainsi que le nombre total d’images que vous souhaitez collecter sous **Cible**. Cliquez sur **Définir la capture automatique** pour commencer le processus de capture d’image automatique.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="Menu déroulant de la capture d’image automatique.":::

Lorsque vous avez suffisamment de photos, cliquez sur **Suivant : Étiqueter les images et entraîner le modèle** en bas de l’écran. Toutes les images seront enregistrées dans [Custom Vision](https://www.customvision.ai/).

> [!NOTE]
> Si vous choisissez de charger des images d’entraînement directement dans Custom Vision, notez que la taille du fichier image ne doit pas dépasser 6 Mo.

## <a name="tag-images-and-train-your-model"></a>Étiqueter les images et entraîner le modèle

Avant d’entraîner votre modèle, ajoutez des étiquettes à vos images.

1. Dans la page **Étiqueter les images et entraîner le modèle**, cliquez sur **Ouvrir un projet dans Custom Vision**.

1. Sur le côté gauche de la page **Custom Vision**, cliquez sur **Sans étiquette** sous **Étiquettes** pour afficher les images que vous avez collectées à l’étape précédente. Sélectionnez une ou plusieurs images sans étiquette.

1. Dans la fenêtre **Détails de l’image**, cliquez sur l’image pour commencer l’étiquetage. Si vous avez sélectionné la détection d’objets comme type de projet, vous devez également dessiner un [cadre englobant](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#upload-and-tag-images) autour des objets que vous souhaitez étiqueter. Ajustez le cadre englobant si nécessaire. Tapez l’étiquette de votre objet, puis cliquez sur **+** pour l’appliquer. Par exemple, si vous créez une solution de vision qui doit vous informer quand un rayon de magasin a besoin d’être réapprovisionné, ajoutez l’étiquette « Rayon vide » aux images montrant des rayons vides, et ajoutez l’étiquette « Rayon plein » aux images montrant des rayons entièrement réapprovisionnés. Répétez cette opération pour toutes les images non étiquetées.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Écran Étiquetage des images dans Custom Vision.":::

1. Après avoir étiqueté vos images, cliquez sur l’icône **X** en haut à droite de la fenêtre. Cliquez sur **Étiqueté** sous **Étiquettes** pour afficher toutes les images récemment étiquetées.

1. Une fois vos images étiquetées, vous êtes prêt à effectuer l’entraînement de votre modèle d’IA. Pour ce faire, cliquez sur **Entraîner** en haut de la page. Vous devez disposer d’au moins 15 images par type d’étiquette pour entraîner votre modèle (nous vous recommandons d’en utiliser au moins 30). L’entraînement prend généralement 30 minutes. Toutefois, il peut prendre davantage de temps si votre jeu d’images est très grand.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="Sélection des images d’entraînement avec le bouton Entraîner mis en évidence.":::

1. Une fois l’entraînement terminé, votre écran affiche les performances de votre modèle. Pour plus d’informations sur l’évaluation de ces résultats, consultez la [documentation concernant l’évaluation des modèles](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#evaluate-the-detector). Après l’entraînement, vous souhaiterez peut-être également [tester votre modèle](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/test-your-model) sur d’autres images et le réentraîner si nécessaire. Chaque fois que vous entraînez votre modèle, celui-ci est enregistré en tant que nouvelle itération. Pour plus d’informations sur l’amélioration des performances d’un modèle, consultez la [documentation Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier).

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="Résultats de l’entraînement du modèle.":::

    > [!NOTE]
    > Si vous choisissez de tester votre modèle sur d’autres images dans Custom Vision, notez que la taille du fichier image de test ne doit pas dépasser 4 Mo.

Une fois que vous êtes satisfait des performances de votre modèle, fermez Custom Vision en fermant l’onglet du navigateur.

## <a name="deploy-your-ai-model"></a>Déployer votre modèle d’IA

1. Revenez à l’onglet Azure Percept Studio, puis cliquez sur **Suivant : Évaluer et déployer** en bas de l’écran.

1. La fenêtre **Évaluer et déployer** affiche les performances de l’itération de modèle sélectionnée. Sélectionnez l’itération que vous souhaitez déployer sur votre devkit dans le menu déroulant **Itération de modèle**, puis cliquez sur **Déployer le modèle** en bas de l’écran.

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="Écran Déploiement du modèle." lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. Une fois le modèle déployé, affichez le flux vidéo de celui-ci pour voir l’inférence de votre modèle à l’œuvre.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="Flux d’appareil montrant le détecteur de casque à l’œuvre.":::

Après avoir fermé cette fenêtre, vous pouvez revenir en arrière pour modifier votre projet de vision à tout moment. Pour cela, cliquez sur **Vision** sous **Projets d’IA** dans la page d’accueil d’Azure Percept Studio, puis sélectionnez le nom de votre projet de vision.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="Page Projet de vision." lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous avez créé une ressource Azure pour ce tutoriel et si vous ne souhaitez plus développer ni utiliser votre solution de vision, effectuez les étapes suivantes pour supprimer votre ressource :

1. Accédez au [portail Azure](https://ms.portal.azure.com/#home).
1. Cliquez sur **Toutes les ressources**.
1. Cochez la case en regard de la ressource créée pendant ce tutoriel. Le type de ressource sera listé sous **Cognitive Services**.
1. Cliquez sur l’icône **Supprimer** en haut de l’écran.

## <a name="next-steps"></a>Étapes suivantes

Consultez ensuite les guides pratiques sur les projets de vision pour en savoir plus sur les autres fonctionnalités de la solution de vision dans Azure Percept Studio.

<!--
Add links to how-to articles and oobe article.
-->
