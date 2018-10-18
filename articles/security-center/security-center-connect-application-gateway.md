---
title: Connexion de Microsoft Azure Application Gateway à Azure Security Center | Microsoft Docs
description: Découvrez comment intégrer Application Gateway et Azure Security Center pour améliorer la sécurité globale de vos ressources.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: 5638b71147592ae71c741ca86da68ddfec668af5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299064"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Connexion de Microsoft Azure Application Gateway à Azure Security Center
Ce document vous aide à configurer l’intégration avec les pare-feu d’applications web (WAF) Application Gateway et le Security Center.

## <a name="why-connect-application-gateway"></a>Pourquoi connecter Application Gateway?
Le pare-feu WAF d’Application Gateway protège les applications web des attaques basées sur le web courantes comme l’injection de code SQL, les attaques de script de site à site et les piratages de session. Le Security Center s’intègre à Application Gateway pour empêcher et détecter les menaces aux applications web non protégées dans votre environnement.

## <a name="how-do-i-configure-this-integration"></a>Comment configurer cette intégration ?
Le Security Center détecte les instances WAF précédemment déployées dans l’abonnement. Connectez ces solutions au Security Center pour permettre l’intégration d’alertes.

> [!NOTE]
> Le WAF d’Application Gateway peut également être configuré à partir des **Recommandations** du Security Center comme décrit dans [Ajouter un pare-feu d’applications web](security-center-add-web-application-firewall.md).
>
>

1. Connectez-vous au [portail Azure](https://azure.microsoft.com/features/azure-portal/).

2. Dans le **menu Microsoft Azure**, sélectionnez **Security Center**.

3. Sous **HYGIÈNE DE SÉCURITÉ DE LA RESSOURCE**, sélectionnez **Solutions de sécurité**.

  ![Vue d’ensemble de Security Center](./media/security-center-connect-application-gateway/overview.png)

4. Sous **Solutions découvertes**, recherchez le pare-feu d’applications Web basé sur SaaS Microsoft et sélectionnez **CONNEXION**.

  ![Solutions découvertes](./media/security-center-connect-application-gateway/connect.png)

5. **Connecter la solution WAF** s’ouvre.  Sélectionnez **Connexion** pour intégrer le WAF et le Security Center.

  ![Connecter la solution WAF](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à intégrer la solution WAF d’Application Gateway dans Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Intégrer des solutions de sécurité dans le Security Center](security-center-partner-integration.md)
* [Connexion de Microsoft Advanced Threat Analytics au Security Center](security-center-ata-integration.md)
* [Connecting Azure Active Directory Identity Protection to Security Center](security-center-aadip-integration.md) (Connexion d’Azure Active Directory Identity Protection au Security Center)
* [Surveillance de l’intégrité de la sécurité dans Security Center](security-center-monitoring.md).
* [Surveiller les solutions de partenaire avec Security Center](security-center-partner-solutions.md).
* [Questions fréquentes : Azure Security Center](security-center-faq.md).
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/).
