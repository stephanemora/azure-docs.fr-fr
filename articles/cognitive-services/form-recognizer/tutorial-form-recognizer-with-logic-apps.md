---
title: 'Tutoriel : Utiliser Form Recognizer avec Azure Logic Apps pour analyser les factures - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Dans ce tutoriel, vous allez utiliser Form Recognizer avec Azure Logic Apps pour créer un workflow qui automatise les processus d’entraînement et de test des modèles à l’aide d’exemples de données.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: nitinme
ms.openlocfilehash: ff160c57b86e2269165f5fe1d2808684d2e6e86e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118534"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Tutoriel : Utiliser Form Recognizer avec Azure Logic Apps pour analyser les factures

Dans ce tutoriel, à l’aide d’Azure Logic Apps, vous allez créer un workflow qui utilise Form Recognizer (un service de la suite Azure Cognitive Services) afin d’extraire les données des factures. Commencez par entraîner un modèle Form Recognizer à l’aide d’un exemple de jeu de données, puis testez le modèle sur un autre jeu de données.

Voici ce qui est couvert par ce tutoriel :

> [!div class="checklist"]
> * Demander l’accès à Form Recognizer
> * Créer un conteneur d’objets blob du stockage Azure
> * Charger des exemples de données dans le conteneur d’objets blob Azure
> * Créer une application logique Azure
> * Configurer l’application logique pour qu’elle utilise une ressource Form Recognizer
> * Tester le workflow en exécutant l’application logique

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/).

## <a name="understand-the-invoice-to-be-analyzed"></a>Comprendre la facture à analyser

