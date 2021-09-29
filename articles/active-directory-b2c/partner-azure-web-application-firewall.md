---
title: Tutoriel pour configurer Azure Active Directory B2C avec Azure Web Application Firewall
titleSuffix: Azure AD B2C
description: Tutoriel pour configurer Azure Active Directory B2C avec le pare-feu d’applications web Azure afin de protéger vos applications contre les attaques malveillantes
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 54cb0cdbff2ac11334f168e41a18107d3d0dfe87
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763215"
---
# <a name="tutorial-configure-azure-web-application-firewall-with-azure-active-directory-b2c"></a>Tutoriel : Configurer Azure Web Application Firewall avec Azure Active Directory B2C

Dans cet exemple de tutoriel, découvrez comment activer la solution [Azure Web Application Firewall (WAF)](https://azure.microsoft.com/services/web-application-firewall/#overview) pour le locataire B2C Azure Active Directory (AD) B2C avec un domaine personnalisé. Azure WAF protège de manière centralisée vos applications web contre les vulnérabilités et attaques courantes.

>[!NOTE]
>Cette fonctionnalité est en version préliminaire publique.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure : si vous n’avez pas d’abonnement, vous pouvez obtenir un [compte gratuit](https://azure.microsoft.com/free/).

- [Un locataire Azure AD B2C](tutorial-create-tenant.md) : le serveur d’autorisation chargé de vérifier les informations d’identification de l’utilisateur à l’aide des stratégies personnalisées définies dans le locataire.  Également connu sous le nom de fournisseur d’identité.

- [Azure Front Door (AFD) :](../frontdoor/index.yml) permet d’activer des domaines personnalisés pour le locataire Azure AD B2C.  

- [Azure WAF :](https://azure.microsoft.com/services/web-application-firewall/#overview) gère tout le trafic envoyé au serveur d’autorisation.

## <a name="azure-ad-b2c-setup"></a>Configuration d’Azure AD B2C

Pour utiliser des domaines personnalisés dans Azure AD B2C, la fonctionnalité de domaine personnalisé fournie par AFD doit être utilisée. Découvrez comment [activer des domaines personnalisés Azure AD B2C](./custom-domain.md?pivots=b2c-user-flow).  

Une fois le domaine personnalisé pour Azure AD B2C correctement configuré à l’aide d’AFD, [testez le domaine personnalisé](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain) avant de poursuivre.  

## <a name="onboard-with-azure-waf"></a>Intégrer Azure WAF

Pour activer Azure WAF, configurez une stratégie WAF et associez cette stratégie au AFD pour la protection.

### <a name="create-a-waf-policy"></a>Créer une stratégie de pare-feu d’applications web (WAF)

Créez une stratégie WAF de base avec un ensemble de règles par défaut managé dans le [Portail Azure](https://portal.azure.com).

1. Accédez au [portail Azure](https://portal.azure.com). Sélectionnez **Créer une ressource**, puis recherchez Azure WAF. Sélectionnez **Azure Web Application Firewall (WAF)**  > **Créer**.

2. Accédez à la page **Créer une stratégie WAF**, sélectionnez l’onglet **De base**. Entrez les informations suivantes et acceptez les valeurs pour les paramètres restants.

| Valeur | Description |
|:--------|:-------|
| Stratégie pour | WAF global (Front Door)|
| Référence SKU Front Door | Choisir entre une référence SKU De base, Standard ou Premium |
|Abonnement | Sélectionner le nom de votre abonnement Front Door |
| Resource group | Sélectionner le nom de votre groupe de ressources Front Door |
| Nom de stratégie | Entrer un nom unique pour votre stratégie WAF |
| État de la stratégie | Définir sur Activé |
| Mode de stratégie | Définir comme détection |

3. Sélectionner **Vérifier + créer**

4. Accéder à l’onglet **Association** de la page Créer une stratégie WAF, sélectionner + **Associer un profil Front Door**, entrer les paramètres suivants

| Valeur | Description |
|:----|:------|
| Front Door | Sélectionner le nom de votre instance Front Door associée au domaine personnalisé Azure AD B2C |
| Domaines | Sélectionner les domaines personnalisés Azure AD B2C auxquels associer la stratégie WAF|

5. Sélectionnez **Ajouter**.

6. Sélectionnez **Vérifier + créer**, puis sélectionnez **Créer**.

### <a name="change-policy-mode-from-detection-to-prevention"></a>Modifier le mode de stratégie de la détection à la prévention

Quand une stratégie WAF est créée, par défaut la stratégie est en mode Détection. En mode Détection, la stratégie WAF ne bloque aucune requête, mais elle enregistre les requêtes correspondant aux règles WAF dans les journaux WAF. Pour plus d’informations sur la journalisation, consultez [Supervision et journalisation Azure WAF](../web-application-firewall/afds/waf-front-door-monitor.md).

L’exemple de requête affiche toutes les demandes qui ont été bloquées par la stratégie WAF au cours des dernières 24 heures. Les détails incluent le nom de la règle, les données de la requête, l’action effectuée par la stratégie et le mode de stratégie.

![L’image montre les requêtes bloquées](./media/partner-azure-web-application-firewall/blocked-requests-query.png)

![L’image montre le détail des requêtes bloquées](./media/partner-azure-web-application-firewall/blocked-requests-details.png)

Il est recommandé de laisser les requêtes de capture WAF en mode de détection. Examinez les journaux WAF pour déterminer s’il existe des règles dans la stratégie qui entraînent des résultats de faux positifs. Ensuite, [excluez les règles WAF basées sur les journaux WAF](../web-application-firewall/afds/waf-front-door-exclusion.md#define-exclusion-based-on-web-application-firewall-logs).

Pour voir WAF en action, utilisez Basculer vers le mode de prévention pour passer du mode de détection au mode de prévention. Toutes les requêtes qui correspondent aux règles définies dans l’ensemble de règles par défaut sont bloquées et enregistrées dans les journaux WAF.

![L’image montre le passage en mode de prévention](./media/partner-azure-web-application-firewall/switch-to-prevention-mode.png)

Au cas où vous souhaiteriez revenir au mode de détection, vous pouvez utiliser l’option Basculer vers le mode de détection.

![L’image montre le passage en mode de détection](./media/partner-azure-web-application-firewall/switch-to-detection-mode.png)

## <a name="next-steps"></a>Étapes suivantes

- [Surveillance et journalisation Azure WAF](../web-application-firewall/afds/waf-front-door-monitor.md)

- [WAF avec les listes d’exclusion du service Front Door](../web-application-firewall/afds/waf-front-door-exclusion.md)