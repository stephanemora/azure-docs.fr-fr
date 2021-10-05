---
title: Définir la configuration technique de l’offre de visuel Power BI dans l’Espace partenaires pour Microsoft AppSource
description: Découvrez comment définir la configuration technique de l’offre de visuel Power BI dans l’Espace partenaires pour Microsoft AppSource.
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: f544e9b98204f48defee59e614f395c879ecc8e4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700643"
---
# <a name="set-up-power-bi-visual-offer-technical-configuration"></a>Configuration technique de l’offre de visuel Power BI

Dans l’onglet **Configuration technique**, fournissez les fichiers nécessaires pour l’offre de visuel Power BI.

:::image type="content" source="media/power-bi-visual/technical-configuration.png" alt-text="Montre la page Configuration technique dans l’Espace partenaires.":::

## <a name="pbiviz-package"></a>Package PBIVIZ

[Empaquetez votre visuel Power BI](https://docs.microsoft.com/power-bi/developer/visuals/package-visual) dans un package PBIVIZ contenant toutes les métadonnées requises :

- Nom du visuel
- Nom d’affichage
- GUID (voir la remarque ci-dessous)
- Version (voir la remarque ci-dessous)
- Description
- Nom et e-mail de l’auteur

> [!NOTE]
> Si vous mettez à jour ou soumettez à nouveau un visuel :
> - Le GUID doit rester inchangé.
> - Le numéro de version doit être incrémenté entre les mises à jour du package.

## <a name="sample-pbix-report-file"></a>Exemple de fichier de rapport PBIX

Pour présenter votre offre de visuel, aidez les utilisateurs à se familiariser avec ce visuel. Insistez sur la valeur que le visuel apporte à l’utilisateur, et donnez des exemples d’utilisation et des options de mise en forme. Ajoutez une page de conseils à la fin pour donner des conseils et astuces, et expliquer ce qu’il faut éviter de faire. L’exemple de fichier de rapport PBIX doit pouvoir être utilisé hors connexion, sans connexion externe.

> [!NOTE]
> - Le rapport PBIX doit utiliser la même version du visuel que le PBIVIZ.
> - Le fichier de rapport PBIX doit pouvoir être utilisé hors connexion, sans connexion externe.

Sélectionnez **Enregistrer le brouillon** avant de passer dans le menu de navigation de gauche à l’onglet **Gestion des offres**.

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des offres](power-bi-visual-manage-names.md)
