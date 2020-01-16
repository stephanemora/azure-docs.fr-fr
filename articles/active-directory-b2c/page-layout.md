---
title: Sélectionner une mise en page - Azure Active Directory B2C
description: Découvrez comment sélectionner une mise en page dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b8bf44893bf23502aaf8c446d9e6d7c9022bfce3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425657"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Sélectionner une mise en page dans Azure Active Directory B2C à l’aide de stratégies personnalisées

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Vous pouvez activer le code JavaScript côté client dans vos stratégies Azure Active Directory B2C (Azure AD B2C) que vous utilisiez des flux d’utilisateurs ou des stratégies personnalisées. Pour activer JavaScript pour vos applications, vous devez ajouter un élément à votre [stratégie personnalisée](active-directory-b2c-overview-custom.md), puis sélectionner une mise en page et utiliser le paramètre [b2clogin.com](b2clogin.md) dans vos requêtes.

Une mise en page désigne une association d’éléments qu’Azure AD B2C fournit et du contenu que vous fournissez.

Cet article traite de la sélection d’une mise en page dans Azure AD B2C en configurant celle-ci dans une stratégie personnalisée.

> [!NOTE]
> Si vous voulez activer JavaScript pour les flux utilisateur, consultez [Versions des mises en page et JavaScript dans Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Remplacer les valeurs DataUri

Dans vos stratégies personnalisées, [ContentDefinitions](contentdefinitions.md) peut définir les modèles HTML utilisés dans le parcours utilisateur. **ContentDefinition** contient un **DataUri** qui fait référence aux éléments de page fournis par Azure AD B2C. **LoadUri** désigne le chemin d’accès relatif au contenu HTML et CSS que vous fournissez.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Pour sélectionner une mise en page, modifiez les valeurs **DataUri** dans l’élément [ContentDefinitions](contentdefinitions.md) au sein de vos stratégies. En passant des anciennes valeurs **DataUri** aux nouvelles valeurs, vous sélectionnez un package immuable. Ce package présente l’avantage de ne jamais changer ni provoquer de comportement inattendu dans votre page.

Pour spécifier une mise en page dans vos stratégies personnalisées si celles-ci utilisent une ancienne valeur **DataUri**, insérez `contract` entre `elements` et le type de page (par exemple, `selfasserted`), puis spécifiez le numéro de version. Par exemple :

| Ancienne valeur DataUri | Nouvelle valeur DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Journal des modifications de version

Les packages de mise en page sont régulièrement mis à jour afin d’ajouter des correctifs et des améliorations à leurs éléments de page. Le journal des modifications suivant indique les modifications introduites dans chaque version.

### <a name="200"></a>2.0.0

- Page autodéclarée (`selfasserted`)
  - Ajout de la prise en charge des [contrôles d’affichage](display-controls.md) dans les stratégies personnalisées.

### <a name="120"></a>1.2.0

- Toutes les pages
  - Correctifs de l’accessibilité
  - Vous pouvez maintenant ajouter l’attribut `data-preload="true"` dans les balises HTML pour contrôler l’ordre de chargement des fichiers CSS et JavaScript. Il s’agit entre autres des scénarios suivants :
    - Utilisez l’attribut sur votre lien CSS pour charger le fichier CSS en même temps que votre fichier HTML. Cela évite l’effet de « scintillement » entre le chargement des fichiers
    - Cet attribut vous permet de contrôler l’ordre dans lequel les balises de script sont récupérées (fetch) et exécutées avant le chargement de la page
  - Le champ d’e-mail est désormais `type=email`, et les claviers mobiles fournissent les suggestions appropriées
  - Prise en charge de Chrome Translate
- Page unifiée et autodéclarée
  - Les champs de nom d’utilisateur/e-mail et de mot de passe utilisent désormais l’élément de formulaire HTML.  Edge et Internet Explorer peuvent ainsi enregistrer correctement ces informations

### <a name="110"></a>1.1.0

- Page d’exception (globalexception)
  - Correctif de l’accessibilité
  - Suppression du message par défaut s’affichant lorsqu’il n’y a aucun contact de la stratégie
  - Suppression du CSS par défaut
- Page MFA (multifactor)
  - Suppression du bouton « Confirmer le code »
  - Il n’est maintenant possible d’entrer que six (6) caractères dans le champ d’entrée du code
  - La page essaie automatiquement de vérifier le code entré lorsqu’un code à six chiffres est saisi, sans cliquer sur le moindre bouton
  - Si le code est incorrect, le champ d’entrée est automatiquement effacé
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

### <a name="100"></a>1.0.0

- Version initiale

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment personnaliser l’interface utilisateur de vos applications, consultez [Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
