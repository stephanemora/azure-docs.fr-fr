---
title: Personnalisation de la langue dans Azure Active Directory B2C | Microsoft Docs
description: Apprenez-en davantage sur la personnalisation de l’expérience linguistique.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e5bf7cc199fc8a439b5071faa89cce2e3ed47a75
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724313"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personnalisation de la langue dans Azure Active Directory B2C

La personnalisation de la langue dans Azure Active Directory B2C (Azure AD B2C) permet à votre flux utilisateur de prendre en charge plusieurs langues pour répondre aux besoins de votre client.  Microsoft fournit les traductions en [36 langues](#supported-languages), mais vous pouvez également fournir vos propres traductions pour n’importe quelle langue. Même si votre expérience est disponible dans une seule langue, vous pouvez personnaliser n’importe quel texte sur les pages.  

## <a name="how-language-customization-works"></a>Fonctionnement de la personnalisation de la langue
La personnalisation de la langue vous permet de sélectionner les langues dans lesquelles votre flux utilisateur est disponible. Une fois que la fonctionnalité est activée, vous pouvez fournir le paramètre de chaîne de requête, `ui_locales`, de votre application. Lorsque vous appelez dans Azure AD B2C, votre page est traduite dans la langue régionale que vous avez indiquée. Ce type de configuration vous permet de contrôler intégralement les langues de votre flux utilisateur et ignore les paramètres de langue du navigateur du client. 

Vous pouvez ne pas avoir besoin de ce niveau de contrôle sur les langues que votre client voit. Si vous n’indiquez pas de paramètre `ui_locales`, l’expérience du client est régie par les paramètres de son navigateur.  Vous pouvez toujours contrôler les langues dans lesquelles votre flux utilisateur est traduit en les ajoutant en tant que langues prises en charge. Si le navigateur d’un client est défini pour afficher une langue que vous ne souhaitez pas prendre en charge, c’est la langue sélectionnée par défaut dans les cultures prises en charge qui est affichée à la place.

- **Langue spécifiée par le paramètre ui-locales** : après avoir activé la personnalisation de la langue, votre flux utilisateur est traduit dans la langue indiquée ici.
- **Langue requise par le navigateur** : si aucun paramètre `ui_locales` n’a été spécifié, votre flux utilisateur est traduit dans la langue requise par le navigateur, *si la langue est prise en charge*.
- **Langue par défaut de la stratégie** : si le navigateur ne spécifie aucune langue ou s’il en spécifie une qui n’est pas prise en charge, le flux utilisateur est traduit dans la langue par défaut de ce dernier.

>[!NOTE]
>Si vous utilisez des attributs utilisateur personnalisés, vous devez fournir vos propres traductions. Pour plus d’informations, consultez [Personnaliser vos chaînes](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Prendre en charge les langues demandées par le paramètre ui_locales 
Les stratégies créées avant la disponibilité générale de la personnalisation de la langue doivent d’abord activer cette fonctionnalité. La personnalisation de la langue est activée par défaut dans les stratégies et flux d’utilisateurs créés après. 

En activant la personnalisation de la langue sur un flux utilisateur, vous pouvez contrôler la langue de ce flux utilisateur en ajoutant le paramètre `ui_locales`.
1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateurs**.
2. Cliquez sur le flux utilisateur pour lequel vous souhaitez activer la traduction.
3. Sélectionnez **Langues**.  
4. Sélectionnez **Activer la personnalisation de la langue**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Sélectionnez les langues activées dans votre flux utilisateur 
Activez un ensemble de langues dans lesquelles votre flux utilisateur peut être traduit lorsque le navigateur le demande sans paramètre `ui_locales`.
1. Vérifiez que la personnalisation de la langue est activée dans votre flux utilisateur, comme vu précédemment.
2. Dans la page **Langues** du flux utilisateur, sélectionnez une langue que vous souhaitez prendre en charge.
3. Dans le volet Propriétés, configurez **Activé** sur **Oui**.  
4. Sélectionnez **Enregistrer** dans la partie supérieure du volet.

>[!NOTE]
>Si aucun paramètre `ui_locales` n’est fourni, la page est traduite dans la langue du navigateur de l’utilisateur uniquement si elle est activée.
>

## <a name="customize-your-strings"></a>Personnaliser vos chaînes
La personnalisation de la langue vous permet de personnaliser les chaînes de votre flux utilisateur.
1. Vérifiez que la personnalisation de la langue est activée dans votre flux utilisateur, comme vu précédemment.
2. Dans la page **Langues** du flux utilisateur, sélectionnez une langue que vous souhaitez personnaliser.
3. Sous **Fichiers de ressource au niveau de la page**, sélectionnez la page que vous souhaitez modifier.
4. Sélectionnez **Télécharger les valeurs par défaut** (ou **Télécharger les valeurs de remplacements** si vous avez déjà modifié cette langue).

À la suite de ces opérations, vous obtenez un fichier JSON que vous pouvez utiliser pour commencer à modifier vos chaînes.

### <a name="change-any-string-on-the-page"></a>Modifier des chaînes sur la page
1. Ouvrez le fichier JSON téléchargé précédemment dans un éditeur JSON.
2. Recherchez l’élément que vous souhaitez modifier.  Vous pouvez trouver le `StringId` de la chaîne que vous recherchez, ou recherchez l’attribut `Value` que vous souhaitez modifier.
3. Mettez à jour l’attribut `Value` avec ce que vous souhaitez afficher.
4. Pour chaque chaîne que vous souhaitez modifier, modifiez `Override` en `true`.
5. Enregistrez le fichier et chargez vos modifications. (Vous pouvez trouver le contrôle de téléchargement au même emplacement que celui d’où vous avez téléchargé le fichier JSON). 

>[!IMPORTANT]
>Si vous devez remplacer une chaîne, veillez à définir la valeur `Override` sur `true`.  Si la valeur n’est pas modifiée, l’entrée est ignorée. 
>

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
Si vous souhaitez fournir une liste définie de valeurs pour les réponses, vous devez créer un attribut `LocalizedCollections`.  Un élément `LocalizedCollections` est un tableau de paires `Name` et `Value`. L’ordre des éléments sera l’ordre dans lequel ils apparaissent.  Pour ajouter `LocalizedCollections`, utilisez le format suivant :

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
1. Une fois que vous avez terminé de modifier votre fichier JSON, revenez à votre client B2C.
2. Sélectionnez **Flux d’utilisateurs** et cliquez sur celui pour lequel vous souhaitez activer la traduction.
3. Sélectionnez **Langues**.
4. Sélectionnez la langue dans laquelle vous voulez traduire.
5. Sélectionnez la page pour laquelle vous souhaitez fournir des traductions.
6. Sélectionnez l’icône de dossier et sélectionnez le fichier JSON à charger.
 
Ces changements sont automatiquement enregistrés dans votre flux utilisateur.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personnaliser l’interface utilisateur de la page à l’aide de la personnalisation de la langue

Il existe deux façons de traduire votre contenu HTML. Vous pouvez tout d’abord activer la [personnalisation de la langue](active-directory-b2c-reference-language-customization.md). L’activation de cette fonctionnalité permet à Azure AD B2C de transmettre le paramètre Open ID Connect, `ui-locales`, à votre point de terminaison.  Votre serveur de contenu peut utiliser ce paramètre pour fournir des pages HTML personnalisées qui sont spécifiques à la langue.

Vous pouvez également extraire le contenu de plusieurs emplacements selon les paramètres régionaux utilisés. Dans votre point de terminaison avec CORS activé, vous pouvez configurer une structure de dossiers pour héberger du contenu pour des langues spécifiques. Vous devez appeler celui qui convient si vous utilisez la valeur générique `{Culture:RFC5646}`.  Imaginons, par exemple, qu’il s’agit de l’URI de votre page personnalisée :

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Vous pouvez charger la page dans `fr`. La page extrait le contenu HTML et CSS à partir de :
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Ajouter des langues personnalisées

Vous pouvez également ajouter des langues pour lesquelles Microsoft ne fournit pas encore de traductions. Vous devrez fournir les traductions de toutes les chaînes du flux utilisateur.  Les codes de langues et de paramètres régionaux se limitent à ceux de la norme ISO 639-1. 

1. Dans votre locataire Azure AD B2C, sélectionnez **Flux d’utilisateurs**.
2. Sélectionnez le flux utilisateur dans lequel vous souhaitez ajouter des langues personnalisées, puis cliquez sur **Langues**.
3. Sélectionnez **Ajouter une langue personnalisée** en haut de la page.
4. Dans le volet contextuel qui s’ouvre, identifiez la langue pour laquelle vous fournissez des traductions en entrant un code de paramètres régionaux valide.
5. Pour chaque page, vous pouvez télécharger un ensemble de remplacements pour l’anglais et travailler sur les traductions.
6. Une fois que vous avez terminé avec les fichiers JSON, vous pouvez les télécharger pour chaque page.
7. Sélectionnez **Activer** et votre flux utilisateur peut désormais afficher cette langue pour votre utilisateur.
8. Enregistrez la langue.

>[!IMPORTANT]
>Vous devez activer les langues personnalisées ou charger des langues de substitution pour celles-ci avant de pouvoir enregistrer.
>

## <a name="additional-information"></a>Informations supplémentaires

### <a name="page-ui-customization-labels-as-overrides"></a>Étiquettes de personnalisation de l’interface utilisateur de la page comme remplacements
Lorsque vous activez la personnalisation de la langue, les modifications précédemment appliquées aux étiquettes via la personnalisation de l’interface utilisateur de la page sont conservées dans un fichier JSON pour l’anglais (en). Vous pouvez continuer à modifier vos étiquettes et les autres chaînes en chargeant des ressources de langue dans « Personnalisation de la langue ».
### <a name="up-to-date-translations"></a>Traductions à jour
Microsoft s’engage à fournir des traductions à jour pour que vous puissiez les utiliser. Nous ne cessons d’améliorer nos traductions pour qu’elles soient conformes à vos attentes. Microsoft identifiera les bogues et les modifications dans la terminologie globale et créera les mises à jour qui s’adapteront en toute transparence à votre flux utilisateur.
### <a name="support-for-right-to-left-languages"></a>Prise en charge des langues s’écrivant de droite à gauche
Microsoft n’assure actuellement pas la prise en charge des langues s’écrivant de droite à gauche. Pour cela, vous pouvez utiliser des paramètres régionaux personnalisés et modifier la manière dont les chaînes sont affichées à l’aide de CSS.  Si vous avez besoin de cette fonctionnalité, votez pour elle sur [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traductions des fournisseurs d’identité de réseaux sociaux
Microsoft fournit le paramètre OIDC `ui_locales` pour les connexions aux réseaux sociaux. Mais certains fournisseurs d’identité de réseaux sociaux, notamment Facebook et Google, ne les respectent pas. 
### <a name="browser-behavior"></a>Comportement du navigateur
Chrome et Firefox nécessitent la langue qui a été définie. S’il s’agit d’une langue prise en charge, elle s’affiche avant la langue par défaut. Actuellement, Edge n’exige pas de langue particulière et affiche directement la langue par défaut.

### <a name="supported-languages"></a>Langues prises en charge

| Langage              | Code de langue |
|-----------------------|---------------|
| Bangla                | bn            |
| Tchèque                 | cs            |
| Danois                | da            |
| Allemand                | de            |
| Grec                 | el            |
| Français               | en            |
| Espagnol               | es            |
| Finnois               | fi            |
| Français                | fr            |
| Goudjrati              | gu            |
| Hindi                 | hi            |
| Croate              | hr            |
| Hongrois             | hu            |
| Italien               | it            |
| Japonais              | ja            |
| Kannada               | kn            |
| Coréen                | ko            |
| Malayalam             | ml            |
| Marathi               | mr            |
| Malais                 | ms            |
| Norvégien Bokmal      | nb            |
| Néerlandais                 | nl            |
| Pendjabi               | pa            |
| Polonais                | pl            |
| Portugais - Brésil   | pt-br         |
| Portugais - Portugal | pt-pt         |
| Roumain              | ro            |
| Russe               | ru            |
| Slovaque                | sk            |
| Suédois               | sv            |
| Tamoul                 | ta            |
| Télougou                | te            |
| Thaï                  | th            |
| Turc               | tr            |
| Chinois - simplifié  | zh-hans       |
| Chinois - traditionnel | zh-hant       |
