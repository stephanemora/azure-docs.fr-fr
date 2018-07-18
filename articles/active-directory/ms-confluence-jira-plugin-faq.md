---
title: FAQ du plug-in Azure Active Directory SSO | Microsoft Docs
description: Obtenez des réponses aux questions fréquemment posées sur la configuration de l’authentification unique entre Azure Active Directory et Jira/Confluence.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: jeedes
ms.openlocfilehash: eb7576c48d3836eec8051d849cefe4c5ec6658c9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699204"
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>FAQ du plug-in Azure Active Directory SSO

Reportez-vous aux FAQ ci-dessous si vous avez une requête concernant ce plug-in.

## <a name="what-does-the-plug-in-do"></a>Que fait le plug-in ?

Le plug-in fournit une fonctionnalité d’authentification unique (SSO) pour les logiciels locaux Jira (notamment Jira Core, Jira Software et Jira Service Desk) et Confluence d’Atlassian. Le plug-in fonctionne avec Azure Active Directory (Azure AD) comme fournisseur d’identité (IdP).

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Avec quels produits Atlassian le plug-in fonctionne-t-il ?

Le plug-in fonctionne avec les versions locales de Jira et Confluence.

## <a name="does-the-plug-in-work-on-cloud-versions"></a>Le plug-in fonctionne-t-il sur les versions cloud ?

Non. Le plug-in prend uniquement en charge les versions locales de Jira et Confluence.

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Quelles versions de Jira et Confluence le plug-in prend-il en charge ?

Le plug-in prend en charge les versions suivantes :

* Jira Core et Software : 6.0 à 7.8
* Jira Service Desk : 3.0 à 3.2
* Confluence : 5.0 à 5.10

## <a name="is-the-plug-in-free-or-paid"></a>Le plug-in est-il gratuit ou payant ?

Il s’agit d’un module complémentaire gratuit.

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Ai-je besoin de redémarrer Jira ou Confluence après avoir déployé le plug-in ?

Un redémarrage n’est pas nécessaire. Vous pouvez commencer à utiliser le plug-in immédiatement.

## <a name="how-do-i-get-support-for-the-plug-in"></a>Comment obtenir du support pour le plug-in ?

Vous pouvez contacter [l’équipe d’intégration Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) pour tout support nécessaire pour ce plug-in. Elle répond dans un délai de 24 à 48 heures.

Vous pouvez également créer un ticket de support auprès de Microsoft par le biais du portail Azure.

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Le plug-in fonctionne-t-il avec une installation de Jira et Confluence sur Mac ou Ubuntu ?

Nous avons testé le plug-in uniquement sur des installations de Jira et Confluence sur des serveurs Windows 64 bits.

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Le plug-in fonctionne-t-il avec des IdP autres qu’Azure AD ?

Non. Il fonctionne uniquement avec Azure AD.

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Avec quelle version de SAML le plug-in fonctionne-t-il ?

Il fonctionne avec SAML 2.0.

## <a name="does-the-plug-in-do-user-provisioning"></a>Le plug-in procède-t-il à l’approvisionnement des utilisateurs ?

Non. Le plug-in fournit uniquement l’authentification unique basée sur SAML 2.0. L’utilisateur doit être provisionné dans l’application avant la connexion avec authentification unique.

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Le plug-in prend-il en charge les versions cluster de Jira et Confluence ?

Non. Le plug-in fonctionne avec les versions locales de Jira et Confluence.

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Le plug-in fonctionne-t-il avec les versions HTTP de Jira et Confluence ?

Non. Le plug-in fonctionne uniquement avec les installations HTTPS.