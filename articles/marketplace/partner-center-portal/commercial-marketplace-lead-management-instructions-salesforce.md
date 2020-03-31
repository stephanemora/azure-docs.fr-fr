---
title: Configurer la gestion des prospects pour Salesforce | Place de marché Azure
description: Configurer la gestion des prospects sur Salesforce pour les clients Place de marché Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 1720026b4beff941b02a60cd1c755a043d66bdb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281491"
---
# <a name="configure-lead-management-for-salesforce"></a>Configurer la gestion des prospects pour Salesforce

Cet article explique comment configurer votre système Salesforce de manière à traiter les prospects issus de votre offre de la Place de marché.

## <a name="set-up-your-salesforce-system"></a>Configurer votre système Salesforce

1. Connectez-vous à Salesforce.
2. Si vous utilisez l'expérience Salesforce Lighting :
    1. Sélectionnez **Configurer** sur la page d'accueil de Salesforce.
    ![Configuration de Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Sur la page Configuration, accédez au volet de navigation gauche et sélectionnez **Outils de plate-forme -> Paramètres de la fonctionnalité -> Marketing -> Web-to-Lead** .
    ![Salesforce - Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Si vous utilisez l'expérience Salesforce Classic :
    1. Sélectionnez **Configurer** sur la page d'accueil de Salesforce.
    ![Configuration de Salesforce Classic](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Sur la page Configuration, accédez au volet de navigation gauche et sélectionnez **Générer -> Personnaliser -> Prospects -> Web-to-Lead**.
    ![Salesforce Classic - Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Les autres instructions sont identiques, quelle que soit l'expérience Salesforce que vous utilisez.

4. Sur la **page Configuration de Web-to-Lead**, sélectionnez le bouton **Créer un formulaire Web-to-Lead**.
5. Dans **Configuration de Web-to-Lead**, sélectionnez **Créer un formulaire Web-to-Lead**.
    ![Salesforce - Configuration de Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. Dans **Créer un formulaire Web-to-Lead**, vérifiez que la case `the Include reCAPTCHA in HTML` est décochée et sélectionnez **Générer**. 
    ![Salesforce - Créer un formulaire Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Du texte HTML s'affiche. Recherchez le texte « oid », copiez la **valeur oid** à partir du texte HTML (uniquement le texte entre guillemets) et enregistrez-la. Vous collerez cette valeur dans le champ **Identificateur de l'organisation** du portail de publication.
    ![Salesforce - Créer un formulaire Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Sélectionnez **Terminé**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Configurer votre offre pour envoyer des prospects à Salesforce

Lorsque vous êtes prêt à configurer les informations de gestion des prospects pour votre offre sur le portail de publication, procédez comme suit :

1. Accédez à la page **Configuration de l'offre** de votre offre.
1. Sélectionnez **Connexion** dans la section Gestion des prospects.
    ![Gestion des prospects - Connecter](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Dans la fenêtre contextuelle des détails de la connexion, sélectionnez **Salesforce** pour définir la **Destination du prospect** et collez la valeur `oid` du formulaire Web-to-Lead que vous avez créé lors des étapes précédentes dans le champ **Identificateur de l'organisation**.

1. Sélectionnez **Enregistrer**. 

    >[!Note]
    >Vous devez terminer la configuration du reste de l'offre et la publier avant de pouvoir recevoir des prospects pour cette offre.

    ![Détails de la connexion - Choisir une destination de prospect](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Détails de la connexion - Choisir une destination de prospect](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)
