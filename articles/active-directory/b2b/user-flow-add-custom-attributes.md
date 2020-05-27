---
title: Ajouter des attributs personnalisés pour des flux utilisateur Azure AD
description: Découvrez la personnalisation des attributs de vos flux utilisateur d’inscription en libre-service.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f187cc47d9c64c8257cc097734fa41e10629f1c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594708"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>Définir des attributs personnalisés pour les flux utilisateurs (préversion)
|     |
| --- |
| La fonctionnalité des attributs utilisateur personnalisés est une fonctionnalité en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Pour chaque application, vous pouvez avoir des exigences différentes concernant les informations que vous souhaitez collecter lors de l’inscription. Azure AD est fourni avec un ensemble intégré d’informations stockées dans des attributs, comme le prénom, le nom, la localité et le code postal. Avec Azure AD, vous pouvez étendre l’ensemble des attributs stockés sur un compte invité lorsque l’utilisateur externe s’inscrit via un flux utilisateur.

Vous pouvez créer des attributs personnalisés dans le portail Azure et les utiliser dans vos flux utilisateur d’inscription en libre-service. Vous pouvez également lire et écrire ces attributs à l’aide de [l’API Microsoft Graph](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api). L’API Microsoft Graph prend en charge la création et la mise à jour d’un utilisateur avec des attributs d’extension. Dans l’API Graph, les attributs d’extension sont nommés à l’aide de la convention `extension_<Application-client-id>_attributename`. Par exemple :

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Vous pouvez trouver l’ID `<Application-client-id>` sur la page **Inscriptions d’applications** en regard de **ID d’application (client)** . Cet ID est spécifique de votre locataire.

## <a name="create-a-custom-attribute"></a>Création d’un attribut personnalisé

1. Connectez-vous au [Portail Azure](https://portal.azure.com) en tant qu’administrateur Azure AD.
2. Sous **Services Azure**, sélectionnez **Azure Active Directory**.
3. Dans le menu de gauche, sélectionnez **Identités externes**.
4. Sélectionnez **Attributs utilisateur personnalisés (préversion)** . Les attributs utilisateur disponibles sont répertoriés.

   ![Sélectionner les attributs utilisateur pour l’inscription](media/user-flow-add-custom-attributes/user-attributes.png)

5. Pour ajouter un attribut, sélectionnez **Ajouter**.
6. Dans le volet **Ajouter un attribut**, entrez les valeurs suivantes :

   - **Nom** : fournissez un nom pour l’attribut personnalisé (par exemple, « ShoeSize »).
   - **Type de données** : choisissez un type de données (**chaîne**, **booléen**ou **entier**).
   - **Description** : vous pouvez entrez une description de l’attribut personnalisé à usage interne. Cette description n’est pas visible par l’utilisateur.

   ![Ajouter un attribut](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Sélectionnez **Create** (Créer).

L’attribut personnalisé est maintenant disponible dans la liste des attributs utilisateur, et pour une utilisation dans vos flux utilisateur. Un attribut personnalisé est créé lors de sa première utilisation dans un flux utilisateur, non quand vous l’ajoutez à la liste des attributs utilisateur.

Une fois que vous avez créé un utilisateur à l’aide d’un flux utilisateur qui utilise l’attribut personnalisé nouvellement créé, l’objet peut être interrogé dans l’[Explorateur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer). Vous devez maintenant voir **ShoeSize** dans la liste d’attributs collectés lors de l’inscription, et le voir dans le jeton retourné à votre application. Pour plus d’informations sur la façon d’inclure ces revendications dans le jeton renvoyé à votre application, consultez [Configuration des revendications facultatives d’extension d’annuaire](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-directory-extension-optional-claims)

## <a name="next-steps"></a>Étapes suivantes

[Ajouter un flux utilisateur d’inscription en libre-service à une application](self-service-sign-up-user-flow.md)