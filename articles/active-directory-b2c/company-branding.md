---
title: Ajout d’une personnalisation à la page de connexion de l’organisation
titleSuffix: Azure AD B2C
description: Découvrez comment ajouter la personnalisation de votre organisation aux pages Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: bee81876b066b9fc1ea69c418ee4d0839db27b3c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97110969"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-b2c-pages"></a>Ajout d’une personnalisation aux pages Azure Active Directory B2C de l’organisation

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Vous pouvez personnaliser vos pages Azure AD B2C avec un logo de bannière, une image et une couleur d’arrière-plan avec la fonctionnalité [Personnalisation de la société](../active-directory/fundamentals/customize-branding.md) d’Azure Active Directory. La personnalisation de la société comprend l’inscription, la connexion, la modification de profil et la réinitialisation de mot de passe. 

> [!TIP]
> Pour personnaliser d’autres aspects de vos pages de flux utilisateur que le logo de la bannière, l’image et la couleur d’arrière-plan, consultez [Guide pratique pour personnaliser l’interface utilisateur avec un modèle HTML](customize-ui-with-html.md).

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


Pour personnaliser vos pages de flux utilisateur, vous devez commencer par configurer la marque de la société dans Azure Active Directory, puis l’activer dans les mises en page de vos flux utilisateur dans Azure AD B2C.

## <a name="configure-company-branding"></a>Configurer la marque de la société

Commencez par définir le logo de bannière, l’image d’arrière-plan et la couleur d’arrière-plan dans **Marque de la société**.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez le filtre **Annuaire et abonnement** dans le menu supérieur, puis l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sous **Gérer**, sélectionnez **Marque de la société**.
1. Suivez les étapes décrites dans [Personnaliser la page de connexion Azure Active Directory de votre organisation](../active-directory/fundamentals/customize-branding.md).

Lorsque vous configurez la marque de la société dans Azure AD B2C, gardez à l’esprit les points suivants :

* La personnalisation de la marque société dans Azure AD B2C est actuellement limitée à l’**image d’arrière-plan**, au **logo de bannière** et à la **couleur d’arrière-plan**. Les autres propriétés dans le volet Marque de la société, telles que les **Paramètres avancés**, ne sont *pas prises en charge*.
* Dans vos pages de flux utilisateur, la couleur d’arrière-plan est affichée avant le chargement de l’image d’arrière-plan. Nous vous suggérons de choisir une couleur d’arrière-plan proche des couleurs de votre image d’arrière-plan pour offrir une expérience de chargement homogène.
* Le logo de bannière apparaît dans les e-mails de vérification envoyés aux utilisateurs quand ceux-ci démarrent un flux utilisateur d’inscription.

::: zone pivot="b2c-user-flow"

## <a name="enable-branding-in-user-flow-pages"></a>Activer la marque dans les pages de flux utilisateur

Une fois que vous avez configuré la marque de la société, activez-la dans vos flux utilisateur.

1. Dans le menu de gauche du portail Azure, sélectionnez **Azure AD B2C**.
1. Sous **Stratégies**, sélectionnez **Flux utilisateur (stratégies)** .
1. Sélectionnez le flux utilisateur pour lequel vous souhaitez activer la marque de la société. La marque de la société n’est **pas prise en charge** pour les types de flux utilisateur *Connexion* et *Modification de profil* standard.
1. Sous **Personnaliser**, sélectionnez **Mises en page**, puis sélectionnez la mise en page à personnaliser. Par exemple, sélectionnez **Page de connexion ou d’inscription unifiée**.
1. Pour **Version Mise en page (préversion)** , choisissez la version **1.2.0** ou version ultérieure.
1. Sélectionnez **Enregistrer**.

Si vous souhaitez personnaliser toutes les pages du flux utilisateur, définissez la version de mise en page pour chaque mise en page dans le flux utilisateur.

![Sélection de mise en page dans Azure AD B2C sur le portail Azure](media/company-branding/portal-02-page-layout-select.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="select-a-page-layout"></a>Sélectionner une mise en page

Pour activer la personnalisation de la société, définissez une [version de mise en page](contentdefinitions.md#migrating-to-page-layout) avec la version `contract` de la page pour *toutes* les définitions de contenu de votre stratégie personnalisée. Le format de la valeur doit contenir le mot `contract`: _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_. Pour spécifier une mise en page dans vos stratégies personnalisées qui utilisent une ancienne valeur de **DataUri**, procédez comme suit.

Découvrez comment [migrer vers la mise en page](contentdefinitions.md#migrating-to-page-layout) avec la version de la page.

L’exemple suivant montre les identificateurs de définition de contenu et les **DataUri** correspondants avec le contrat de page : 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

::: zone-end

L’exemple suivant présente un logo de bannière et une image d’arrière-plan personnalisés sur une page de flux utilisateur *Inscription et connexion* qui utilise le modèle Bleu océan :

![Page d’inscription/connexion personnalisée servie par Azure AD B2C](media/company-branding/template-ocean-blue-branded.png)

## <a name="use-company-branding-assets-in-custom-html"></a>Utiliser des ressources de marque de société dans du code HTML personnalisé

Pour utiliser les ressources de personnalisation de votre société dans du [code HTML personnalisé](customize-ui-with-html.md), ajoutez les balises suivantes en dehors de la balise `<div id="api">` :

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

La source de l’image est remplacée par celle de l’image d’arrière-plan et du logo de bannière.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment personnaliser l’interface utilisateur de vos applications, consultez [Personnalisation de l’interface utilisateur d’une application dans Azure Active Directory B2C](customize-ui-with-html.md).