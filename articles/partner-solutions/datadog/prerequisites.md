---
title: Conditions préalables pour Datadog sur Azure - solutions de partenaires Azure
description: Cet article explique comment configurer votre environnement Azure pour créer une instance de Datadog.
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 637d3cfb3baf8a53cb62943419077fad0f70d33d
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110656870"
---
# <a name="configure-environment-before-datadog-deployment"></a>Configurer l’environnement avant le déploiement Datadog

Cet article explique comment configurer votre environnement avant de déployer votre première instance de Datadog. Ces conditions sont requises pour la réalisation des démarrages rapides.

## <a name="access-control"></a>Contrôle d’accès

Pour configurer l’intégration de Datadog à Azure, vous devez disposer d’un accès **Propriétaire** sur l’abonnement Azure. [Vérifiez que vous disposez de l’accès approprié](../../role-based-access-control/check-access.md) avant de commencer la configuration.

## <a name="add-enterprise-application"></a>Ajouter une application d’entreprise
 
Pour utiliser la fonctionnalité d’authentification unique Security Assertion Markup Language (SAML) dans la ressource Datadog, vous devez configurer une application d’entreprise. Pour ajouter une application d’entreprise, vous avez besoin de l’un des rôles suivants : Administrateur général, Administrateur d’application cloud, Administrateur d’application ou Propriétaire du principal de service.

Suivez les étapes suivantes pour configurer l’application d’entreprise :

1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Azure Active Directory**.
1. Dans le volet gauche, sélectionnez **Applications d’entreprise**.
1. Sélectionnez **Nouvelle application**.
1. Dans **Ajouter à partir de la galerie**, recherchez *Datadog*. Sélectionnez le résultat de la recherche, puis sélectionnez **Ajouter**.

   :::image type="content" source="media/prerequisites/datadog-azure-ad-app-gallery.png" alt-text="Application Datadog dans la galerie d’entreprise AAD." border="true":::

1. Une fois l’application créée, accédez aux propriétés à partir du panneau latéral. Définissez **Affectation d’utilisateur requise ?** sur **Non**, puis sélectionnez **Enregistrer**.

   :::image type="content" source="media/prerequisites/user-assignment-required.png" alt-text="Définir les propriétés de l’application Datadog" border="true":::

1. Accédez à **Authentification unique** à partir du panneau latéral. Sélectionnez ensuite **SAML**.

   :::image type="content" source="media/prerequisites/saml-sso.png" alt-text="Authentification SAML." border="true":::

1. Sélectionnez **Oui** lorsque vous êtes invité à **enregistrer les paramètres d’authentification unique**.

   :::image type="content" source="media/prerequisites/save-sso.png" alt-text="Enregistrer l’authentification unique pour l’application Datadog" border="true":::

1. La configuration de l’authentification unique est maintenant terminée.

## <a name="next-steps"></a>Étapes suivantes

Pour créer une instance de Datadog, consultez [Démarrage rapide : Bien démarrer avec Datadog](create.md).
