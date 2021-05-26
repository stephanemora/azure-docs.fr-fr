---
title: 'Démarrage rapide : Étiqueter des formulaires, effectuer l’apprentissage d’un modèle et analyser des formulaires à l’aide de l’exemple d’outil d’étiquetage – Form Recognizer'
titleSuffix: Azure Applied AI Services
description: Dans ce guide de démarrage rapide, vous allez utiliser l’outil d’étiquetage des exemples Form Recognizer pour étiqueter manuellement des documents de formulaire. Ensuite, vous allez effectuer l’apprentissage d’un modèle de traitement de documents personnalisé, et utiliser le modèle pour extraire des paires clé/valeur.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/14/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-may-2021
keywords: traitement de documents
ms.openlocfilehash: af803b92945be059f604a3890a90d3aa36e5781e
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110475150"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
<!-- markdownlint-disable MD029 -->
# <a name="get-started-with-form-recognizer"></a>Bien démarrer avec Form Recognizer

Découvrez comment bien démarrer avec Form Recognizer en utilisant l’exemple d’outil de Form Recognizer. Azure Form Recognizer est un service cognitif qui vous permet de créer des logiciels de traitement de données automatisé à l’aide des technologies du Machine Learning. Identifiez et extrayez du texte, des paires clé/valeur, des marques de sélection, des données de tableau, et plus encore, de vos formulaires : le service génère des données structurées qui incluent les relations dans le fichier d’origine. Vous pouvez utiliser Form Recognizer par le biais de l’exemple d’outil, de l’API REST ou du SDK. Effectuez les étapes suivantes pour tester Form Recognizer par le biais de l’exemple d’outil.

Utilisez Form Recognizer pour :

* Analyser la disposition
* Analyser avec un modèle Prebuilt (prédéfini) (factures, tickets de caisse, documents d’identité)
* Entraîner et analyser un formulaire personnalisé

## <a name="prerequisites"></a>Prérequis

Pour suivre cette procédure de démarrage rapide, vous avez besoin des éléments suivants :

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="créez une ressource Form Recognizer"  target="_blank">créer une ressource Form Recognizer</a> sur le portail Azure pour obtenir votre clé et votre point de terminaison.
  * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* (Facultatif) Télécharger et décompresser les exemples de documents de démarrage rapide suivants

## <a name="create-a-form-recognizer-resource"></a>Créer une ressource Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-layout"></a>Analyser la disposition

Extrayez du texte, des tableaux, des marques de sélection et une structure d’un document.

