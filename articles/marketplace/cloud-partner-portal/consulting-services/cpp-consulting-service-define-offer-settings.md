---
title: Définir les paramètres de l’offre pour une offre de service de Conseil | Place de marché Azure
description: Définir les paramètres de l’offre dans une offre de service de conseil Azure ou Dynamics 365 dans le portail Cloud Partner pour Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 601ad62bddd1373742b0cab5a388a55cfd52f4bc
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942785"
---
# <a name="offer-settings-tab"></a>Onglet des paramètres de l’offre

Dans l’écran **Nouvelle offre**, la première étape consiste à créer l’identité de l’offre. L’identité de l’offre se compose de trois parties : **ID de l’offre**, **ID de l’éditeur** et **Nom**. Chacune de ces parties est présentée individuellement dans les sections suivantes.

![Créer une offre de service de conseil : onglet Paramètres de l’offre](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>ID d’offre *

Cet identificateur est un nom unique que vous créez lorsque vous soumettez l’offre pour la première fois. Il doit contenir uniquement des caractères alphanumériques en minuscules, des tirets ou des traits de soulignement. L’**ID de l’offre** étant présent dans l’URL, il affecte les résultats des moteurs de recherche. *yourcompanyname_exampleservice* est un exemple.

Comme indiqué dans l’exemple, l’**ID de l’offre** est ajouté à votre ID d’éditeur pour créer un identificateur unique. Cet identificateur unique est exposé sous forme de lien permanent, qui peut être réservé et indexé par les moteurs de recherche.

>[!Note]
>Une fois une offre en ligne, son identificateur ne peut plus être mis à jour.


### <a name="publisher-id"></a>Publisher ID *

Cet identificateur est associé à votre compte. Une fois que vous êtes connecté avec votre compte professionnel, votre **ID d’éditeur** s’affiche dans le menu déroulant.


### <a name="name"></a>Nom*

Cette chaîne s’affiche en tant que nom de l’offre sur AppSource ou la Place de marché Microsoft Azure. La zone **Name** est limitée à 50 caractères. Le réviseur devra peut-être modifier votre titre pour ajouter la durée et le type d’offre au nom de votre offre.

L’exemple suivant montre comment se compose le nom de l’offre. 

![Créer une offre de service de conseil](media/cppsampleconsultingoffer.png)

Le nom de l’offre se compose de quatre parties :

-   **Durée :** définie sous l’onglet **Storefront Details** (Informations sur les vitrines) de l’éditeur. La durée peut être exprimée en heures, en jours ou en semaines.
-   **Type de service :** définie sous l’onglet **Storefront Details** (Informations sur les vitrines) de l’éditeur. Les types de services sont `Assessment`, `Briefing`, `Implementation`, `Proof of concept` et `Workshop`.
-   **Préposition :** insérée par le réviseur.
-   **Nom :** défini dans la page **Offer Settings** (Paramètres de l’offre).

>[!Note]
>La zone **Name** est limitée à 50 caractères. Le réviseur devra peut-être modifier votre titre pour ajouter la durée et le type d’offre au nom de votre offre.

La liste suivante contient plusieurs noms d’offres bien nommés :

-   Fondamentaux pour les services professionnels : Réunion d’information d’une heure
-   Plateforme de migration cloud : Réunion d’information d’une heure
-   PowerApps et Microsoft Flow : Atelier d’une journée
-   Azure Machine Learning Services : Preuve de concept de 3 semaines
-   Solution de vente au détail physique et en ligne : Réunion d’information d’une heure
-   Apportez vos propres données : Atelier d’une semaine
-   Cloud Analytics : Atelier de 3 jours
-   Formation Power BI : Atelier de 3 jours
-   Solution de gestion des ventes : Implémentation d’une semaine
-   Démarrage rapide CRM : Atelier d’une journée
-   Dynamics 365 pour les ventes : Évaluation de 2 jours

Une fois que vous avez rempli l’onglet **Offer Settings** (Paramètres de l’offre), enregistrez votre soumission. Le nom de l’offre s’affiche maintenant au-dessus de l’éditeur, et vous pouvez le trouver dans **All Offers**.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez entrer les [Informations sur la vitrine et indiquer si vous souhaitez effectuer la publication sur la Place de marché Microsoft Azure ou sur AppSource](./cpp-consulting-service-storefront-details.md).
