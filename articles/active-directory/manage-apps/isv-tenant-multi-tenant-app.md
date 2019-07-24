---
title: Créer un locataire Azure pour une application multilocataire
description: Conseils pour les éditeurs de logiciels indépendants sur l’intégration avec Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659447"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Créer un locataire Azure pour une application multilocataire  

Pour fournir l’accès à votre application mutualisée, vous devez créer un locataire Azure Active Directory pour inscrire l’application et activer la fédération des identités de votre client. Consultez [Choix du protocole de fédération approprié pour votre application mutualisée](isv-choose-multi-tenant-federation.md). Ce locataire vous permettra de tester votre application et la fédération dans un environnement qui est similaire aux environnements Azure AD de vos clients.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Coûts d’hébergement d'une application mutualisée

Azure Active Directory est disponible dans trois références (SKU), Gratuit, De base et Premium. [Consultez la comparaison détaillée des fonctionnalités](https://azure.microsoft.com/pricing/details/active-directory/).

Vous pouvez créer votre abonnement Azure et Azure Active Directory gratuitement et utiliser les fonctionnalités de base.

## <a name="create-your-tenant"></a>Créer votre locataire

1. Créez votre locataire. Consultez [Configurer un environnement de développement](../develop/quickstart-create-new-tenant.md).

2. Activez et testez l’accès par authentification unique à votre application,

   a. **Pour les applications OIDC ou Oath**, [inscrivez votre application](../develop/quickstart-register-app.md) comme une application mutualisée. Sous Types de comptes pris en charge, sélectionnez l’option Comptes dans un annuaire organisationnel et comptes personnels Microsoft

   b. **Pour les applications basées sur SAML et WS-Fed**, vous [Configurez des applications avec authentification unique basée sur SAML](configure-single-sign-on-non-gallery-applications.md) à l’aide d’un modèle SAML générique dans Azure AD.

Vous pouvez également [convertir une application à locataire unique en application mutualisée](../develop/howto-convert-app-to-be-multi-tenant.md) si nécessaire.

## <a name="next-steps"></a>Étapes suivantes

[Intégrer l’authentification unique dans votre application](isv-sso-content.md)