1. Accédez à l’[exemple d’outil Form Recognizer](https://fott-2-1.azurewebsites.net/)
2. Dans la page d’accueil de l’exemple d’outil, sélectionnez « Use Layout to get text, tables and selection marks » (Utiliser Disposition pour obtenir du texte, des tableaux et des marques de sélection)

     :::image type="content" source="../media/label-tool/layout-1.jpg" alt-text="Paramètres de connexion pour l’outil Layout (Disposition) de Form Recognizer.":::

3. Remplacez « need endpoint » (point de terminaison nécessaire) par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.

4. Remplacez « need apikey » (clé d’API nécessaire) par la clé d’abonnement que vous avez obtenue avec votre ressource Form Recognizer.

    :::image type="content" source="../media/label-tool/layout-2.jpg" alt-text="Paramètres de connexion de l’outil Layout (Disposition) de Form Recognizer.":::

5. Sélectionnez l’URL source, collez l’URL suivante de l’exemple de document https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/layout-page-001.jpg, puis cliquez sur le bouton Fetch (Récupérer).

1. Cliquez sur « Run Layout » (Exécuter la disposition). L’exemple d’outil d’étiquetage de Form Recognizer appelle alors l’API Analyze Layout (Analyser la disposition) et analyse le document.

1. Afficher les résultats : consultez le texte extrait mis en évidence, les marques de sélection détectées et les tableaux détectés.

    :::image type="content" source="../media/label-tool/layout-3.jpg" alt-text="Paramètres de connexion pour l’outil Form Recognizer.":::

1. Téléchargez le fichier de sortie JSON pour afficher les résultats détaillés de la disposition.
     * Le nœud « readResults » contient chaque ligne de texte avec sa position de cadre englobant respective dans la page.
     * Le nœud « selectionMarks » affiche chaque marque de sélection (case à cocher, case d’option) et indique si son état est « sélectionné » ou « non sélectionné ».
     * La section « pageResults » comprend les tableaux extraits. Pour chaque tableau, le texte, l’index de ligne et de colonne, l’étendue de ligne et de colonne, le cadre englobant, etc. sont extraits.

## <a name="analyze-using-a-prebuilt-model-invoices-receipts-ids-"></a>Analyser avec un modèle prédéfini (Factures, Tickets de caisse, Identifiants...)

Extrayez du texte, des tableaux et des paires clé/valeur à partir de factures, de reçus, de documents d’identité et de cartes de visite à l’aide d’un modèle prédéfini Form Recognizer.

1. Accédez à l’[exemple d’outil Form Recognizer](https://fott-2-1.azurewebsites.net/)
2. Dans la page d’accueil de l’exemple d’outil, sélectionnez « Use prebuilt model to get data » (Utiliser un modèle prédéfini pour obtenir des données)

    :::image type="content" source="../media/label-tool/prebuilt-1.jpg" alt-text="Analyser les résultats de la disposition Form Recognizer":::

3. Sélectionnez l’URL source

4. Choisissez le fichier que vous souhaitez analyser à partir des options ci-dessous :

    * URL pour une image de facture. Pour ce démarrage rapide, vous pouvez utiliser un [exemple de document de facture](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf).
    * Une URL pour une image de ticket de caisse. Pour ce démarrage rapide, vous pouvez utiliser un [exemple de document de facture](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/id-us-driver-license-wa.jpg).
    * Une URL pour une image de ticket de caisse. Pour ce démarrage rapide, vous pouvez utiliser un [exemple d’image de reçu](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg).
    * URL pour une image de carte de visite. Pour ce démarrage rapide, vous pouvez utiliser un [exemple de carte de visite](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg).

5. Remplacez « need endpoint » (point de terminaison nécessaire) par le point de terminaison que vous avez obtenu avec votre abonnement Form Recognizer.

6. Remplacez « need apikey » (clé d’API nécessaire) par la clé d’abonnement que vous avez obtenue avec votre ressource Form Recognizer.

    :::image type="content" source="../media/label-tool/prebuilt-3.jpg" alt-text="Paramètres de connexion de l’outil prédéfini de Form Recognizer.":::

7. Sélectionnez le type de formulaire que vous souhaitez analyser (facture, reçu, cartes de visite ou identifiant en fonction du type de document que vous voulez analyser et que vous avez sélectionné).

8. Cliquez sur « Run analysis » (Exécuter l’analyse). L’exemple d’outil d’étiquetage de Form Recognizer appelle alors l’API Analyze Prebuilt (Analyser des éléments prédéfinis) et analyse le document.
9. Afficher les résultats : consultez les paires clé/valeur extraites, les éléments de ligne, le texte mis en évidence extrait et les tableaux détectés.

    :::image type="content" source="../media/label-tool/prebuilt-2.jpg" alt-text="Analyser les résultats de la facture prédéfinie Form Recognizer":::

10. Téléchargez le fichier de sortie JSON pour consulter les résultats détaillés.

    * Le nœud « readResults » contient chaque ligne de texte avec sa position de cadre englobant respective dans la page.
    * Le nœud « selectionMarks » affiche chaque marque de sélection (case à cocher, case d’option) et indique si son état est « sélectionné » ou « non sélectionné ».
    * La section « pageResults » comprend les tableaux extraits. Pour chaque tableau, le texte, l’index de ligne et de colonne, l’étendue de ligne et de colonne, le cadre englobant, etc. sont extraits.
    * Le champ « documentResults » contient les informations sur les paires clé/valeur et sur les éléments de ligne pour les parties les plus pertinentes du document.

## <a name="train--analyze-a-custom-form"></a>Entraîner et analyser un formulaire personnalisé

Entraînez un modèle de formulaire personnalisé adapté à vos documents. Extrayez du texte, des tableaux, des marques de sélection et des paires clé/valeur de vos documents avec un modèle Form Recognizer personnalisé.

### <a name="prerequisites-for-training-a-custom-form-model"></a>Prérequis pour entraîner un modèle de formulaire personnalisé

* Un conteneur d’objets blob du Stockage Azure qui contient un jeu de données d’entraînement. Tout d’abord, vérifiez que tous les documents d’entraînement ont le même format. Si vous avez des formulaires dans plusieurs formats, organisez-les en sous-dossiers en fonction du format. Dans le cadre de ce guide de démarrage rapide, vous pouvez utiliser les fichiers disponibles dans le dossier Train (Entraîner) de l’[exemple de jeu de données](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample_data_without_labels.zip) (téléchargez et extrayez sample_data.zip).
* Configurez le partage des ressources inter-domaine (CORS) sur le Stockage Blob Azure et activez-le sur votre compte de stockage. Sélectionnez votre compte de stockage dans le portail Azure, puis choisissez l’onglet **CORS** dans le volet gauche. Sur la ligne inférieure, renseignez les valeurs suivantes. Sélectionnez **Enregistrer** en haut. </br></br>

  * Origines autorisées = *
  * Méthodes autorisées = \[tout sélectionner\]
  * En-têtes autorisés = *
  * En-têtes exposés = *
  * Âge maximal = 200

> [!div class="mx-imgBorder"]
> ![Configuration de CORS dans le portail Azure](../media/label-tool/cors-setup.png)

## <a name="train-a-custom-form-model"></a>Entraîner un modèle de formulaire personnalisé

1. Accédez à l’[exemple d’outil Form Recognizer](https://fott-2-1.azurewebsites.net/)

1. Dans la page d’accueil de l’exemple d’outil, sélectionnez « Use Custom to train a model with labels and get key value pairs » (Utiliser un formulaire personnalisé pour entraîner un modèle avec des étiquettes et obtenir des paires clé/valeur)

    :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="Entraîner un modèle personnalisé.":::

2. Sélectionnez « New Project » (Nouveau projet)

#### <a name="create-a-new-project"></a>Création d'un projet

Configurez les paramètres du projet en renseignant les champs avec les valeurs suivantes :

* **Display Name** (Nom d’affichage) : nom d’affichage du projet
* **Security Token** (Jeton de sécurité) : certains paramètres de projet peuvent inclure des valeurs sensibles, telles que des clés API ou d’autres secrets partagés. Chaque projet génère un jeton de sécurité qui peut être utilisé pour chiffrer/déchiffrer les paramètres de projet sensibles. Vous pouvez accéder aux jetons de sécurité dans les paramètres de l’application, en sélectionnant l’icône d’engrenage en bas de la barre de navigation gauche.

* **Source connection** (Connexion source) : L’exemple d’outil d’étiquetage se connecte à une source (vos formulaires chargés d’origine) et à une cible (étiquettes créées et données de sortie). Les connexions peuvent être configurées et partagées entre les projets. Elles utilisent un modèle de fournisseur extensible, ce qui vous permet d’ajouter facilement de nouveaux fournisseurs sources/cibles. Créez une connexion en cliquant sur le bouton **Add Connection** (Ajouter une connexion). Renseignez les champs avec les valeurs suivantes :
   * **Display Name** (nom d’affichage) : nom d’affichage de la connexion.
   * **Description** : description de votre projet.
   * **SAS URL** (URL SAS) : URL de signature d’accès partagé (SAS) de votre conteneur Stockage Blob Azure.

   [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="Emplacement SAS.":::

* **Folder Path** (Chemin du dossier) : (Facultatif) Si vos formulaires sources se trouvent dans un dossier sur le conteneur d’objets blob, spécifiez le nom du dossier ici
* **Form Recognizer Service Uri** (URI du service Form Recognizer) : votre URL de point de terminaison Form Recognizer.
* **API Key** (Clé API) : votre clé d’abonnement Form Recognizer.
* **Description** : (facultatif) Description du projet

    :::image type="content" source="../media/label-tool/connections.png"  alt-text="Paramètres de connexion":::

#### <a name="label-your-forms"></a>Étiqueter vos formulaires

  :::image type="content" source="../media/label-tool/new-project.png"  alt-text="Page du nouveau projet":::

Quand vous créez ou ouvrez un projet, la fenêtre principale de l’éditeur d’étiquettes s’ouvre. L’éditeur d’étiquettes est composé de trois parties :

* Un volet de visualisation redimensionnable, qui contient une liste déroulante de formulaires à partir de la connexion source.
* Le volet principal de l’éditeur, qui vous permet d’appliquer des étiquettes.
* Le volet de l’éditeur d’étiquettes, qui permet aux utilisateurs de modifier, de verrouiller, de réorganiser et de supprimer des étiquettes.

##### <a name="identify-text-and-tables"></a>Identifier du texte et des tables

Sélectionnez **Run OCR on all files** (Exécuter l’OCR sur tous les fichiers) dans le volet gauche pour obtenir les informations sur la disposition du texte et des tables pour chaque document. L’outil d’étiquetage dessine des rectangles englobants autour de chaque élément de texte.

L’outil d’étiquetage indique aussi les tables qui ont été automatiquement extraites. Sélectionnez l’icône de table/grille à gauche du document pour voir la table extraite. Dans ce démarrage rapide, le contenu des tables étant automatiquement extrait, nous n’étiquèterons pas le contenu des tables et ferons confiance à l’extraction automatisée.

  :::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Visualisation d’une table dans l’outil d'étiquetage des exemples.":::

##### <a name="apply-labels-to-text"></a>Appliquer des étiquettes à du texte

Vous allez ensuite créer des balises (étiquettes) et les appliquer aux éléments de texte que vous voulez que le modèle analyse. Notez que les exemples de données d’étiquette incluent déjà des champs étiquetés auxquels nous allons ajouter un autre champ.

1. Tout d’abord, utilisez le volet de l’éditeur d’étiquettes pour créer une étiquette que vous souhaitez identifier.
   1. Sélectionnez **+** pour créer une étiquette.
   1. Entrez le nom de l’étiquette. Ajouter une étiquette « Total »
   1. Appuyez sur Entrée pour enregistrer l’étiquette.
1. Dans l’éditeur principal, sélectionnez la valeur totale parmi les éléments de texte mis en évidence.
1. Sélectionnez l’étiquette Total que vous voulez appliquer à la valeur, ou appuyez sur la touche du clavier correspondante. Les touches numériques sont affectées comme touches d’accès rapide pour les 10 premières étiquettes. Vous pouvez réorganiser vos étiquettes à l’aide des icônes de flèches haut et bas dans le volet de l’éditeur d’étiquettes.

    > [!Tip]
    > Gardez à l’esprit les conseils suivants quand vous étiquetez vos formulaires :
    >
    > * Vous ne pouvez appliquer qu’une seule étiquette à chaque élément de texte sélectionné.
    > * Chaque étiquette ne peut être appliquée qu’une seule fois par page. Si une valeur apparaît plusieurs fois sur le même formulaire, créez des étiquettes différentes pour chaque instance, par exemple « facture n° 1 », « facture n° 2 », etc.
    > * Les étiquettes ne peuvent pas s’étendre sur plusieurs pages.
    > * Étiquetez les valeurs telles qu’elles apparaissent sur le formulaire ; n’essayez pas de fractionner une valeur en deux parties avec deux étiquettes différentes. Par exemple, un champ d’adresse doit être étiqueté avec une étiquette unique, même s’il s’étend sur plusieurs lignes.
    > * N’incluez pas de clés dans vos champs étiquetés&mdash;uniquement les valeurs.
    > * Les données du tableau doivent être détectées automatiquement et seront disponibles dans le fichier JSON de sortie final dans la section « pageResults ». Toutefois, si le modèle ne parvient pas à détecter toutes les données de votre tableau, vous pouvez également étiqueter et entraîner un modèle pour détecter les tableaux. Consultez le « Guide pratique pour entraîner et étiqueter << route vers la rubrique de guide pratique >> »
    > * Servez-vous des boutons situés à droite de **+** pour rechercher, renommer, réorganiser et supprimer vos étiquettes.
    > * Pour supprimer une étiquette appliquée sans supprimer l’étiquette proprement dite, sélectionne lez rectangle étiqueté dans la vue du document et appuyez sur la touche de suppression.
    >

Effectuez les étapes ci-dessus pour étiqueter les cinq formulaires de l’exemple de jeu de données.

  :::image type="content" source="../media/label-tool/custom-1.jpg" alt-text="Étiqueter les exemples.":::

#### <a name="train-a-custom-model"></a>Entraîner un modèle personnalisé

Choisissez l’icône d’entraînement dans le volet gauche pour ouvrir la page Training (Entraînement). Sélectionnez ensuite le bouton **Train** pour commencer l’entraînement du modèle. Une fois le processus d’entraînement terminé, les informations suivantes s’affichent :

* **Model ID** : ID du modèle qui a été créé et entraîné. Chaque appel d’entraînement crée un modèle avec son propre ID. Copiez cette chaîne dans un emplacement sûr. Vous en aurez besoin si vous souhaitez effectuer des appels de prédiction via l’[API REST](./client-library.md?pivots=programming-language-rest-api) pi une [bibliothèque de client](./client-library.md).
* **Average Accuracy** : justesse moyenne du modèle. Vous pouvez améliorer la justesse du modèle en étiquetant des formulaires supplémentaires et en effectuant un nouvel entraînement pour créer un modèle. Nous vous recommandons de commencer par étiqueter cinq formulaires en analysant et en testant les résultats, puis d’ajouter, si nécessaire, d’autres formulaires.
* Liste des étiquettes et justesse estimée par étiquette.

    :::image type="content" source="../media/label-tool/custom-2.jpg" alt-text="Vue de l’entraînement.":::

#### <a name="analyze-a-custom-form"></a>Analyser un formulaire personnalisé

Sélectionnez l’icône Analyser (ampoule) située à gauche pour tester votre modèle. Sélectionnez le fichier local (« Local File ») source. Recherchez puis sélectionnez un fichier dans l’exemple de jeu de données que vous avez décompressez dans le dossier de test. Choisissez ensuite le bouton **« Run analysis » (Exécuter l’analyse)** pour obtenir des paires clé/valeur, du texte et des prédictions de tableaux pour le formulaire. L’outil applique des étiquettes dans les cadres englobants et signale la confiance de chaque étiquette.

:::image type="content" source="../media/label-tool/custom-3.jpg" alt-text="Outil d’affichage de l’entraînement.":::

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à utiliser l’exemple d’outil Form Recognizer qui permet de tester les modèles Layout (Disposition) et Pre-built (Prédéfini), ainsi que d’entraîner un modèle personnalisé et d’analyser un formulaire personnalisé avec des données étiquetées manuellement. Vous pouvez maintenant essayer le SDK de la bibliothèque de client ou l’API REST pour utiliser Form Recognizer.

> [!div class="nextstepaction"]
> [ explorer le SDK de la bibliothèque de client Form Recognizer ou l’API REST](client-library.md)
