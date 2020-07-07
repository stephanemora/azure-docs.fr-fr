---
title: Créer et publier une documentation sur l’authentification unique pour votre application
description: Conseils pour les éditeurs de logiciels indépendants sur l’intégration avec Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c758e90548dd22b5abfb731f674f83dfbde9819
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955979"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Créer et publier une documentation sur l’authentification unique pour votre application   

## <a name="documentation-on-your-site"></a>Documentation sur votre site

La facilité d’adoption est un facteur important dans les décisions liées aux logiciels d’entreprise. Une documentation claire et intuitive soutient vos clients dans leur processus d’adoption et réduit les coûts de support. Pour avoir travaillé avec des milliers d’éditeurs de logiciels, Microsoft sait ce qui fonctionne.

Nous recommandons que la documentation sur votre site contienne au minimum les éléments suivants.

* Présentation de vos fonctionnalités d’authentification unique

  * Protocoles pris en charge

  * Version et référence SKU

  * Liste de fournisseurs d’identité pris en charge avec des liens vers leurs documentations respectives

* Informations de licence pour votre application

* Contrôle d’accès en fonction du rôle pour configurer l’authentification unique

* Étapes de configuration de l’authentification unique

  * Éléments de configuration d’interface utilisateur pour SAML avec les valeurs attendues pour le fournisseur

  * Informations du fournisseur de service à transmettre aux fournisseurs d’identité

* Si OIDC/OAuth

  * Liste d’autorisations nécessaires pour le consentement avec les justifications métier

* Étapes de tests pour les utilisateurs pilotes

* Informations de dépannage, notamment messages et codes d’erreur

* Mécanismes de support pour les clients

## <a name="documentation-on-the-microsoft-site"></a>Documentation sur le site Microsoft

Lorsque vous répertoriez votre application dans la galerie d’applications Azure Active Directory, qui publie également votre application sur la Place de marché Azure, Microsoft génère la documentation pour nos clients mutuels expliquant le processus pas à pas. Vous pouvez consulter un exemple [ici](https://aka.ms/appstutorial). Cette documentation est créée en fonction de ce que vous envoyez à la galerie, et vous pouvez facilement la mettre à jour si vous apportez des modifications à votre application à l’aide de votre compte GitHub.

## <a name="next-steps"></a>Étapes suivantes

[Afficher votre application dans la galerie d’applications Azure AD](https://docs.microsoft.com/Azure/active-directory/develop/howto-app-gallery-listing)
