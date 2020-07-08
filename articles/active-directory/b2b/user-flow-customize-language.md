---
title: Personnalisation de la langue dans les flux utilisateurs Azure AD
description: Apprenez-en davantage sur la personnalisation de l’expérience linguistique dans vos flux utilisateur.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/06/2020
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd6423ebbdba11cd1b0e0c2d00cfd36aa745e72
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85551141"
---
# <a name="language-customization-in-azure-active-directory-preview"></a>Personnalisation de la langue dans Azure Active Directory (préversion)

> [!NOTE]
> L’inscription en libre-service est une fonctionnalité en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La personnalisation de la langue dans Azure Active Directory (Azure AD) permet à votre flux utilisateur de prendre en charge plusieurs langues pour répondre aux besoins de votre utilisateur. Microsoft fournit les traductions pour [36 langues](#supported-languages). Même si votre expérience n’est disponible que dans une seule langue, vous pouvez personnaliser les noms d’attributs dans la page de la collection d’attributs.

## <a name="how-language-customization-works"></a>Fonctionnement de la personnalisation de la langue

Par défaut, la personnalisation de la langue est activée pour les utilisateurs qui s’inscrivent pour garantir une expérience d’inscription cohérente. Vous pouvez utiliser des langues pour modifier les chaînes affichées pour les utilisateurs dans le cadre du processus de collecte d’attribut lors de l’inscription.

> [!NOTE]
> Si vous utilisez des attributs utilisateur personnalisés, vous devez fournir vos propres traductions. Pour plus d’informations, consultez [Personnaliser vos chaînes](#customize-your-strings).

## <a name="customize-your-strings"></a>Personnaliser vos chaînes

La personnalisation de la langue vous permet de personnaliser les chaînes de votre flux utilisateur.

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Sous **Services Azure**, sélectionnez **Azure Active Directory**.
3. Dans le menu de gauche, sélectionnez **Identités externes**.
4. Sélectionnez **Flux utilisateur (préversion)** .
3. Sélectionnez le flux utilisateur pour lequel vous souhaitez activer les traductions.
4. Sélectionnez **Langues**.
5. Dans la page **Langues** du flux utilisateur, sélectionnez une langue que vous souhaitez personnaliser.
6. Développez la **Page de collection d’attributs**.
7. Sélectionnez **Télécharger les valeurs par défaut** (ou **Télécharger les valeurs de remplacements** si vous avez déjà modifié cette langue).

À la suite de ces opérations, vous obtenez un fichier JSON que vous pouvez utiliser pour commencer à modifier vos chaînes.

### <a name="change-any-string-on-the-page"></a>Modifier des chaînes sur la page

1. Ouvrez le fichier JSON téléchargé précédemment dans un éditeur JSON.
1. Recherchez l’élément que vous souhaitez modifier. Vous pouvez trouver le `StringId` de la chaîne que vous recherchez, ou recherchez l’attribut `Value` que vous souhaitez modifier.
1. Mettez à jour l’attribut `Value` avec ce que vous souhaitez afficher.
1. Pour chaque chaîne que vous souhaitez modifier, modifiez `Override` en `true`.
1. Enregistrez le fichier et chargez vos modifications. (Vous pouvez trouver le contrôle de téléchargement au même emplacement que celui d’où vous avez téléchargé le fichier JSON).

> [!IMPORTANT]
> Si vous devez remplacer une chaîne, veillez à définir la valeur `Override` sur `true`. Si la valeur n’est pas modifiée, l’entrée est ignorée.

### <a name="change-extension-attributes"></a>Modifier les attributs d’extension

Si vous souhaitez modifier la chaîne d’un attribut utilisateur personnalisé, ou en ajouter un au fichier JSON, le format suivant est utilisé :

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Remplacez `<ExtensionAttribute>` par le nom de votre attribut utilisateur personnalisé.

Remplacez `<ExtensionAttributeValue>` par la nouvelle chaîne à afficher.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fournir la liste des valeurs à l’aide de LocalizedCollections

Si vous souhaitez fournir une liste définie de valeurs pour les réponses, vous devez créer un attribut `LocalizedCollections`. Un élément `LocalizedCollections` est un tableau de paires `Name` et `Value`. L’ordre des éléments sera l’ordre dans lequel ils apparaissent. Pour ajouter `LocalizedCollections`, utilisez le format suivant :

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` est l’attribut utilisateur auquel cet attribut `LocalizedCollections` apporte une réponse.
* `Name` est la valeur affichée à l’utilisateur.
* `Value` est ce qui est renvoyé dans la revendication lorsque cette option est sélectionnée.

### <a name="upload-your-changes"></a>Charger vos modifications

1. Une fois que vous avez terminé de modifier votre fichier JSON, revenez à votre locataire.
1. Sélectionnez **Flux d’utilisateurs** et cliquez sur celui pour lequel vous souhaitez activer la traduction.
1. Sélectionnez **Langues**.
1. Sélectionnez la langue dans laquelle vous voulez traduire.
1. Sélectionnez la **Page de collection d’attributs**.
1. Sélectionnez l’icône de dossier et sélectionnez le fichier JSON à charger.

Ces changements sont automatiquement enregistrés dans votre flux utilisateur.

## <a name="additional-information"></a>Informations supplémentaires

### <a name="page-ui-customization-labels-as-overrides"></a>Étiquettes de personnalisation de l’interface utilisateur de la page comme remplacements

Lorsque vous activez la personnalisation de la langue, les modifications précédemment appliquées aux étiquettes via la personnalisation de l’interface utilisateur de la page sont conservées dans un fichier JSON pour l’anglais (en). Vous pouvez continuer à modifier vos étiquettes et les autres chaînes en chargeant des ressources de langue dans « Personnalisation de la langue ».

### <a name="up-to-date-translations"></a>Traductions à jour

Microsoft s’engage à fournir des traductions à jour pour que vous puissiez les utiliser. Nous ne cessons d’améliorer nos traductions pour qu’elles soient conformes à vos attentes. Microsoft identifiera les bogues et les modifications dans la terminologie globale et créera les mises à jour qui s’adapteront en toute transparence à votre flux utilisateur.

### <a name="support-for-right-to-left-languages"></a>Prise en charge des langues s’écrivant de droite à gauche

Microsoft n’assure actuellement pas la prise en charge des langues s’écrivant de droite à gauche. Pour cela, vous pouvez utiliser des paramètres régionaux personnalisés et modifier la manière dont les chaînes sont affichées à l’aide de CSS. Si vous avez besoin de cette fonctionnalité, votez pour elle sur [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traductions des fournisseurs d’identité de réseaux sociaux

Microsoft fournit le paramètre OIDC `ui_locales` pour les connexions aux réseaux sociaux. Mais certains fournisseurs d’identité de réseaux sociaux, notamment Facebook et Google, ne les respectent pas.

### <a name="browser-behavior"></a>Comportement du navigateur

Chrome et Firefox nécessitent la langue qui a été définie. S’il s’agit d’une langue prise en charge, elle s’affiche avant la langue par défaut. Actuellement, Microsoft Edge n’exige pas de langue particulière et affiche directement la langue par défaut.

## <a name="supported-languages"></a>Langues prises en charge

Azure AD inclut la prise en charge des langues suivantes. Les langues de flux utilisateur sont fournies par Azure AD. Les langues de notification d’authentification multifacteur (MFA) sont fournies par [Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

| Langage              | Code langue | Flux d’utilisateurs         | Notifications MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabe                | ar            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Bulgare             | bg            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Bangla                | bn            | ![Oui](./media/user-flow-customize-language/yes.png) | ![non](./media/user-flow-customize-language/no.png) |
| Catalan               | ca            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Tchèque                 | cs            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Danois                | da            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Allemand                | de            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Grec                 | el            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Anglais               | en            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Espagnol               | es            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Estonien              | et            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Basque                | eu            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Finnois               | fi            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Français                | fr            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Galicien              | gl            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Goudjrati              | gu            | ![Oui](./media/user-flow-customize-language/yes.png) | ![non](./media/user-flow-customize-language/no.png) |
| Hébreu                | he            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Hindi                 | hi            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Croate              | hr            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Hongrois             | hu            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Indonésien            | id            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Italien               | it            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Japonais              | ja            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Kazakh                | kk            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Kannada               | kn            | ![Oui](./media/user-flow-customize-language/yes.png) | ![non](./media/user-flow-customize-language/no.png) |
| Coréen                | ko            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Lituanien            | lt            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Letton               | lv            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Malayalam             | ml            | ![Oui](./media/user-flow-customize-language/yes.png) | ![non](./media/user-flow-customize-language/no.png) |
| Marathi               | mr            | ![Oui](./media/user-flow-customize-language/yes.png) | ![non](./media/user-flow-customize-language/no.png) |
| Malais                 | ms            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Norvégien Bokmal      | nb            | ![Oui](./media/user-flow-customize-language/yes.png) | ![non](./media/user-flow-customize-language/no.png) |
| Néerlandais                 | nl            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Norvégien             | non            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Pendjabi               | pa            | ![Oui](./media/user-flow-customize-language/yes.png) | ![non](./media/user-flow-customize-language/no.png) |
| Polonais                | pl            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Portugais - Brésil   | pt-br         | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Portugais - Portugal | pt-pt         | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Roumain              | ro            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Russe               | ru            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Slovaque                | sk            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Slovène             | sl            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Serbe - Cyrillique    | sr-cryl-cs    | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Serbe - Latin       | sr-latn-cs    | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Suédois               | sv            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Tamoul                 | ta            | ![Oui](./media/user-flow-customize-language/yes.png) | ![non](./media/user-flow-customize-language/no.png) |
| Télougou                | te            | ![Oui](./media/user-flow-customize-language/yes.png) | ![non](./media/user-flow-customize-language/no.png) |
| Thaï                  | th            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Turc               | tr            | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Ukrainien             | uk            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Vietnamien            | vi            | ![non](./media/user-flow-customize-language/no.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Chinois - simplifié  | zh-hans       | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |
| Chinois - traditionnel | zh-hant       | ![Oui](./media/user-flow-customize-language/yes.png) | ![Oui](./media/user-flow-customize-language/yes.png) |