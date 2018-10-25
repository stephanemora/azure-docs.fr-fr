---
title: Point de terminaison HTTPS | Microsoft Docs
description: Configurez la gestion des prospects pour HTTPS.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806022"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Configuration de la gestion des prospects à l’aide d’un point de terminaison HTTPS

Vous pouvez utiliser un point de terminaison HTTPS pour gérer les prospects Place de marché Microsoft Azure et Microsoft AppSource, qui peuvent être écrits dans un système CRM. Cet article décrit comment configurer la gestion des prospects à l’aide du service d’automatisation Microsoft Flow.


## <a name="create-a-flow-using-microsoft-flow"></a>Création d’un flux à l’aide de Microsoft Flow

1.  Ouvrez la page web [Flow](https://flow.microsoft.com/). Sélectionnez **Se connecter** ou **S’inscrire gratuitement** pour créer un compte Flow gratuit.

2.  Connectez-vous et sélectionnez **Mes flux** dans la barre de menus.

    ![Mes flux](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  Sélectionnez **Créer entièrement**.

    ![Créer entièrement](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  Sélectionnez le connecteur **Requête/Réponse**, puis recherchez le déclencheur de requête. 

    ![Créer entièrement](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. Sélectionnez le déclencheur **Requête**.
    ![Déclencheur de requête](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  Copiez **l’exemple JSON** indiqué à la fin de cet article dans le **Schéma JSON du corps de la demande**.

7.  Ajoutez une nouvelle étape, puis choisissez le système CRM de votre choix avec l’action permettant de créer un enregistrement. La capture d’écran suivante affiche l’exemple **Dynamics 365 - Créer un enregistrement**.

    ![Créer un enregistrement](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  Indiquez les entrées de connexion de votre connecteur, puis sélectionnez l’entité **Prospects**.

    ![Sélection de prospects](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  Flow affiche un formulaire dans lequel fournir des informations de prospect. Vous pouvez mapper des éléments de la demande d’entrée en choisissant d’ajouter du contenu dynamique.

    ![Ajout de contenu dynamique](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  Mappez les champs de votre choix, puis sélectionnez **Enregistrer** pour enregistrer votre flux.

11. Une URL HTTP POST est créée dans la fenêtre Requête. Copiez cette URL et utilisez-la comme point de terminaison HTTPS.

    ![URL HTTP POST](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Configuration de votre offre pour l’envoi des prospects au point de terminaison HTTPS

Lorsque vous configurez les informations de gestion des prospects pour votre offre, sélectionnez **Point de terminaison HTTPS** pour la zone Destination du prospect et collez l’URL HTTP POST que vous avez copiée à l’étape précédente.  

![Ajout de contenu dynamique](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

Lorsque des prospects sont générés, Microsoft les envoie vers le service Flow, qui est acheminé vers le système CRM que vous avez configuré.


## <a name="json-example"></a>Exemple JSON

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
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
