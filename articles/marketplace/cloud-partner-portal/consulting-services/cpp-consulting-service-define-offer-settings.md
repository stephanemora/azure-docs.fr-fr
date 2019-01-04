---
title: Offre de service de conseil Azure et Dynamics 365 - Définir les paramètres de l’offre | Microsoft Docs
description: Guide pour la définition des paramètres de l’offre de service de conseil Azure ou Dynamics 365 sur le portail Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 6ce51dc9bbcdc7b46593a4800c4fce07a42f3171
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345065"
---
# <a name="offer-settings-tab"></a>Onglet des paramètres de l’offre

Dans l’écran **Nouvelle offre**, la première étape consiste à créer l’identité de l’offre. L’identité de l’offre se compose de trois parties : **ID de l’offre**, **ID de l’éditeur** et **Nom**. Chacune de ces parties est présentée individuellement dans les sections suivantes.

![Création d’une offre de service de conseil : onglet des paramètres de l’offre](media/consultingoffer-settings-tab.png)

*ID de l’offre*

Cet identificateur est un nom unique que vous créez lorsque vous soumettez l’offre pour la première fois. Il doit contenir uniquement des caractères alphanumériques en minuscules, des tirets ou des traits de soulignement. L’**ID de l’offre** est visible dans l’URL et a une incidence sur les résultats des moteurs de recherche. Par exemple : *yourcompanyname_exampleservice*

Comme indiqué dans l’exemple, l’**ID de l’offre** est ajouté à votre ID d’éditeur pour créer un identificateur unique. Cette information est exposée sous forme de lien permanent, qui peut être réservé et indexé par les moteurs de recherche.

*Une fois une offre active, son identificateur ne peut plus être mis à jour*

*ID de l’éditeur*

Cet identificateur est associé à votre compte. Lorsque vous êtes connecté avec votre compte professionnel, votre **ID d’éditeur** s’affiche dans le menu déroulant.

*Nom*

Cette chaîne s’affiche en tant que nom de l’offre sur la Place de marché AppSource ou Azure. Le champ de *nom* est limité à 50 caractères.  Le réviseur devra peut-être modifier votre titre pour permettre l’ajout de la durée et du type d’offre à son nom.

>[!Note]
>Important : N’entrez ici que le nom réel du service. Ne mentionnez pas la durée ni le type de service.

L’exemple suivant par Edgewater Fullscope montre comment se compose le nom de l’offre. Le nom de l’offre s’affiche comme suit :

![Création d’une offre de service de conseil](media/cppsampleconsultingoffer.png)

Le nom de l’offre se compose de quatre parties :

-   **Durée** : définie sous l’onglet **Détails de la vitrine** de l’éditeur. La durée peut être exprimée en heures, en jours ou en semaines.
-   **Type de service** : défini sous l’onglet **Détails de la vitrine** de l’éditeur. Les types de services sont `Assessment`, `Briefing`, `Implementation`, `Proof of concept` et `Workshop`.
-   **Préposition** : insérée par le réviseur.
-   **Nom** : défini dans la page **Paramètres de l’offre**.

>[!Note]
>Le champ de nom est limité à 50 caractères. Le nom soumis devra peut-être être modifié par le réviseur pour permettre l’ajout de la durée et du type d’offre à son nom.

La liste suivante contient plusieurs noms d’offres bien nommés :

-   Fondamentaux pour les services professionnels : Réunion d’information d’une heure
-   Plateforme de migration cloud : Réunion d’information d’une heure
-   PowerApps et Microsoft Flow : Atelier d’une journée
-   Azure Machine Learning Services : Preuve de concept de 3 semaines
-   Solution de vente au détail physique et en ligne : Réunion d’information d’une heure
-   Apportez vos propres données : Atelier d’une semaine
-   Cloud Analytics : Atelier de 3 jours
-   Formation Power BI : Atelier de 3 jours
-   Solution de gestion des ventes : Implémentation d’une semaine
-   Démarrage rapide CRM : Atelier d’une journée
-   Dynamics 365 pour les ventes : Évaluation de 2 jours

Une fois que vous avez complété l’onglet **Paramètres de l’offre**, vous pouvez enregistrer votre soumission. Le nom de l’offre s’affiche maintenant au-dessus de l’éditeur, et vous pouvez le retrouver dans toutes les offres.

**Étapes suivantes**

Vous pouvez entrer les [détails de la vitrine et indiquer si vous souhaitez effectuer la publication sur la Place de marché Microsoft Azure ou dans AppSource](./cpp-consulting-service-storefront-details.md).