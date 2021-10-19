---
title: Form Recognizer Studio | Préversion
titleSuffix: Azure Applied AI Services
description: 'Concept : Traitement, extraction de données et analyse des formulaires et documents à l’aide de Form Recognizer Studio (préversion)'
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: sajagtap
ms.openlocfilehash: e476f6da88688cb055c741f0888001d864908bae
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812427"
---
# <a name="form-recognizer-studio--preview"></a>Form Recognizer Studio | Préversion

>[!NOTE]
> Form Recognizer Studio est actuellement en préversion publique. Certaines fonctionnalités peuvent ne pas être prises en charge ou avoir des capacités limitées.

[Form Recognizer Studio (préversion)](https://formrecognizer.appliedai.azure.com/) est un outil en ligne permettant d’explorer, de comprendre et d’intégrer visuellement des fonctionnalités du service Form Recognizer dans vos applications. Utilisez le [démarrage rapide de Form Recognizer Studio](quickstarts/try-v3-form-recognizer-studio.md) pour commencer à explorer les modèles préformés avec des exemples de documents ou vos propres documents. Créez des projets pour générer des modèles de formulaire personnalisés et référencez ces modèles dans vos applications à l’aide du [Kit de développement logiciel (SDK) Python (préversion)](quickstarts/try-v3-python-sdk.md) et d’autres démarrages rapides.

L’image suivante montre la fonctionnalité de modèle prédéfini Facture en action.

:::image border="true" type="content" source="media/quickstarts/prebuilt-get-started-v2.gif" alt-text="Exemple de modèle prédéfini de Form Recognizer":::

## <a name="form-recognizer-studio-features"></a>Fonctionnalités de Form Recognizer Studio

Les fonctionnalités suivantes du service Form Recognizer sont disponibles dans le studio.

* **Disposition** : Essayez la fonctionnalité Disposition de Form Recognizer pour extraire le texte, les tableaux, les marques de sélection et les informations de structure des documents (PDF, TIFF) et des images (JPG, PNG, BMP). Commencez par le [démarrage rapide Disposition de Studio](quickstarts/try-v3-form-recognizer-studio.md#layout). Explorez la fonctionnalité à l’aide d’exemples de documents et de vos documents. Utilisez la visualisation interactive et la sortie JSON pour comprendre le fonctionnement de la fonctionnalité. Consultez la [présentation de Disposition](concept-layout.md) pour en savoir plus et commencez par le [démarrage rapide du Kit de développement logiciel (SDK) Python pour Disposition](quickstarts/try-v3-python-sdk.md#try-it-layout-model).

* **Modèles prédéfinis** : Les modèles prédéfinis de Form Recognizer vous permettent d’ajouter un traitement intelligent des formulaires à vos applications et à vos flux sans avoir à effectuer l’apprentissage et la construction de vos propres modèles. Commencez par le [démarrage rapide Modèles prédéfinis de Studio](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models). Explorez la fonctionnalité à l’aide d’exemples de documents et de vos documents. Utilisez la visualisation interactive, la liste des champs extraits et la sortie JSON pour comprendre le fonctionnement de la fonctionnalité. Consultez la [présentation de Modèles](concept-model-overview.md) pour en savoir plus et commencez par le [démarrage rapide du Kit de développement logiciel (SDK) Python pour Facture prédéfinie](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-invoice-model).

* **Modèles personnalisés** : Les modèles personnalisés de Form Recognizer vous permettent d’extraire des champs et des valeurs à partir de modèles formés avec vos données, adaptés à vos formulaires et à vos documents. Créez des modèles personnalisés autonomes ou combinez deux ou plusieurs modèles personnalisés pour créer un modèle composé permettant d’extraire des données de plusieurs types de formulaires. Commencez par le [démarrage rapide Modèles personnalisés de Studio](quickstarts/try-v3-form-recognizer-studio.md#custom-model-basics).  Utilisez l’Assistant en ligne, l’interface d’étiquetage, l’étape de formation et les visualisations pour comprendre le fonctionnement de la fonctionnalité. Testez le modèle personnalisé à l’aide de vos exemples de documents et procédez par itération pour améliorer le modèle. Consultez la [présentation des modèles personnalisés](concept-custom.md) pour en savoir plus et utilisez le [guide de migration de la préversion de Form Recognizer v3.0](v3-migration-guide.md) pour commencer à intégrer les nouveaux modèles à vos applications.

* **Modèles personnalisés : fonctionnalités d’étiquetage** : La création de modèles personnalisés Form Recognizer nécessite d’identifier les champs à extraire et d’étiqueter le contenu de vos documents avec ces champs avant d’effectuer l’apprentissage des modèles personnalisés. L’étiquetage du texte, des marques de sélection, des données tabulaires et d’autres types de contenu est généralement assisté par une interface utilisateur afin de faciliter le flux de travail de formation. Par exemple, utilisez les démarrages rapides [Étiquetage des tableaux](quickstarts/try-v3-form-recognizer-studio.md#labeling-as-tables) et [Étiquetage pour la détection de signature](quickstarts/try-v3-form-recognizer-studio.md#labeling-for-signature-detection) pour comprendre l’expérience d’étiquetage dans Form Recognizer Studio.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Prise en main de Form Recognizer Studio (préversion)](https://formrecognizer.appliedai.azure.com)

