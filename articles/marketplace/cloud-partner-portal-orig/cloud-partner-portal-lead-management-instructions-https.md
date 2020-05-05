---
title: Configuration de la gestion des prospects à l’aide d’un point de terminaison HTTPS | Place de marché Azure
description: Découvrez comment utiliser un point de terminaison HTTP pour gérer les prospects de Microsoft AppSource et de la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770166"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configuration de la gestion des prospects à l’aide d’un point de terminaison HTTPS

Vous pouvez utiliser un point de terminaison HTTPS pour gérer les prospects de Microsoft AppSource et de la Place de marché Azure. Ces prospects peuvent être écrits dans un système de Gestion des relations avec la clientèle (CRM) ou envoyés sous la forme d’une notification par e-mail. Cet article décrit comment utiliser le service d’automatisation [Microsoft Power Automate](https://powerapps.microsoft.com/automate-processes/) pour configurer la gestion des prospects.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Créer un flux à l’aide de Microsoft Power Automate

1. Ouvrez la page web [Power Automate](https://flow.microsoft.com/). Sélectionnez **Se connecter** ou **S’inscrire gratuitement** pour créer un compte Flow gratuit.

1. Connectez-vous et sélectionnez **Mes flux** dans la barre de menus.
    > [!div class="mx-imgBorder"]
    > ![My flows](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. Sous **+ Nouveau**, sélectionnez **+ Instantané - à partir de zéro**.
    > [!div class="mx-imgBorder"]
    > ![Create from blank](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Nommez votre flux, puis sous **Choisir comment déclencher ce flux**, sélectionnez **Lors de la réception d’une requête HTTP**.

    > [!div class="mx-imgBorder"]
    > ![Select the HTTP request received trigger](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Cliquez sur l’étape du flux pour la développer.

    > [!div class="mx-imgBorder"]
    > ![Expand the flow step](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. Utilisez l’une des méthodes suivantes pour configurer le **schéma JSON du corps de la demande** :

   - Copiez le [schéma JSON](#json-schema) indiqué à la fin de cet article dans la zone de texte **Schéma JSON du corps de la requête**.
   - Sélectionnez **Utiliser l’exemple de charge utile pour générer le schéma**. Dans la zone de texte **Entrer ou coller un exemple de charge utile JSON**, collez [l’exemple JSON](#json-example). Sélectionnez **Terminé** pour créer le schéma.

   >[!Note]
   >À ce stade du flux, vous pouvez vous connecter à un système CRM ou configurer une notification par e-mail.

### <a name="to-connect-to-a-crm-system"></a>Pour vous connecter à un système CRM

1. Sélectionnez **+ Nouvelle étape**.
2. Choisissez le système CRM de votre choix avec l’action permettant de créer un enregistrement. La capture d’écran suivante montre **Dynamics 365 - Créer un enregistrement** comme exemple.

    ![Créer un enregistrement](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Indiquez le **Nom de l’organisation** qui est l’entrée de connexion de votre connecteur. Sélectionnez **Prospects** dans la liste déroulante **Nom de l’entité**.

    ![Sélection de prospects](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow affiche un formulaire dans lequel fournir des informations sur les prospects. Vous pouvez mapper des éléments de la demande d’entrée en choisissant d’ajouter du contenu dynamique. La capture d’écran suivante présente **OfferTitle** comme exemple.

    ![Ajout de contenu dynamique](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Mappez les champs de votre choix, puis sélectionnez **Enregistrer** pour enregistrer votre flux.

6. Une URL HTTP POST est créée dans la requête. Copiez cette URL et utilisez-la comme point de terminaison HTTPS.

    ![URL HTTP POST](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Pour configurer une notification par e-mail

1. Sélectionnez **+ Nouvelle étape**.
2. Sous **Choisir une action**, sélectionnez **Actions**.
3. Sous **Actions**, sélectionnez **Envoi d’un courrier électronique**.

    ![Ajouter une action de messagerie](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. Dans **Envoyer un e-mail**, configurez les champs obligatoires suivants :

   - **À** : entrez au moins une adresse e-mail valide.
   - **Objet** : Flow vous donne la possibilité d’ajouter du contenu dynamique, par exemple **LeadSource**, dans la capture d’écran suivante.

     ![Ajouter une action de messagerie à l’aide de contenu dynamique](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Corps** : dans la liste de contenu dynamique, ajoutez les informations souhaitées dans le corps de l’e-mail. Par exemple, LastName, FirstName, Email et Company.

   Quand vous avez terminé de configurer la notification par e-mail, elle se présente comme l’exemple affiché dans la capture d’écran suivante.

   ![Ajouter une action de messagerie](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Sélectionnez **Enregistrer** pour terminer votre flux.

6. Une URL HTTP POST est créée dans la requête. Copiez cette URL et utilisez-la comme point de terminaison HTTPS.

    ![URL HTTP POST](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configuration de votre offre pour l’envoi des prospects au point de terminaison HTTPS

Quand vous configurez les informations de gestion des prospects pour votre offre, sélectionnez **Point de terminaison HTTPS** pour la **Destination du prospect**, puis collez l’URL HTTP POST que vous avez copiée à l’étape précédente.  

![Ajout de contenu dynamique](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Quand des prospects sont générés, Microsoft les envoie à votre flux Power Automate, qui les achemine vers le système CRM ou l’adresse e-mail que vous avez configurée.

## <a name="json-schema-and-example"></a>Schéma JSON et exemple

L’exemple de test JSON utilise le schéma suivant :

### <a name="json-schema"></a>Schéma JSON

``` json
{
  "$schema": "https://json-schema.org/draft-04/schema#",
  "definitions": {},
  "id": "http://example.com/example.json",
  "properties": {
    "ActionCode": {
      "id": "/properties/ActionCode",
      "type": "string"
    },
    "OfferTitle": {
      "id": "/properties/OfferTitle",
      "type": "string"
    },
    "LeadSource": {
      "id": "/properties/LeadSource",
      "type": "string"
    },
    "Description": {
      "id": "/properties/Description",
      "type": "string"
    },
    "UserDetails": {
      "id": "/properties/UserDetails",
      "properties": {
        "Company": {
          "id": "/properties/UserDetails/properties/Company",
          "type": "string"
        },
        "Country": {
          "id": "/properties/UserDetails/properties/Country",
          "type": "string"
        },
        "Email": {
          "id": "/properties/UserDetails/properties/Email",
          "type": "string"
        },
        "FirstName": {
          "id": "/properties/UserDetails/properties/FirstName",
          "type": "string"
        },
        "LastName": {
          "id": "/properties/UserDetails/properties/LastName",
          "type": "string"
        },
        "Phone": {
          "id": "/properties/UserDetails/properties/Phone",
          "type": "string"
        },
        "Title": {
          "id": "/properties/UserDetails/properties/Title",
          "type": "string"
        }
      },
      "type": "object"
    }
  },
  "type": "object"
}
```

Vous pouvez copier et modifier l’exemple JSON suivant pour l’utiliser comme test dans votre flux.

### <a name="json-example"></a>Exemple JSON

```json
{
  "UserDetails": {
    "FirstName": "Some",
    "LastName": "One",
    "Email": "someone@contoso.com",
    "Phone": "16175555555",
    "Country": "USA",
    "Company": "Contoso",
    "Title": "Esquire"
 },
  "LeadSource": "AzureMarketplace",
  "ActionCode": "INS",
  "OfferTitle": "Test Microsoft",
  "Description": "Test run through Power Automate"
}
```

## <a name="next-steps"></a>Étapes suivantes

Si vous ne l’avez pas déjà fait, configurez des [prospects](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) dans le portail Cloud Partner.
