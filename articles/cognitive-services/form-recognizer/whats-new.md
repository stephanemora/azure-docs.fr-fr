---
title: Nouveautés de Form Recognizer
titleSuffix: Azure Cognitive Services
description: Comprenez les dernières modifications apportées à l’API Form Recognizer.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: e952d481daf53b1806dc3cfbb658c8c0c21f6984
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516295"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-form-recognizer"></a>Nouveautés de Form Recognizer

Le service Form Recognizer est régulièrement mis à jour. Lisez cet article pour vous tenir informé des améliorations de fonctionnalités, des correctifs et des mises à jour de la documentation.

## <a name="april-2021"></a>Avril 2021
<!-- markdownlint-disable MD029 -->

### <a name="sdk-updates-api--version-21-preview3"></a>Mises à jour du SDK (API version 2.1-preview.3)

### <a name="c-version-310-beta4"></a>**C# version 3.1.0-beta.4**

* **Nouvelles méthodes pour analyser les données de documents d’identité** :

   **[StartRecognizeIdDocumentsFromUriAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   **[StartRecognizeIdDocumentsAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   Pour obtenir la liste des valeurs de champ, _consultez_ [Champs extraits](concept-identification-cards.md#fields-extracted) dans notre documentation sur Form Recognizer.

* Augmenté l’ensemble des langages de document qui peuvent être fournis à la méthode **[StartRecognizeContent](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true)** .

* **Nouvelle propriété `Pages`  prise en charge par les classes suivantes** :

   **[RecognizeBusinessCardsOptions](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormsOptions](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   La propriété `Pages` vous permet de sélectionner une ou plusieurs pages pour les documents PDF et TIFF multipages. Pour les pages individuelles, entrez le numéro de page, par exemple, `3`. Pour une plage de pages (par exemple, page 2 et pages 5-7), entrez les numéros et les plages de page en les séparant par des virgules : `2, 5-7`.    

* **Nouvelle propriété `ReadingOrder` prise en charge par la classe suivante** :

   **[RecognizeContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  La propriété `ReadingOrder` est un paramètre facultatif qui vous permet de spécifier l’algorithme d’ordre de lecture (`basic` ou `natural`) qui doit être appliqué pour ordonner l’extraction d’éléments de texte. Si elle n’est pas spécifiée, la valeur par défaut est `basic`.

#### <a name="breaking-changes"></a>Changements cassants

* Le client prend par défaut la dernière version du service pris en charge, qui est actuellement **2.1-preview.3**.

* La méthode **[StartRecognizeCustomForms](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_)** lève désormais une exception `RequestFailedException()` lorsqu’un fichier non valide est passé.

### <a name="java-version-310-beta3"></a>**Java version 3.1.0-beta.3**

* **Nouvelles méthodes pour analyser les données de documents d’identité** :

  **[beginRecognizeIdDocumentsFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocumentsfromurl?view=azure-java-preview&preserve-view=true)**

  **[beginRecognizeIdDocuments](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocuments?view=azure-java-preview&preserve-view=true)**

   Pour obtenir la liste des valeurs de champ, _consultez_ [Champs extraits](concept-identification-cards.md#fields-extracted) dans notre documentation sur Form Recognizer.

* **Prise en charge du fichier image bitmap (.bmp) pour les formulaires personnalisés et les méthodes de formation dans l’énumération`FormContentType`**  :

* `image/bmp`

* **Nouvelle propriété `Pages`  prise en charge par les classes suivantes** :

   **[RecognizeBusinessCardsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormOptions](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  La propriété `Pages` vous permet de sélectionner une ou plusieurs pages pour les documents PDF et TIFF multipages. Pour les pages individuelles, entrez le numéro de page, par exemple, `3`. Pour une plage de pages (par exemple, page 2 et pages 5-7), entrez les numéros et les plages de page en les séparant par des virgules : `2, 5-7`.

* **Prise en charge du fichier image bitmap (.bmp) pour les formulaires personnalisés et les méthodes de formation dans les champs [FormContentType](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields)**  :

  `image/bmp`

* **Nouvel argument de mot clé `ReadingOrder` pris en charge pour les méthodes suivantes** :

* **[beginRecognizeContent](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?view=azure-java-preview&preserve-view=true)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   L’argument de mot clé `ReadingOrder` est un paramètre facultatif qui vous permet de spécifier l’algorithme d’ordre de lecture (`basic` ou `natural`) qui doit être appliqué pour ordonner l’extraction d’éléments de texte. Si elle n’est pas spécifiée, la valeur par défaut est `basic`.

* Le client prend par défaut la dernière version du service pris en charge, qui est actuellement **2.1-preview.3**.

### <a name="javascript-version-310-beta3"></a>**JavaScript version 3.1.0-beta.3**

* **Nouvelles méthodes pour analyser les données de documents d’identité** :

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[beginRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    Pour obtenir la liste des valeurs de champ, _consultez_ [Champs extraits](concept-identification-cards.md#fields-extracted) dans notre documentation sur Form Recognizer.

* **Nouvelles valeurs de champ ajoutées à l’interface FieldValue** :

    `gender` : les valeurs possibles sont `M`, `F` ou `X`.</br>
   `country` : les valeurs possibles suivent la chaîne de code du pays [ISO alpha-3](https://www.iso.org/obp/ui/#search) à trois lettres.

* ** Nouvelle option `pages` prise en charge par toutes les méthodes de reconnaissance de formulaire (formulaires personnalisés et tous les modèles prédéfinis). L’argument vous permet de sélectionner une ou plusieurs pages pour les documents PDF et TIFF multipages. Pour les pages individuelles, entrez le numéro de page, par exemple, `3`. Pour une plage de pages (par exemple, page 2 et pages 5-7), entrez les numéros et les plages de page en les séparant par des virgules : `2, 5-7`.

* Ajout de la prise en charge d’un type **[ReadingOrder](/javascript/api/@azure/ai-form-recognizer/readingorder?view=azure-node-preview&preserve-view=true)** aux méthodes de reconnaissance du contenu. Cette option vous permet de contrôler l’algorithme utilisé par le service pour déterminer la façon dont les lignes de texte reconnues doivent être triées. Vous pouvez spécifier l’algorithme d’ordre de lecture (`basic` ou `natural`) à appliquer pour ordonner l’extraction d’éléments de texte. Si elle n’est pas spécifiée, la valeur par défaut est `basic`.

* Fractionnez le type **[FormField](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true)** en plusieurs interfaces différentes. Cela ne devrait pas provoquer de problèmes de compatibilité d’API sauf dans certains cas extrêmes (valueType non défini).

* Migré vers le point de terminaison du service Form Recognizer **2.1-preview.3** pour tous les appels d’API REST.

### <a name="python-version--310b4"></a>**Python version 3.1.0b4**

* **Nouvelles méthodes pour analyser les données de documents d’identité** :

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  Pour obtenir la liste des valeurs de champ, _consultez_ [Champs extraits](concept-identification-cards.md#fields-extracted) dans notre documentation sur Form Recognizer.

* **Nouvelles valeurs de champ ajoutées à l’énumération [FieldValueType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true)**  :

   gender : les valeurs possibles sont `M`, `F` ou `X`.

  country : les valeurs possibles suivent [les codes de pays ISO alpha-3](https://www.iso.org/obp/ui/#search).

* **Prise en charge du fichier image bitmap (.bmp) pour les formulaires personnalisés et les méthodes de formation dans l’énumération [FormContentType](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true)**  :

    image/bmp

* **Nouvel argument de mot clé `pages` pris en charge par les méthodes suivantes** :

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   L’argument de mot clé `pages` vous permet de sélectionner une ou plusieurs pages pour les documents PDF et TIFF multipages. Pour les pages individuelles, entrez le numéro de page, par exemple, `3`. Pour une plage de pages (par exemple, page 2 et pages 5-7), entrez les numéros et les plages de page en les séparant par des virgules : `2, 5-7`.

* **Nouvel argument de mot clé `readingOrder` pris en charge pour les méthodes suivantes** :

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   L’argument de mot clé `readingOrder` est un paramètre facultatif qui vous permet de spécifier l’algorithme d’ordre de lecture (`basic` ou `natural`) qui doit être appliqué pour ordonner l’extraction d’éléments de texte. Si elle n’est pas spécifiée, la valeur par défaut est `basic`.

## <a name="march-2021"></a>Mars 2021

**La préversion publique 3 de Form Recognizer v2.1 est désormais disponible.** La version v2.1-preview.3 a été mise en production, qui inclut les fonctionnalités suivantes :

* **Nouveau modèle prédéfini de pièce d’identité** : Le nouveau modèle prédéfini de pièce d’identité permet aux clients de prendre des pièces d’identité et de renvoyer des données structurées pour automatiser le traitement. Il associe nos puissantes capacités de reconnaissance optique de caractères (OCR) à des modèles de compréhension des pièces d’identité pour extraire des informations clés des passeports et des permis de conduire, telles que le nom, la date de naissance, la date d’émission, la date d’expiration, etc.

  [En savoir plus sur le modèle prédéfini de pièce d’identité](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="exemple de passeport" lightbox="./media/id-canada-passport-example.png":::

* **Extraction d’éléments de ligne pour le modèle de facture prédéfini** : Le modèle de facture prédéfini prend désormais en charge l’extraction d’éléments de ligne. Il extrait maintenant les éléments complets et leurs parties (description, montant, quantité, ID produit, date, etc.). Un simple appel à l’API/au SDK vous permet d’extraire des données utiles de vos factures (texte, tableau, paires clé-valeur et éléments de ligne).

   [En savoir plus sur le modèle de facture prédéfini](concept-invoices.md)

* **Étiquetage et formation supervisés de tableaux, étiquetage de valeurs vides** : En plus des [capacités de pointe d’extraction automatique de tableaux par Deep Learning](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011) de Form Recognizer, il permet désormais aux clients d’étiqueter des tableaux et d’utiliser ces derniers pour la formation. Cette nouvelle version comprend la possibilité d’étiqueter des éléments de ligne/tableaux (dynamiques et fixes), de les utiliser pour la formation et d’effectuer l’apprentissage d’un modèle personnalisé pour extraire des paires clé-valeur et des éléments de ligne. Une fois qu’un modèle est formé, le modèle extrait les éléments de ligne dans le cadre de la sortie JSON dans la section documentResults.

    :::image type="content" source="./media/table-labeling.png" alt-text="Étiquetage des tableaux" lightbox="./media/table-labeling.png":::

    Outre l’étiquetage des tableaux, vous pouvez désormais étiqueter les valeurs vides et les régions. Si certains documents de votre jeu d’apprentissage n’ont pas de valeurs pour certains champs, vous pouvez utiliser cette fonction pour que votre modèle sache extraire correctement les valeurs des documents analysés.

* **Prise en charge de 66 nouvelles langues** : L’API de disposition et les modèles personnalisés de Form Recognizer prennent désormais en charge 73 langues.

  [En savoir plus sur la prise en charge linguistique de Form Recognizer](language-support.md)

* **Ordre de lecture naturel, classification de l’écriture manuscrite et sélection de page** : Avec cette mise à jour, vous pouvez choisir d’afficher les sorties de lignes de texte dans l’ordre de lecture naturel plutôt que dans l’ordre par défaut de gauche à droite et de haut en bas. Utilisez le nouveau paramètre de requête readingOrder et attribuez-lui la valeur « natural » pour obtenir un ordre de lecture plus convivial. En outre, pour les langues latines, Form Recognizer classe les lignes de texte comme styles manuscrits ou non et donne un score de confiance.

* **Améliorations de la qualité du modèle de reçu prédéfini** : Cette mise à jour comprend de nombreuses améliorations de la qualité pour le modèle de reçu prédéfini, notamment en ce qui concerne l’extraction d’éléments de ligne.

## <a name="november-2020"></a>Novembre 2020

### <a name="new-features"></a>Nouvelles fonctionnalités

**La préversion publique 2 de Form Recognizer v2.1 est désormais disponible.** La version v2.1-preview.2 a été mise en production, qui inclut les fonctionnalités suivantes :

- **Nouveau modèle de facture prédéfini** : le nouveau modèle de facture prédéfini permet aux clients de prendre des factures dans divers formats et de retourner des données structurées pour automatiser le traitement des factures. Il combine nos puissantes fonctionnalités de reconnaissance optique de caractères (OCR) avec des modèles de Deep Learning qui comprennent les factures dans le but d’extraire des informations clés de ces factures. Il extrait le texte clé, les tables et les informations comme le client, le fournisseur, le numéro de facture, la date d’échéance, le total, le montant dû, le montant des taxes, l’adresse d’expédition et l’adresse de facturation.

  > [En savoir plus sur le modèle de facture prédéfini](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="exemple de facture" lightbox="./media/invoice-example.jpg":::

- **Extraction de table améliorée** : Form Recognizer propose à présent une extraction de table améliorée, qui combine nos puissantes fonctionnalités de reconnaissance optique de caractères (OCR) avec un modèle d’extraction de table Deep Learning. Form Recognizer peut extraire des données dans les tables, y compris les tables complexes avec des colonnes ou lignes fusionnées, sans aucune bordure, etc.

  :::image type="content" source="./media/tables-example.jpg" alt-text="exemples de tables" lightbox="./media/tables-example.jpg":::


  > [En savoir plus sur l’extraction de dispositions](concept-layout.md)

- **Mise à jour de la bibliothèque de client** : les dernières versions des [bibliothèques de client](quickstarts/client-library.md) pour .NET, Python, Java et JavaScript prennent en charge l’API Form Recognizer 2.1.
- **Nouvelle langue prise en charge : japonais** : la nouvelle langue suivante est maintenant prise en charge pour `AnalyzeLayout` et `AnalyzeCustomForm` : japonais (`ja`). [Prise en charge linguistique](language-support.md)
- **Indication du style de ligne de texte (écriture manuscrite/autre) (langues latines uniquement)**  : Form Recognizer génère désormais un objet `appearance` qui détermine si chaque ligne de texte relève d’un style manuscrit ou non, ainsi qu’un score de confiance. Cette fonctionnalité est prise en charge uniquement pour les langues latines.
- **Améliorations apportées à la qualité** : l’extraction a été améliorée, notamment celle des chiffres uniques.
- **Nouvelle fonctionnalité de test dans l’outil d’étiquetage des exemples de Form Recognizer** : possibilité de tester les modèles prédéfinis de facture, de ticket de caisse et de carte de visite, ainsi que l’API Layout à l’aide de l’outil d’étiquetage des exemples de Form Recognizer. Découvrez comment vos données sont extraites sans écrire de code.

  > [Tester l’exemple d’outil de Form Recognizer](https://fott-preview.azurewebsites.net/)

  ![Exemple FOTT](./media/ui-preview.jpg)

- **Boucle de commentaires** : quand vous analysez des fichiers par le biais de l’outil d’étiquetage des exemples, vous pouvez maintenant l’ajouter au jeu d’apprentissage et ajuster les étiquettes au besoin pour effectuer l’apprentissage du modèle afin d’améliorer celui-ci.
- **Étiquetage automatique des documents** : étiquetez automatiquement d’autres documents en fonction de documents précédemment étiquetés dans le projet.

## <a name="august-2020"></a>Août 2020

### <a name="new-features"></a>Nouvelles fonctionnalités

**La préversion publique de Form Recognizer v2.1 est désormais disponible.** Le version v2.1-preview.1 a été mise en production, qui inclut les fonctionnalités suivantes :


- **La référence sur l’API REST est disponible** : afficher la [référence v2.1-preview.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
- **Nouvelles langues prises en charge en plus de l’anglais** : les [langues suivantes](language-support.md) sont désormais pris en charge pour `Layout` et `Train Custom Model` : anglais (`en`), chinois (simplifié) (`zh-Hans`), néerlandais (`nl`), français (`fr`), allemand (`de`), italien (`it`), portugais (`pt`) et espagnol (`es`).
- **Détection de case à cocher/marque de sélection** : le service Form Recognizer prend en charge la détection et l’extraction de marques de sélection telles que les cases à cocher et les cases d’option. Les marques de sélection sont extraites dans `Layout` et vous pouvez désormais aussi les étiqueter et les former dans `Train Custom Model` - _Effectuer l’entraînement avec des étiquettes_ afin d’extraire des paires clé-valeur pour les marques de sélection.
- La fonctionnalité de **composition de modèles** permet de composer et d’appeler plusieurs modèles avec un ID de modèle unique. Lors de l’envoi d’un document pour analyse avec un ID de modèle composé, une étape de classification a d’abord lieu pour l’acheminer vers le modèle personnalisé approprié. La composition de modèles est disponible pour `Train Custom Model` - _Effectuer l’entraînement avec des étiquettes_.
- Le **Nom du modèle** permet d’ajouter un nom convivial à vos modèles personnalisés afin d’en faciliter la gestion et le suivi.
- **[Nouveau modèle préconstruit pour les cartes de visite](concept-business-cards.md)** pour l’extraction de champs communs en anglais de cartes de visite.
- **[Nouveaux paramètres régionaux pour les reçus préconstruits](concept-receipts.md)** . En plus de EN-US, les paramètres régionaux EN-AU, EN-CA, EN-GB, EN-IN sont désormais pris en charge.
- **Améliorations de la qualité** pour `Layout`, `Train Custom Model` - _Effectuer l’entraînement sans étiquettes_ et _Effectuer l’entraînement avec des étiquettes_.

La version **v2.0** inclut la mise à jour suivante :

- Les [bibliothèques clientes](quickstarts/client-library.md) pour NET, Python, Java et JavaScript sont en disponibilité générale.

De **nouveaux exemples** sont disponibles sur GitHub.

- Le manuel [Recettes d’extraction de connaissances – Playbook de formulaires](https://github.com/microsoft/knowledge-extraction-recipes-forms) recueille les meilleures pratiques d’engagement des clients de Form Recognizer, et fournit des exemples de code utilisables, des listes de contrôle et des exemples de pipelines utilisés dans le développement de ces projets.
- L’[exemple d’outil d’étiquetage](https://github.com/microsoft/OCR-Form-Tools) a été mis à jour pour prendre en charge la nouvelle fonctionnalité v2.1. Consultez ce [démarrage rapide](quickstarts/label-tool.md) pour bien démarrer avec l’outil.
- L’exemple de Form Recognizer de [Kiosque intelligent](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) kiosque montre comment intégrer `Analyze Receipt` et `Train Custom Model` - _Effectuer l’entraînement sans étiquettes_.

## <a name="july-2020"></a>Juillet 2020

### <a name="new-features"></a>Nouvelles fonctionnalités
<!-- markdownlint-disable MD004 -->
* **Référence v2.0 disponible** : consultez les [informations de référence sur l’API v2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) et les SDK mis à jour pour [.NET](/dotnet/api/overview/azure/ai.formrecognizer-readme), [Python](/python/api/overview/azure/), [Java](/java/api/overview/azure/ai-formrecognizer-readme) et [JavaScript](/javascript/api/overview/azure/).
* Les **améliorations apportées aux tables et aux extractions** incluent des améliorations au niveau de la précision et des extractions de tables, en particulier, la possibilité de faire l’apprentissage d’en-têtes et de structures de tables dans l’_entraînement personnalisé sans étiquettes_.

* **Prise en charge des devises** : détection et extraction des symboles monétaires du monde entier.
* **Azure Gov** : Form Recognizer est maintenant disponible dans Azure Gov.
* **Fonctionnalités de sécurité améliorées** :
  * **Bring Your Own Key (BYOK)**  : Form Recognizer chiffre automatiquement vos données quand elles sont conservées dans le cloud afin de les protéger et de vous aider à répondre aux exigences de votre organisation concernant la sécurité et la conformité. Par défaut, votre abonnement utilise des clés de chiffrement gérées par Microsoft. Vous pouvez aussi maintenant gérer votre abonnement avec vos propres clés de chiffrement. Les [clés gérées par le client](./encrypt-data-at-rest.md), également appelées BYOK (Bring Your Own Key), offrent plus de flexibilité pour créer, permuter, désactiver et révoquer des contrôles d’accès. Vous pouvez également effectuer un audit sur les clés de chiffrement utilisées pour protéger vos données.
  * **Points de terminaison privés** : Sur un réseau virtuel, permettent d’[accéder aux données de façon sécurisée via une liaison privée](../../private-link/private-link-overview.md).

## <a name="june-2020"></a>Juin 2020

### <a name="new-features"></a>Nouvelles fonctionnalités

* **API CopyModel ajoutée aux SDK clients** : vous pouvez désormais utiliser les SDK clients pour copier des modèles d’un abonnement à un autre. Consultez [Sauvegarder et récupérer des modèles](./disaster-recovery.md) pour obtenir des informations générales sur cette fonctionnalité.
* **Intégration d’Azure Active Directory**  : vous pouvez maintenant utiliser vos informations d’identification Azure AD pour authentifier vos objets clients Form Recognizer dans les SDK.
* **Modifications spécifiques au SDK** : ces changements comprennent les ajouts de fonctionnalités mineures ainsi que les modifications importantes. Consultez les journaux des modifications du kit de développement logiciel (SDK) pour plus d’informations.
  * [Journal des modifications C# SDK Preview 3](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Journal des modifications Python SDK Preview 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Journal des modifications Java SDK Preview 3](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Journal des modifications JavaScript SDK Preview 3](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Avril 2020

### <a name="new-features"></a>Nouvelles fonctionnalités

* **Prise en charge du SDK pour la préversion publique de l’API Form Recognizer v 2.0** : ce mois-ci, nous avons étendu notre support technique pour inclure une préversion du SDK de Form Recognizer v2.0 (préversion). Utilisez les liens ci-dessous pour bien démarrer avec le langage de votre choix :
  * [Kit de développement logiciel (SDK) .NET](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Kit SDK Java](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Kit de développement logiciel (SDK) Python](/python/api/overview/azure/ai-formrecognizer-readme)
  * [Kit de développement logiciel (SDK) JavaScript](/javascript/api/overview/azure/ai-form-recognizer-readme)

  Le nouveau SDK prend en charge toutes les fonctionnalités de l’API REST v2.0 pour Form Recognizer. Par exemple, vous pouvez entraîner un modèle avec ou sans étiquettes pour extraire du texte, des paires clé-valeur et des tables de vos formulaires, extraire des données à partir de reçus avec le service des reçus préintégré et extraire du texte et des tables de vos documents avec le service de disposition. Vous pouvez partager vos commentaires sur les SDK à l’aide du [formulaire de commentaires sur les SDK](https://aka.ms/FR_SDK_v1_feedback).

* **Copier un modèle personnalisé** Vous pouvez désormais copier des modèles entre les régions et les abonnements à l’aide de la nouvelle fonctionnalité de copie de modèle personnalisé. Avant d’appeler l’API Copier une modèle personnalisé, vous devez d’abord obtenir l’autorisation de copie dans la ressource cible en appelant l’opération d’autorisation de copie sur le point de terminaison de cette dernière.

  * API REST [Générer une autorisation de copie](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization)
  * API REST [Copier un modèle personnalisé](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel)

### <a name="security-improvements"></a>Améliorations de sécurité

* Les clés gérées par le client sont maintenant disponibles pour Form Recognizer. Pour plus d’informations, consultez [Chiffrement des données au repos pour Form Recognizer](./encrypt-data-at-rest.md).
* Utilisez des identités managées pour accéder aux ressources Azure avec Azure Active Directory. Pour plus d’informations, consultez [Autoriser l’accès aux identités managées](../authentication.md#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Mars 2020

### <a name="new-features"></a>Nouvelles fonctionnalités

* **Types de valeurs pour l’étiquetage** : vous pouvez maintenant spécifier les types de valeurs que vous étiquetez avec l’outil d’étiquetage des exemples Form Recognizer. Les types et variantes de valeurs suivants sont actuellement pris en charge :
  * `string`
    * default, `no-whitespaces`, `alphanumeric`
  * `number`
    * default, `currency`
  * `date`
    * default, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Pour savoir comment utiliser cette fonctionnalité, consultez le guide de l’[outil d’étiquetage des exemples](./quickstarts/label-tool.md#specify-tag-value-types).


* **Visualisation des tables** : l’outil d’étiquetage des exemples affiche désormais les tables reconnues dans le document. Cette fonctionnalité vous permet de visualiser les tables qui ont été reconnues et extraites du document, avant l’étiquetage et l’analyse. Cette fonctionnalité peut être activée/désactivée à l'aide de l'option couches.

  L’image suivante illustre la façon dont les tables sont reconnues et extraites :

  > [!div class="mx-imgBorder"]
  > ![Visualisation de table à l'aide de l'outil d'étiquetage des exemples](./media/whats-new/table-viz.png)

    Les tables extraites sont disponibles dans la sortie JSON sous `"pageResults"`.

  > [!IMPORTANT]
  > L'étiquetage des tables n'est pas pris en charge. Si les tables ne sont pas reconnues et extraites automatiquement, vous ne pouvez les étiqueter qu'en tant que paires clé/valeur. Lorsque vous étiquetez des tables en tant que paires clé-valeur, étiquetez chaque cellule en tant que valeur unique.

### <a name="extraction-enhancements"></a>Améliorations apportées à l'extraction

Cette version comprend des améliorations en termes d'extraction et de précision, avec notamment la possibilité d'étiqueter et d'extraire plusieurs paires clé/valeur sur la même ligne de texte.

### <a name="sample-labeling-tool-is-now-open-source"></a>L’outil d’étiquetage des exemples est désormais open source

L’outil d’étiquetage des exemples Form Recognizer est désormais disponible sous forme de projet open source. Vous pouvez l'intégrer à vos solutions et y apporter des modifications pour qu'il réponde à vos besoins.

Pour plus d’informations sur l’outil d’étiquetage des exemples Form Recognizer, consultez la documentation disponible sur [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Application de TLS 1.2

La sécurité TLS 1.2 est maintenant appliquée pour toutes les requêtes HTTP adressées à ce service. Pour plus d'informations, consultez [Sécurité Azure Cognitive Services](../cognitive-services-security.md).

## <a name="january-2020"></a>Janvier 2020

Cette version introduit Form Recognizer 2.0 (préversion). Les sections ci-dessous contiennent des informations sur les nouvelles fonctionnalités, améliorations et modifications.

### <a name="new-features"></a>Nouvelles fonctionnalités

* **Modèle personnalisé**
  * **Effectuer l'apprentissage avec les étiquettes** Vous pouvez maintenant effectuer l'apprentissage d’un modèle personnalisé avec des données étiquetées manuellement. Cette méthode aboutit à des modèles plus performants et peut engendrer des modèles qui fonctionnent avec des formulaires complexes ou des formulaires contenant des valeurs sans clés.
  * **API asynchrone** Vous pouvez utiliser des appels d’API asynchrone pour effectuer l'apprentissage et analyser des fichiers et des ensembles de données volumineux.
  * **Prise en charge du fichier TIFF** Vous pouvez effectuer l'apprentissage et extraire des données de documents TIFF.
  * **Améliorations de la précision d’extraction**

* **Modèle de reçu préconstruit**
  * **Montants des pourboires** Vous pouvez à présent extraire les montants des pourboires et d’autres valeurs écrites à la main.
  * **Extraction d’élément de ligne** Vous pouvez extraire des valeurs d’élément de ligne des reçus.
  * **Valeurs de confiance** Vous pouvez afficher la confiance du modèle pour chaque valeur extraite.
  * **Améliorations de la précision d’extraction**

* **Extraction de la disposition** Vous pouvez maintenant utiliser l’API de disposition pour extraire les données texte et les données de tableau à partir de vos formulaires.

### <a name="custom-model-api-changes"></a>Modifications de l’API du modèle personnalisé

Toutes les API pour l’apprentissage et l’utilisation de modèles personnalisés ont été renommées, et certaines méthodes synchrones sont maintenant asynchrones. Les modifications principales sont les suivantes :

* Le processus d’apprentissage d’un modèle est maintenant asynchrone. Vous initiez l’apprentissage via l’appel d'API **/custom/models**. Cet appel renvoie un ID d’opération, que vous pouvez passer dans **custom/models/{modelID}** pour revenir aux résultats de l’apprentissage.
* L’extraction de clé/valeur est maintenant initiée par l’appel d'API **/custom/models/{modelID}/analyze**. Cet appel renvoie un ID d’opération, que vous pouvez passer dans **custom/models/{modelID}/analyzeResults/{resultID}** pour renvoyer les résultats d’extraction.
* Les ID d’opération pour l’opération d’apprentissage se trouvent maintenant dans l’en-tête **Location** des réponses HTTP, non dans l’en-tête **Operation-Location**.

### <a name="receipt-api-changes"></a>Modifications de l’API de reçu

Les API pour lire les reçus ont été renommées.

* L’extraction de données de reçu est maintenant initiée par l’appel d'API **/prebuilt/receipt/analyze**. Cet appel renvoie un ID d’opération, que vous pouvez passer dans **/prebuilt/receipt/analyzeResults/{resultID}** pour renvoyer les résultats d’extraction.

### <a name="output-format-changes"></a>Modifications du format de sortie

Les réponses JSON pour tous les appels d’API ont de nouveaux formats. Certaines clés et valeurs ont été ajoutées, supprimées et renommées. Consultez les démarrages rapides pour des exemples des formats JSON actuels.

## <a name="next-steps"></a>Étapes suivantes

Suivez un [guide de démarrage rapide](quickstarts/client-library.md) pour vous lancer dans l’écriture d’une application de traitement de formulaires avec Form Recognizer dans le langage de développement de votre choix.

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que Form Recognizer ?](./overview.md)