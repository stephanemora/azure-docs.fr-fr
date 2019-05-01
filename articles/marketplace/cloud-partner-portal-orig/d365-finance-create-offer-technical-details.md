---
title: Comment remplir le formulaire d’informations techniques
description: Explique comment entrer les valeurs dans le formulaire des informations techniques pour une nouvelle application Dynamics 365 Business Central.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: dbc38fab5bd8e55f6dd280ecc46af1b1a5ae7ede
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935064"
---
<a name="how-to-fill-out-the-technical-info-form"></a>Comment remplir le formulaire d’informations techniques
===========================================

1.  Dans la section **Choisir le type d’application**, chargez votre fichier de package d’extension (.app) et, si nécessaire, les fichiers de package d’extension dont votre extension dépend.

    ![Informations sur le package d’application](./media/d365-financials/image015.png)

-   **Fichier de package d’extension** : obligatoire. Fichier de package d’extension (.app).

-   **Fichier de package de dépendance** : obligatoire si l’application comporte une dépendance sur une autre application publiée dans AppSource. Le fichier .app d’extension déjà publié sur AppSource, duquel dépend l’application actuelle. 

-   **Fichier de package de bibliothèque** : obligatoire si l’application comporte une dépendance sur une autre application qui *n’est pas* publiée dans AppSource. Fichier .app d’une application existante, mais qui n’a pas été publiée dans AppSource et qui ne le sera pas à l’avenir.

-   **Automatisation de test d’application** : obligatoire. Le package de test de code Visual Studio que vous devez créer pour les tests d’extensions automatisés.

1. Dans la section **Informations complémentaires sur l’extension**, chargez toutes les autres informations complémentaires pour votre extension. Ces informations sont utilisées lors de la validation.

   ![Formulaire d’informations complémentaires pour l’extension d’application](./media/d365-financials/image016.png)


-   **URL vers la documentation du produit** : obligatoire. URL de la documentation de l’extension.

-   **Scénarios d’utilisations clés** : obligatoire. Document qui contient les procédures détaillées de configuration et d’utilisation de l’extension. Vous trouverez un exemple dans l’article [Documentation des scénarios utilisateur](https://docs.microsoft.com/dynamics-nav/compliance/apptest-userscenario/).

-   **Version cible** : obligatoire . Sélectionnez la version sur laquelle déployer l’application. Sélectionnez **actuelle** pour la déployer sur la version actuelle sur le marché. Sélectionnez **version mineure suivante** pour la déployer à la publication de la prochaine version mineure. Sélectionnez **version majeure suivante** pour la déployer à la publication de la prochaine version majeure.

-   **Nécessite la référence SKU Premium** : facultatif. Sélectionnez le bouton Premium si l’application requiert la référence Premium. La gestion des services et de la fabrication sont disponibles uniquement sur Premium. Vous retrouverez des informations détaillées sur les versions Essential et Premium dans l’article [Modification des fonctionnalités affichées](https://docs.microsoft.com/dynamics365/financials/ui-experiences).

-   **Explication des erreurs d’analyse du Code** : facultatif. Document qui répertorie et justifie tout code qui ne respecte pas la configuration requise.

-   **Explication des fonctionnalités de base affectées** : facultatif. Document qui répertorie et décrit toutes les fonctionnalités de base qui sont limitées par cette extension.

-   **Comptes de test** : facultatif. Comptes utilisateur pour les services à distance, les sites web, etc, nécessaires pour effectuer les tests d’utilisation de bout en bout.

-   **Exceptions d’expérience utilisateur** : facultatif. Document qui répertorie et justifie les exigences d’expérience utilisateur qui ne sont pas satisfaites par cette extension.

L’étape suivante consiste à ajouter les détails de la vitrine de votre offre.
