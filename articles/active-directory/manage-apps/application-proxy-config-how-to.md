---
title: Comment configurer une application du proxy d’application | Microsoft Docs
description: Découvrez comment créer et configurer une application de Proxy d’APplication en quelques étapes simples
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc9287aac567c8989564094564b92b82662e603f
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825934"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Comment configurer une application du proxy d’application

Cet article vous aide à comprendre comment configurer une application de Proxy d’Application dans Azure AD pour exposer vos applications locales vers le cloud.

## <a name="recommended-documents"></a>Documents recommandés 

Pour en savoir plus sur les configurations initiales et la création d’une application de proxy d’application par le biais du portail d’administration, consultez [Publier des applications avec le Proxy d’application Azure AD](application-proxy-add-on-premises-application.md).

Pour plus d’informations sur la configuration des connecteurs, consultez [Activer le proxy d’application dans le portail Azure](application-proxy-add-on-premises-application.md).

Pour plus d’informations sur le chargement des certificats et l’utilisation des domaines personnalisés, consultez [Utilisation des domaines personnalisés dans le proxy d’application Azure AD](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Créer l’application/Définir les URL

Si vous suivez les étapes décrites dans la documentation [Publier des applications avec le Proxy d’application Azure AD](application-proxy-add-on-premises-application.md) et si vous obtenez une erreur lors de création de l’application, consultez les détails de l’erreur pour en savoir plus et découvrir des suggestions afin de corriger l’application. La plupart des messages d’erreur incluent la suggestion d’un correctif. Pour éviter les erreurs courantes, vérifiez les points suivants :

-   Vous êtes un administrateur autorisé à créer une application de proxy d’application

-   L’URL interne est unique

-   L’URL externe est unique

-   L’URL commence par http ou https et se terminer par un « / »

-   L’URL doit être un nom de domaine, et non une adresse IP

Le message d’erreur doit afficher dans l’angle supérieur droit lorsque vous créez l’application. Vous pouvez également sélectionner l’icône de notification pour afficher les messages d’erreur.

   ![Invite de notification](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Configurer des connecteurs/groupes de connecteurs

Si vous rencontrez des difficultés pour configurer votre application en raison d’un avertissement concernant les connecteurs et les groupes de connecteurs, consultez les instructions dédiées à l’activation du proxy d’application pour en savoir plus sur le téléchargement des connecteurs. Si vous souhaitez en savoir plus sur les connecteurs, consultez la [documentation consacrée aux connecteurs](application-proxy-connectors.md).

Si vos connecteurs sont inactifs, cela signifie qu’ils ne peuvent pas atteindre le service. Cela est souvent dû au fait que tous les ports requis ne sont pas ouverts. Pour afficher la liste des ports requis, consultez la section relative aux conditions préalables dans la documentation d’activation du proxy d’application.

## <a name="upload-certificates-for-custom-domains"></a>Charger des certificats pour des domaines personnalisés

Les domaines personnalisés vous permettent de spécifier le domaine de vos URL externes. Pour utiliser des domaines personnalisés, vous devez charger le certificat relatif à ce domaine. Pour plus d’informations sur l’utilisation des domaines et des certificats personnalisés, consultez [Utilisation des domaines personnalisés dans le proxy d’application Azure AD](application-proxy-configure-custom-domain.md). 

Si vous rencontrez des problèmes de chargement du certificat, recherchez les messages d’erreur dans le portail pour en savoir plus. Voici les problèmes courants liés aux certificats :

-   Expiration du certificat

-   Certificat auto-signé

-   Clé privée manquante pour le certificat

Le message d’erreur s’affiche dans l’angle supérieur droit lorsque vous tentez de télécharger le certificat. Vous pouvez également sélectionner l’icône de notification pour afficher les messages d’erreur.

   ![Invite de notification](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>Étapes suivantes
[Publier des applications avec le Proxy d’application Azure AD](application-proxy-add-on-premises-application.md)
