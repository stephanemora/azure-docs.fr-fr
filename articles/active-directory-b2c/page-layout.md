---
title: Versions des mises en page
titleSuffix: Azure AD B2C
description: Historique des versions de mise en page pour la personnalisation de l’interface utilisateur dans les stratégies personnalisées.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2a0a786d3e2135467c7279c76bae273bff0ba2d0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149505"
---
# <a name="page-layout-versions"></a>Versions des mises en page

Les packages de mise en page sont régulièrement mis à jour afin d’ajouter des correctifs et des améliorations à leurs éléments de page. Le journal des modifications suivant indique les modifications introduites dans chaque version.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Page autodéclarée (`selfasserted`)
  - Ajout de la prise en charge des [contrôles d’affichage](display-controls.md) dans les stratégies personnalisées.

## <a name="120"></a>1.2.0

- Toutes les pages
  - Correctifs de l’accessibilité
  - Vous pouvez maintenant ajouter l’attribut `data-preload="true"` dans les balises HTML pour contrôler l’ordre de chargement des fichiers CSS et JavaScript.
    - Chargez les fichiers CSS liés en même temps que votre modèle HTML, de sorte qu’il n’y ait pas de « scintillement » entre le chargement des fichiers.
    - Contrôlez l’ordre dans lequel vos balises `script` sont récupérées et exécutées avant le chargement de la page.
  - Le champ d’e-mail est désormais `type=email`, et les claviers mobiles fournissent les suggestions appropriées
  - Prise en charge de Chrome Translate
- Pages unifiées et autodéclarées
  - Les champs de nom d’utilisateur/adresse e-mail et de mot de passe utilisent désormais l’élément HTML `form` pour permettre à Edge et Internet Explorer (IE) d’enregistrer correctement ces informations.

## <a name="110"></a>1.1.0

- Page d’exception (globalexception)
  - Correctif de l’accessibilité
  - Suppression du message par défaut s’affichant lorsqu’il n’y a aucun contact de la stratégie
  - Suppression du CSS par défaut
- Page MFA (multifactor)
  - Suppression du bouton « Confirmer le code »
  - Il n’est maintenant possible d’entrer que six (6) caractères dans le champ d’entrée du code
  - La page essaie automatiquement de vérifier le code entré lorsqu’un code à six chiffres est saisi, sans cliquer sur le moindre bouton
  - Si le code est incorrect, le contenu du champ d’entrée est automatiquement effacé
  - Après trois (3) tentatives avec un code incorrect, B2C renvoie une erreur à la partie de confiance
  - Correctifs de l’accessibilité
  - Suppression du CSS par défaut
- Page autodéclarée (selfasserted)
  - Suppression de l’alerte d’annulation
  - Classe CSS pour les éléments d’erreur
  - Amélioration de la fonction Afficher/masquer la logique d’erreur
  - Suppression du CSS par défaut
- SSP unifié (unifiedssp)
  - Ajout de l’option de contrôle Maintenir la connexion (KMSI)

## <a name="100"></a>1.0.0

- Version initiale

## <a name="next-steps"></a>Étapes suivantes

Pour plus de détails sur la personnalisation de l’interface utilisateur de vos applications, voir [Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée](custom-policy-ui-customization.md).
