---
title: Tutoriel pour configurer Azure Active Directory B2C avec le pare-feu d’applications web Cloudflare
titleSuffix: Azure AD B2C
description: Tutoriel pour configurer Azure Active Directory B2C avec le pare-feu d’applications web Cloudflare afin de protéger vos applications contre les attaques malveillantes
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/24/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 3eea91d1cbbb8fe34dcdc8668840503e6dc9d014
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109517349"
---
# <a name="tutorial-configure-cloudflare-with-azure-active-directory-b2c"></a>Tutoriel : Configurer Cloudflare avec Azure Active Directory B2C

Dans cet exemple de tutoriel, découvrez comment activer la solution de [pare-feu d’applications web (WAF) Cloudflare](https://www.cloudflare.com/waf/) pour le locataire B2C Azure Active Directory (AD) B2C avec un domaine personnalisé. Le pare-feu d’applications web Cloudflare aide les organisations à se protéger contre les attaques malveillantes visant à exploiter des vulnérabilités telles que SQLi et XSS.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](tutorial-create-tenant.md) lié à votre abonnement Azure.

- Un compte [Cloudflare](https://dash.cloudflare.com/sign-up).

## <a name="scenario-description"></a>Description du scénario

L’intégration de Cloudflare WAF inclut les composants suivants :

- **Locataire Azure AD B2C** : serveur d’autorisation chargé de vérifier les informations d’identification de l’utilisateur à l’aide des stratégies personnalisées définies dans le locataire.  Également connu sous le nom de fournisseur d’identité.

- [**Azure Front Door**](../frontdoor/front-door-overview.md) : permet d’activer des domaines personnalisés pour le locataire Azure B2C. Tout le trafic issu du pare-feu d'applications web Cloudflare transite via Azure Front Door avant d’accéder au locataire Azure AD B2C.

- **Cloudflare** : le pare-feu d’applications web qui gère tout le trafic envoyé au serveur d’autorisation.

## <a name="integrate-with-azure-ad-b2c"></a>Intégrer avec Azure AD B2C

Pour utiliser des domaines personnalisés dans Azure AD B2C, la fonctionnalité de domaine personnalisé fournie par Azure Front Door doit être utilisée. Découvrez comment [activer des domaines personnalisés Azure AD B2C](./custom-domain.md?pivots=b2c-user-flow).  

Une fois le domaine personnalisé pour Azure AD B2C correctement configuré à l’aide d’Azure Front Door, [testez le domaine personnalisé](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain) avant de poursuivre.  

## <a name="onboard-with-cloudflare"></a>Intégration avec Cloudflare

[Inscrivez-vous](https://dash.cloudflare.com/sign-up) et créez un compte Cloudflare. Pour activer le pare-feu d'applications webpare-feu d'applications web, une [référence SKU Pro](https://www.cloudflare.com/plans/) est, au minimum, requise.

### <a name="configure-dns"></a>Configurer DNS

1. Pour activer le pare-feu d'applications web pour un domaine, vous devez **activer le paramètre de proxy** à partir de la console DNS pour l’entrée CNAME, comme indiqué dans l’exemple relatif au domaine id.contosobank.co.uk.

   ![Illustration montrant comment sélectionner les paramètres de proxy](./media/partner-cloudflare/select-proxy-settings.png)

1. Activez ou désactivez l’option **État du proxy** disponible dans le volet DNS.

1. Après basculement vers **Par proxy**, il devient orange. Le paramètre final doit se présenter comme suit :

   ![Illustration montrant comment sélectionner par proxy](./media/partner-cloudflare/select-proxied.png)

### <a name="configure-waf"></a>Configurer WAF

[Configurez le pare-feu d'applications web](https://www.cloudflare.com/waf/) dans vos paramètres Cloudflare.

### <a name="configure-firewall-rule"></a>Configurer une règle de pare-feu

Ajoutez, mettez à jour ou supprimez les règles de pare-feu à l’aide de l’option de pare-feu disponible dans le volet supérieur de la console. Par exemple, le paramètre de pare-feu suivant active CAPTCHA pour toutes les requêtes entrantes dans le domaine *contosobank.co.uk* avant l’envoi de ces requêtes à Azure Front Door. Apprenez-en plus sur la [définition des règles du pare-feu](https://support.cloudflare.com/hc/articles/360016473712-Cloudflare-Firewall-Rules).

![Illustration montrant comment appliquer captcha](./media/partner-cloudflare/configure-firewall-rule.png)

### <a name="test-the-settings"></a>Tester les paramètres

1. Une fois la règle enregistrée, il est nécessaire d’exécuter CAPTCHA chaque fois qu’un accès au domaine personnalisé est demandé.

   ![Illustration montrant le pare-feu d’applications web Cloudflare appliquant CAPTCHA](./media/partner-cloudflare/enforce-captcha.png)

   > [!NOTE]
   > Cloudflare propose également d’[**autres options**](https://support.cloudflare.com/hc/en-us/articles/200172706-Configuring-Custom-Pages-Error-and-Challenge-) pour créer des pages de bloc personnalisées.  

2. L’utilisateur est dirigé vers la stratégie Azure AD B2C une fois CAPTCHA terminé.

   ![Illustration montrant la connexion à la stratégie Azure AD B2C](./media/partner-cloudflare/azure-ad-b2c-policy.png)


## <a name="additional-resources"></a>Ressources supplémentaires

- [Résoudre les problèmes de pages personnalisées Cloudflare](https://support.cloudflare.com/hc/en-us/articles/200172706-Configuring-Custom-Pages-Error-and-Challenge-#5QWV2KVjLnaAQ8L4tjiguw)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)

## <a name="next-steps"></a>Étapes suivantes 
 
- [Configurer un domaine personnalisé dans Azure AD B2C](./custom-domain.md?pivots=b2c-user-flow)
