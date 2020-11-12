---
title: Sécurisation des applications mobiles et web PaaS
titleSuffix: Azure App Service
description: 'Découvrez les bonnes pratiques de sécurité d’Azure App Service pour protéger vos applications mobiles et web PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: e583bb1b9cad79ee3e48e838d81d6a504c44f72c
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408088"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Bonnes pratiques pour la sécurisation des applications mobiles et web PaaS avec Azure App Service

Dans cet article, nous abordons un ensemble de bonnes pratiques de sécurité [Azure App Service](../../app-service/overview.md) pour protéger vos applications mobiles et web PaaS. Ces bonnes pratiques sont issues de notre expérience d’Azure, mais également de celle des clients, comme vous.

Azure App Service est une offre PaaS (Platform-as-a-Service) qui vous permet de créer des applications mobiles et web pour tout type d’appareil ou de plateforme, et de vous connecter à des données en tout lieu, dans le cloud ou localement. App Service inclut les fonctionnalités web et mobiles qui étaient précédemment fournies séparément dans Sites Web Azure et Azure Mobile Services. Il inclut également de nouvelles fonctionnalités d’automatisation des processus d’entreprise et d’hébergement d’API cloud. App Service est un service intégré unique qui apporte un ensemble complet de fonctionnalités pour les scénarios web, mobiles et d’intégration.

## <a name="authenticate-through-azure-active-directory-ad"></a>S’authentifier par le biais d’Azure Active Directory (AD)
App Service fournit un service OAuth 2.0 pour votre fournisseur d’identité. OAuth 2.0 privilégie la simplicité du développement client tout en fournissant des flux d’autorisation spécifiques pour les applications web, les applications de bureau et les téléphones mobiles. Azure AD utilise OAuth 2.0 pour vous permettre d’autoriser l’accès aux applications mobiles et web. Pour plus d’informations, consultez [Authentification et autorisation dans Azure App Service](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Restreindre l’accès en fonction d’un rôle
La restriction de l’accès est indispensable pour les organisations qui veulent appliquer des stratégies de sécurité portant sur l’accès aux données. Vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) pour affecter des autorisations aux utilisateurs, groupes et applications à une certaine étendue, comme les principes de sécurité des privilèges minimum et du besoin de connaître. Pour en savoir plus sur l’octroi aux utilisateurs du droit d’accès aux applications, consultez [Qu’est-ce que le contrôle d’accès en fonction du rôle (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Protéger vos clés
Si vous perdez vos clés d’abonnement, qu’importe la qualité de votre stratégie de sécurité ! Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Avec Key Vault, vous pouvez chiffrer les clés et les secrets (tels que les clés d’authentification, les clés de compte de stockage, les clés de chiffrement de données, les fichiers .PFX et les mots de passe) à l’aide de clés protégées par des modules de sécurité matériels (HSM). Pour une meilleure garantie, vous pouvez importer ou générer des clés HSM. Vous pouvez également utiliser Key Vault pour gérer vos certificats TLS avec renouvellement automatique. Pour en savoir plus, consultez [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/general/overview.md).

## <a name="restrict-incoming-source-ip-addresses"></a>Limiter les adresses IP source entrantes
Les [environnements App Service](../../app-service/environment/intro.md) proposent une fonctionnalité d’intégration de réseau virtuel qui vous permet de limiter les adresses IP sources entrantes par le biais de groupes de sécurité réseau (NSG). Si vous n’êtes pas familiarisé avec les réseaux Azure Virtual Network, cette fonctionnalité vous permet de placer la plupart de vos ressources Azure dans un réseau routable non-Internet dont vous contrôlez l’accès. Pour en savoir plus, consultez [Intégrer une application à un réseau Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md).

Pour App Service sur Windows, vous pouvez également limiter les adresses IP dynamiquement en configurant web.config. Pour plus d’informations, consultez [Sécurité IP dynamique](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté un ensemble de bonnes pratiques de sécurité App Service pour protéger vos applications mobiles et web PaaS. Pour en savoir plus sur la sécurisation de vos déploiements PaaS, consultez :

- [Sécurisation des déploiements PaaS](paas-deployments.md)
- [Sécurisation des bases de données PaaS dans Azure](paas-applications-using-sql.md)