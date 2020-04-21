---
title: Configurer la gestion des prospects pour Salesforce | Place de marché Azure
description: Configurer la gestion des prospects sur Salesforce pour les clients Place de marché Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263015"
---
# <a name="configure-lead-management-for-salesforce"></a>Configurer la gestion des prospects pour Salesforce

Cet article explique comment configurer votre système Salesforce de manière à traiter les prospects issus de votre offre du marketplace commercial.

> [!Note]
> Place de marché Microsoft Azure ne prend pas en charge les listes préremplies, telles qu’une liste de valeurs pour le champ **Pays**. Assurez-vous qu’aucune liste n’est configurée avant de continuer. Vous pouvez également configurer un [point de terminaison HTTPS](./commercial-marketplace-lead-management-instructions-https.md) ou une [table Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) pour qu’ils reçoivent les prospects.

## <a name="set-up-your-salesforce-system"></a>Configurer votre système Salesforce

1. Connectez-vous à Salesforce.
2. Si vous utilisez l'expérience Salesforce Lighting :
    1. Sélectionnez **Configurer** sur la page d'accueil de Salesforce.
    ![Configuration de Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Sur la page Configuration, accédez au volet de navigation gauche et sélectionnez **Outils de plate-forme -> Paramètres de la fonctionnalité -> Marketing -> Web-to-Lead** .

        ![Web-to-Lead Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Si vous utilisez l'expérience Salesforce Classic :
    1. Sélectionnez **Configurer** sur la page d'accueil de Salesforce.
    ![Configuration de Salesforce Classic](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Sur la page Configuration, accédez au volet de navigation gauche et sélectionnez **Générer -> Personnaliser -> Prospects -> Web-to-Lead**.

        ![Web-to-Lead classique Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

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

1. **E-mail du contact** : fournissez les e-mails des personnes de votre société qui doivent recevoir des notifications par courrier électronique lors de la réception d’un nouveau prospect. Vous pouvez fournir plusieurs e-mails en les séparant par un point-virgule.

1. Sélectionnez **OK**.

Pour vous assurer que vous vous êtes bien connecté à une destination de prospect, cliquez sur le bouton Valider. En cas de réussite, un prospect de test se trouve dans la destination du prospect.

>[!Note]
>Vous devez terminer la configuration du reste de l'offre et la publier avant de pouvoir recevoir des prospects pour cette offre.

![Détails de la connexion - Choisir une destination de prospect](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Détails de la connexion - Choisir une destination de prospect](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