L’exemple de jeu de données que vous allez utiliser pour entraîner et tester le modèle est disponible sous la forme d’un fichier .zip sur [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Téléchargez et extrayez le fichier .zip, puis ouvrez le fichier PDF d’une facture dans le dossier **/train**. Notez qu’il comprend un tableau avec le numéro de facture, la date de facturation, etc. 

> [!div class="mx-imgBorder"]
> ![Exemple de facture](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

Dans ce tutoriel, vous allez apprendre à utiliser un workflow Azure Logic Apps pour extraire les informations de tableaux comme ceux-ci au format JSON.

## <a name="create-an-azure-storage-blob-container"></a>Créer un conteneur d’objets blob du stockage Azure

Vous allez utiliser ce conteneur pour charger les exemples de données nécessaires à l’entraînement du modèle.

1. Suivez les instructions fournies dans [Créer un compte de stockage Azure](../../storage/common/storage-account-create.md) pour créer un compte de stockage. Nommez le compte de stockage **formrecostorage**.
1. Suivez les instructions fournies dans [Créer un compte de stockage Azure](../../storage/blobs/storage-quickstart-blobs-portal.md) pour créer un conteneur dans le compte de stockage Azure. Nommez le conteneur **formrecocontainer**. Définissez le niveau d’accès public sur **Conteneur (accès en lecture anonyme pour les conteneurs et les objets blob)** .

    > [!div class="mx-imgBorder"]
    > ![Créer un conteneur d’objets blob](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Charger des exemples de données dans le conteneur d’objets blob Azure

Téléchargez les exemples de données disponibles sur [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Extrayez les données dans un dossier local, puis chargez le contenu du dossier **/train** dans le conteneur **formrecocontainer** que vous avez créé précédemment. Suivez les instructions fournies dans [Charger un objet blob de blocs](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) pour charger des données dans un conteneur.

Copiez l’URL du conteneur. Vous aurez besoin de cette URL plus tard dans le tutoriel. Si vous avez créé le compte de stockage et le conteneur en leur attribuant les noms indiqués dans ce tutoriel, l’URL sera la suivante : *https:\//formrecostorage.blob.core.windows.net/formrecocontainer/* .

## <a name="create-a-form-recognizer-resource"></a>Créer une ressource Form Recognizer

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Créer votre application logique

Vous pouvez utiliser Azure Logic Apps pour automatiser et orchestrer les tâches et les workflows. Dans ce tutoriel, vous allez créer une application logique qui est déclenchée par la réception d’une facture que vous souhaitez analyser en tant que pièce jointe d’un e-mail. Dans ce workflow, vous allez effectuer les tâches suivantes :
* Configurer l’application logique pour qu’elle se déclenche automatiquement lorsque vous recevez un e-mail avec une facture jointe.
* Configurer l’application logique pour qu’elle utilise l’opération **Train Model** (Entraîner le modèle) de Form Recognizer afin d’entraîner un modèle avec des exemples de données que vous avez chargés dans le stockage d’objets blob Azure.
* Configurer l’application logique pour qu’elle utilise l’opération **Analyze Form** (Analyser le formulaire) de Form Recognizer afin d’utiliser le modèle que vous avez déjà entraîné. Ce composant va analyser la facture que vous fournissez à cette application logique en fonction du modèle qu’il a précédemment entraîné.

Effectuez les étapes suivantes pour configurer votre workflow.

1. Dans le menu principal Azure, choisissez **Créer une ressource** > **Intégration** > **Application logique**.

1. Sous **Créer une application logique**, indiquez les détails de votre application logique comme indiqué ici. Sélectionnez **Créer** quand vous avez terminé.

   | Propriété | Valeur | Description |
   |----------|-------|-------------|
   | **Nom** | <*logic-app-name*> | Nom de votre application logique, qui peut contenir uniquement des lettres, des chiffres, des traits d’union (`-`) des traits de soulignement (`_`), des parenthèses (`(`, `)`) et des points (`.`). Cet exemple utilise « My-First-Logic-App ». |
   | **Abonnement** | <*Azure-subscription-name*> | Le nom de votre abonnement Azure |
   | **Groupe de ressources** | <*nom-groupe-de-ressources-Azure*> | Nom du [groupe de ressources Azure](./../../azure-resource-manager/management/overview.md) utilisé pour organiser les ressources connexes. Cet exemple utilise « My-First-LA-RG ». |
   | **Lieu** | <*Azure-region*> | Région dans laquelle stocker les informations sur votre application logique. Cet exemple utilise la région « USA Ouest ». |
   | **Log Analytics** | Off | Maintenez le paramètre de journalisation des diagnostics **Désactivé**. |
   ||||

1. Une fois qu’Azure a déployé votre application, dans la barre d’outils Azure, sélectionnez **Notifications** > **Accéder à la ressource** pour votre application logique déployée. Ou bien, vous pouvez rechercher et sélectionner votre application logique en tapant son nom dans la zone de recherche.

   Le Concepteur d’application logique s’ouvre et affiche une page contenant une vidéo de présentation et les déclencheurs couramment utilisés. Sous **Modèles**, sélectionnez **Application logique vide**.

   > [!div class="mx-imgBorder"]
   > ![Sélectionner un modèle vide pour l’application logique](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>Configurer l’application logique pour qu’elle déclenche le workflow à la réception d’un e-mail

Dans ce tutoriel, vous allez déclencher le workflow à la réception d’un e-mail contenant une facture en pièce jointe. Ce tutoriel est basé sur le service e-mail Office 365, mais vous pouvez utiliser n’importe quel autre fournisseur de messagerie.

1. Sous les onglets, sélectionnez Tout, sélectionnez **Office 365 Outlook**, puis, sous **Déclencheurs**, sélectionnez **À la réception d’un e-mail**.

    ![Déclencher l’application logique à la réception d’un e-mail](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. Dans la zone **Office 365 Outlook**, cliquez sur **Se Connecter**, puis entrez les informations de connexion au compte Office 365.

1. Dans la boîte de dialogue suivante, effectuez les étapes qui suivent.
    1. Sélectionnez le dossier pour lequel l’arrivée de nouveaux e-mails doit être supervisée.
    1. Pour **Comporte des pièces jointes**, sélectionnez **Oui**. Cela garantit que seuls les e-mails avec des pièces jointes déclencheront le workflow.
    1. Pour **Inclure des pièces jointes**, sélectionnez **Oui**. Cela garantit que le contenu de la pièce jointe sera utilisé dans le traitement en aval.

        > [!div class="mx-imgBorder"]
        > ![Configuration du déclenchement de l’application logique à la réception d’un e-mail](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Cliquez sur **Enregistrer** dans la barre d’outils située en haut.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Configurer l’application logique pour qu’elle utilise une opération d’entraînement de modèle Form Recognizer

Avant de pouvoir utiliser le service Form Recognizer pour analyser les factures, vous devez entraîner un modèle en lui fournissant des exemples de données de factures à analyser.

1. Sélectionnez **Nouvelle étape**, puis, sous **Choisir une action**, recherchez **Form Recognizer**. Dans les résultats qui s’affichent, sélectionnez **Form Recognizer**, puis, sous les actions qui sont disponibles pour Form Recognizer, sélectionnez **Train Model** (Entraîner le modèle).

    > [!div class="mx-imgBorder"]
    > ![Entraîner un modèle Form Recognizer](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Dans la boîte de dialogue Form Recognizer, fournissez un nom pour la connexion, puis entrez l’URL de point de terminaison et la clé que vous avez récupérées pour la ressource Form Recognizer.

    > [!div class="mx-imgBorder"]
    > ![Nom de connexion pour Form Recognizer](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Cliquez sur **Créer**.

1. Dans la boîte de dialogue **Train Model** (Entraîner le modèle), pour **Source**, entrez l’URL du conteneur dans lequel vous avez chargé les exemples de données.

    > [!div class="mx-imgBorder"]
    > ![Conteneur de stockage pour les exemples de factures](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Cliquez sur **Enregistrer** dans la barre d’outils située en haut.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Configurer l’application logique pour qu’elle utilise une opération d’analyse du formulaire Form Recognizer

Dans cette section, vous allez ajouter l’opération **Analyze Form** (Analyser le formulaire) au workflow. Cette opération utilise le modèle déjà entraîné pour analyser une nouvelle facture fournie à l’application logique.

1. Sélectionnez **Nouvelle étape**, puis, sous **Choisir une action**, recherchez **Form Recognizer**. Dans les résultats qui s’affichent, sélectionnez **Form Recognizer**, puis, sous les actions qui sont disponibles pour Form Recognizer, sélectionnez **Analyze Form** (Analyser le formulaire).

    > [!div class="mx-imgBorder"]
    > ![Analyser un formulaire Form Recognizer](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. Dans la boîte de dialogue **Analyze Form** (Analyser le formulaire), effectuez ce qui suit :

    1. Cliquez sur la zone de texte **Model ID** (ID de modèle), puis, dans la boîte de dialogue qui s’ouvre, sous **Dynamic Content** (Contenu dynamique), sélectionnez **modelId**. En procédant ainsi, vous fournissez à l’application de workflow l’ID du modèle que vous avez entraîné dans la dernière section.

        > [!div class="mx-imgBorder"]
        > ![Utiliser ModelID pour Form Recognizer](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Cliquez sur la zone de texte **Document**, puis, dans la boîte de dialogue qui s’ouvre, sous **Dynamic Content** (Contenu dynamique), sélectionnez **Attachments Content** (Contenu des pièces jointes). En procédant ainsi, vous configurez le flux pour utiliser l’exemple de fichier de facture joint à l’e-mail qui déclenche le workflow.

        > [!div class="mx-imgBorder"]
        > ![Utiliser la pièce jointe pour analyser les factures](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Cliquez sur **Enregistrer** dans la barre d’outils située en haut.

### <a name="extract-the-table-information-from-the-invoice"></a>Procéder à l’extraction des informations du tableau que contient la facture

Dans cette section, vous configurez l’application logique pour extraire les informations du tableau parmi les factures.

1. Sélectionnez **Ajouter une action**, puis, sous **Choisir une action**, recherchez **Composer** et sous les actions disponibles, sélectionnez à nouveau **Composer**.
    ![Extraction des informations du tableau que contient la facture](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. Dans la boîte de dialogue **Composer**, cliquez sur la zone de texte **Entrées**, puis, dans la boîte de dialogue qui s’affiche, sélectionnez **Tableaux**.

    > [!div class="mx-imgBorder"]
    > ![Extraction des informations du tableau que contient la facture](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Cliquez sur **Enregistrer**.

## <a name="test-your-logic-app"></a>Tester votre application logique

Pour tester l’application logique, utilisez les exemples de factures situés dans le dossier **/Test** de l’exemple de jeu de données que vous avez téléchargé à partir de [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Procédez comme suit :

1. Dans le concepteur Azure Logic Apps de votre application, sélectionnez **Exécuter** dans la barre d’outils supérieure. Le workflow est maintenant actif et attend de recevoir un e-mail avec la facture jointe.
1. Envoyez un e-mail avec un exemple de facture à l’adresse e-mail que vous avez fournie lors de la création de l’application logique. Vérifiez que l’e-mail arrive bien dans le dossier que vous avez choisi lors de la configuration de l’application logique.
1. Dès que l’e-mail arrive dans le dossier, le concepteur Logic Apps affiche un écran montrant la progression de chaque étape. Dans la capture d’écran ci-dessous, vous voyez qu’un e-mail contenant une pièce jointe a été reçu et que le workflow est en cours.

    > [!div class="mx-imgBorder"]
    > ![Démarrer le workflow en envoyant un e-mail](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. Lorsque toutes les étapes du workflow sont terminées, le concepteur Logic Apps affiche une coche verte pour chaque étape. Dans la fenêtre du concepteur, sélectionnez **For each 2** (Tous les : 2), puis sélectionnez **Composer**.

    > [!div class="mx-imgBorder"]
    > ![Workflow terminé](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    Dans la zone **SORTIES**, copiez la sortie et collez-la dans un éditeur de texte.

1. Comparez la sortie JSON à l’exemple de facture que vous avez envoyé en pièce jointe dans l’e-mail. Vérifiez que les données JSON correspondent aux données du tableau de la facture.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez configuré un workflow Azure Logic Apps de façon à entraîner un modèle et extraire le contenu d’une facture à l’aide de Form Recognizer. Maintenant, vous allez voir comment créer un jeu de données d’entraînement afin de créer un scénario similaire avec vos propres formulaires.

> [!div class="nextstepaction"]
> [Créer un jeu de données d’apprentissage](build-training-data-set.md)