---
title: Déploiement Azure AD B2C
description: Guide de déploiement Azure Active Directory B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 7/12/2021
ms.author: gasinh
author: gargi-sinha
manager: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9935fb243b6c2824633e67ac1eed26a80b67bc9d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068129"
---
# <a name="azure-active-directory-b2c-deployment-plans"></a>Plans de déploiement Azure Active Directory B2C

Azure Active Directory B2C est une solution évolutive de gestion des identités et des accès basée sur le cloud. Sa grande flexibilité pour répondre à vos attentes professionnelles et à une intégration harmonieuse avec l’infrastructure existante permet une meilleure digitalisation.

Pour aider les organisations à comprendre les exigences métier et à respecter les limites de la conformité, une approche étape par étape est recommandée tout au long d’un déploiement Azure Active Directory (Azure AD) B2C.

| Fonctionnalité | Description |
|:-----|:------|
| [Planification](#plan-an-azure-ad-b2c-deployment) | Préparez des projets Azure AD B2C pour le déploiement. Commencez par identifier les parties prenantes et définir ultérieurement une chronologie de projet. |
| [Implémenter](#implement-an-azure-ad-b2c-deployment) | Commencez par activer l’authentification et l’autorisation, puis effectuez une intégration complète de l’application. |
| [Surveiller](#monitor-an-azure-ad-b2c-solution) | Activez la journalisation, l’audit et les rapports une fois qu’une solution Azure AD B2C est en place. |

## <a name="plan-an-azure-ad-b2c-deployment"></a>Envisager un déploiement Azure AD B2C

Cette phase comporte les fonctionnalités suivantes :

| Fonctionnalité | Description |
|:------------|:------------|
|[Revue des exigences métier](#business-requirements-review) | Évaluer l’état et les attentes de votre organisation |
| [Parties prenantes](#stakeholders) |Constituer votre équipe de projet |
|[Communication](#communication) | Communiquez avec votre équipe sur le projet |
| [Durée](#timeline) | Rappel des jalons clés du projet  |

### <a name="business-requirements-review"></a>Revue des besoins métier

- Évaluez la raison principale pour désactiver les systèmes existants et [passez à Azure AD B2C](../../active-directory-b2c/overview.md).

- Pour une nouvelle application, [planifiez et concevez](../../active-directory-b2c/best-practices.md#planning-and-design) le système Customer Identity Access Management (CIAM)

- Identifiez l’emplacement du client et [créez un locataire dans le centre de données](../../active-directory-b2c/tutorial-create-tenant.md) correspondant.

- Vérifier le type des applications en votre possession
  - Vérifiez les plateformes actuellement prises en charge : - [MSAL](../develop/msal-overview.md) ou [Open source](https://azure.microsoft.com/free/open-source/search/?OCID=AID2200277_SEM_f63bcafc4d5f1d7378bfaa2085b249f9:G:s&ef_id=f63bcafc4d5f1d7378bfaa2085b249f9:G:s&msclkid=f63bcafc4d5f1d7378bfaa2085b249f9).
  - Pour les services principaux, utilisez le [flux des informations d’identification du client](../develop/msal-authentication-flows.md#client-credentials).

- Si vous envisagez de migrer à partir d’un fournisseur d’identité (IdP) existant

  - Envisagez d’utiliser l’[approche de migration transparente](../../active-directory-b2c/user-migration.md#seamless-migration)
  - Découvrez [comment migrer les applications existantes](https://github.com/azure-ad-b2c/user-migration)
  - Assurez la coexistence de plusieurs solutions à la fois.

- Déterminer les protocoles que vous souhaitez utiliser

  - Si vous utilisez actuellement Kerberos, NTLM et WS-FED, [migrez et refactorisez vos applications](https://www.bing.com/videos/search?q=application+migration+in+azure+ad+b2c&docid=608034225244808069&mid=E21B87D02347A8260128E21B87D02347A8260128&view=detail&FORM=VIRE). Une fois la migration effectuée, vos applications peuvent prendre en charge des protocoles d’identité modernes, tels que OAuth 2.0 et OpenID Connecter (OIDC) pour activer la protection et la sécurité des identités.

### <a name="stakeholders"></a>Parties prenantes

Les échecs de projets informatiques, lorsqu’ils se produisent, proviennent généralement d’une disparité entre les attentes et l’impact, les responsabilités et les résultats. Pour éviter un tel cas de figure, [veillez à faire appel aux bonnes personnes](./active-directory-deployment-plans.md#include-the-right-stakeholders) et à ce que les parties prenantes comprennent bien leur rôle.

- Identifiez l’architecte principal, le responsable de projet et le propriétaire de l’application.

- Envisagez de fournir une liste de distribution (DL). À l’aide de cette DL, vous pouvez communiquer des problèmes de produit à l’équipe des comptes ou l’équipe de développement Microsoft. Vous pouvez poser des questions et recevoir des notifications importantes.

- Identifiez un partenaire ou une ressource en dehors de votre organisation qui peut vous prendre en charge.

### <a name="communication"></a>Communication

La communication est essentielle à la réussite de tout nouveau service. Communiquez sur la modification de manière proactive avec vos utilisateurs. Communiquez de manière proactive avec vos utilisateurs sur les changements à venir, sur la date de leur implémentation de ceux-ci et sur la façon dont les utilisateurs peuvent obtenir de l’aide en cas de problème.

### <a name="timeline"></a>Durée

Définissez des attentes claires et des plans de suivi pour répondre aux jalons clés :

- Date du pilote attendue

- Date de lancement prévue

- Toutes les dates susceptibles d’affecter la date de livraison du projet

## <a name="implement-an-azure-ad-b2c-deployment"></a>Implémenter un déploiement Azure AD B2C

Cette phase comporte les fonctionnalités suivantes :

| Fonctionnalité | Description |
|:-------------|:--------------|
| [Déployer l’authentification et l’autorisation](#deploy-authentication-and-authorization) | Comprendre les scénarios [d’authentification et d’autorisation](../develop/authentication-vs-authorization.md) |
| [Déployer des applications et des identités d’utilisateur](#deploy-applications-and-user-identities) | Planifier le déploiement de l’application cliente et la migration des identités des utilisateurs  |
| [Intégration et livrables des applications clientes](#client-application-onboarding-and-deliverables) | Intégrer l’application cliente et tester la solution |
| [Sécurité](#security) | Améliorer la sécurité de votre solution d’identité |
|[Conformité](#compliance) | Répondre aux exigences réglementaires |
|[Expérience utilisateur](#user-experience) | Activer un service convivial |

### <a name="deploy-authentication-and-authorization"></a>Déployer l’autorisation et l’authentification

- Commencez par [configurer un locataire Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

- Pour une autorisation gérée par l’entreprise, utilisez les [exemples de parcours utilisateur Identity Experience Framework (IEF) Azure AD B2C](https://github.com/azure-ad-b2c/samples#local-account-policy-enhancements)

- Essayez [Open Policy Agent](https://www.openpolicyagent.org/).

En savoir plus sur Azure AD B2C dans [ce cours pour les développeurs](https://aka.ms/learnaadb2c).

Suivez cet exemple de liste de vérification pour plus d’informations :

- Identifiez les différentes personnes qui ont besoin d’accéder à votre application.  

- Définissez la façon dont vous gérez les autorisations et les droits dans votre système actuel aujourd’hui et comment planifier l’avenir.

- Vérifiez si vous disposez d’un magasin d’autorisations et si des autorisations doivent être ajoutées à l’annuaire.

- Si vous avez besoin d’une administration déléguée, définissez comment la résoudre. Par exemple, la gestion des clients de vos clients.

- Vérifiez si votre application appelle directement un gestionnaire d’API (APIM). Il peut être nécessaire d’appeler à partir de l’IdP avant d’émettre un jeton pour l’application.

### <a name="deploy-applications-and-user-identities"></a>Déployer des applications et des identités d’utilisateur

Tous les projets Azure AD B2C démarrent avec une ou plusieurs applications clientes, qui peuvent avoir des objectifs métier différents.

1. [Créez ou configurez des applications clientes](../../active-directory-b2c/app-registrations-training-guide.md). Reportez-vous à ces [exemples de code](../../active-directory-b2c/integrate-with-app-code-samples.md) pour l’implémentation.

2. Ensuite, configurez votre parcours utilisateur basé sur des flux d’utilisateurs intégrés ou personnalisés. [Apprenez quand utiliser des flux d’utilisateurs et des stratégies personnalisées](../../active-directory-b2c/user-flow-overview.md#comparing-user-flows-and-custom-policies).

3. Configurez les fournisseurs d’identité en fonction des besoins de votre entreprise. [Découvrez comment ajouter Azure Active Directory B2C en tant que IdP](../../active-directory-b2c/add-identity-provider.md).

4. Migrez vos utilisateurs. [En savoir plus sur les approches de migration utilisateur](../../active-directory-b2c/user-migration.md). Pour consulter des scénarios avancés, reportez-vous à [Exemples de parcours utilisateur IEF Azure AD B2C](https://github.com/azure-ad-b2c/samples).  

Considérez cet exemple de liste de vérification lors du **déploiement de vos applications** :

- Vérifiez le nombre d’applications qui sont dans l’étendue pour le déploiement CIAM.

- Vérifiez le type des applications en cours d’utilisation. Par exemple, les applications web traditionnelles, les API, les applications monopage (SPA) ou les applications mobiles natives.

- Vérifiez le type d’authentification en place. Par exemple, les formulaires basés sur SAML ou fédérés avec OIDC.
  - Si OIDC est utilisé, vérifiez le type de réponse : - code ou id_token.

- Vérifiez si toutes les applications frontend et backend sont hébergées dans localement, dans le cloud ou dans un cloud hybride.

- vérifiez les plateformes/langages utilisés, par exemple, [ASP.NET](../../active-directory-b2c/quickstart-web-app-dotnet.md), Java et Node.js.

- Vérifiez l’emplacement de stockage des attributs de l’utilisateur actuel. Il peut s’agir du protocole LDAP (Lightweight Directory Access Protocol) ou de bases de données.

Considérez cet exemple de liste de vérification lorsque vous **déployez des identités d’utilisateur** :

- Vérifiez le nombre d’utilisateurs qui accèdent aux applications.

- Vérifiez le type des fournisseurs d’identité nécessaires. Par exemple, Facebook, compte local et [Active Directory Federation Services (AD FS)](/windows-server/identity/active-directory-federation-services).

- Structurez le schéma de revendication requis à partir de votre application, [Azure AD B2C](../../active-directory-b2c/claimsschema.md) et vos fournisseurs d’identité, le cas échéant.

- Présentez les informations requises pour effectuer la capture pendant un [flux de connexion/inscription](../../active-directory-b2c/add-sign-up-and-sign-in-policy.md?pivots=b2c-user-flow).

### <a name="client-application-onboarding-and-deliverables"></a>Intégration et livrables des applications clientes

Considérez cet exemple de liste de vérification lors de l’**intégration d’une application** :

|  Tâche | Description |
|:--------------------|:----------------------|
| Définir le groupe cible de l’application | Vérifiez si cette application est une application cliente, une application cliente professionnelle ou un service numérique. Vérifiez si la connexion des employés est nécessaire. |
| Identifier la valeur métier tangible d’une application | Comprenez l’ensemble des cas d’entreprise d’une application pour trouver la solution la mieux adaptée à la solution Azure AD B2C et comprenez l’intégration avec d’autres applications clientes.|
| Vérifiez les groupes d’identité en votre possession | Regroupez les identités dans différents types de groupes avec différents types d’exigences, comme **Business to Customer** (B2C) pour les clients finaux et les clients professionnels, **Business to Business** (B2B) pour les partenaires et les fournisseurs, **Business to Employee** (B2E) pour vos employés et les employés externes, **Business to Machine** (B2M) pour les connexions aux appareils IoT et les comptes de service.|
| Vérifiez le fournisseur d’identité dont vous avez besoin pour les processus et les besoins de votre entreprise | Azure AD B2C [prend en charge plusieurs types de fournisseurs d’identité](../../active-directory-b2c/add-identity-provider.md#select-an-identity-provider) et, en fonction du cas d’usage, l’IdP approprié doit être choisi. Par exemple, pour une application mobile C2C, une connexion utilisateur rapide et aisée est requise. Dans un autre cas d’utilisation, pour une application B2C avec des services numériques, des exigences de conformité supplémentaires sont nécessaires. L’utilisateur peut avoir besoin de se connecter avec son identité d’entreprise, telle que la connexion à la messagerie électronique. |
| Vérifier les contraintes réglementaires | Vérifiez s’il existe une raison d’avoir des profils distants ou des stratégies de confidentialité spécifiques.  |
| Concevoir le processus de connexion et d’inscription | Déterminez si vous avez besoin d’une vérification par e-mail à l’intérieur ou à l’extérieur des inscriptions. Tout d’abord, le processus d’extraction, comme les systèmes de commerce électronique ou l’[authentification multifacteur (MFA) Azure AD](../authentication/concept-mfa-howitworks.md), est nécessaire ou non. Regardez [cette vidéo](https://www.youtube.com/watch?v=c8rN1ZaR7wk&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=4). |
| Vérifiez le type d’application et le protocole d’authentification utilisés ou qui seront implémentés | Échange d’informations sur l’implémentation d’une application cliente telle qu’une application web, d’une application monopage ou d’une application native. Les protocoles d’authentification peuvent être OAuth, OIDC et SAML pour l’application cliente et Azure AD B2C. Regarder [cette vidéo](https://www.youtube.com/watch?v=r2TIVBCm7v4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=9)|
| Planifier la migration des utilisateurs | Discutez des possibilités de [migration des utilisateurs avec Azure AD B2C](../../active-directory-b2c/user-migration.md). Il existe plusieurs scénarios possibles, tels que la migration juste-à-temps (JIT) et l’importation/exportation en bloc. Regardez [cette vidéo](https://www.youtube.com/watch?v=lCWR6PGUgz0&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=2). Vous pouvez envisager d’utiliser l’[API Microsoft Graph](https://www.youtube.com/watch?v=9BRXBtkBzL4&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=3) pour la migration des utilisateurs.|

Considérez cet exemple de liste de vérification lors de la **livraison**.

| Fonctionnalité | Description |
|:-----|:-------|
|Informations sur le protocole| Recueillez le chemin d’accès de base, les stratégies et l’URL des métadonnées des deux variantes. Selon l’application cliente, spécifiez les attributs tels que l’exemple de connexion, l’ID d’application cliente, les secrets et les redirections.|
| Exemples d’applications | Reportez-vous aux [exemples de code](../../active-directory-b2c/integrate-with-app-code-samples.md) fournis. |
|Test de pénétration | Avant les tests, informez votre équipe d’exploitation sur les tests du stylet, puis testez tous les flux utilisateur, notamment l’implémentation OAuth. Apprenez-en davantage sur les [test de pénétration](../../security/fundamentals/pen-testing.md) et les [règles d’engagement pour les tests de pénétration du cloud Microsoft](https://www.microsoft.com/msrc/pentest-rules-of-engagement).
| Effectuer des tests unitaires  | Effectuer des tests unitaires et générer des jetons [à l’aide de flux d’informations d’identification de mot de passe de propriétaire de ressource (ROPC)](../develop/v2-oauth-ropc.md). Si vous atteignez la limite de jetons Azure AD B2C, [contactez l’équipe du support technique](../../active-directory-b2c/support-options.md). Réutilisez les jetons pour réduire les efforts d’investigation sur votre infrastructure. [Configurez un flux ROPC](../../active-directory-b2c/add-ropc-policy.md?pivots=b2c-user-flow&tabs=app-reg-ga).|
| Test de charge | Attendez-vous à atteindre les [limites de service](../../active-directory-b2c/service-limits.md) Azure AD B2C. Évaluez le nombre d’authentifications par mois attendues pour votre service. Évaluez le nombre de connexions utilisateur moyennes attendues par mois. Évaluez les durées de trafic de charge élevée attendues et les raisons métier de ces charges, telles que les jours fériés, les migrations et les événements. Évaluez le taux d’inscription maximal attendu, par exemple, le nombre de requêtes par seconde. Évaluez le taux de trafic maximal attendu avec l’authentification multifacteur, par exemple, le nombre de requêtes par seconde. Évaluez la répartition géographique du trafic attendu et leurs taux de pointe.

### <a name="security"></a>Sécurité

Examinez cet exemple de liste de contrôle pour renforcer la sécurité de votre application en fonction des besoins de votre entreprise :

- Vérifiez si une méthode d’authentification forte telle que l’[authentification multifacteur](../authentication/concept-mfa-howitworks.md) (MFA) est requise. Pour les utilisateurs qui déclenchent des transactions à valeur élevée ou d’autres événements à risque, il leur est suggéré d’utiliser l’authentification multifacteur. Par exemple, pour les applications bancaires et financières, les magasins en ligne - tout d’abord, le processus de validation.

- Vérifiez si l’authentification multifacteur est requise, [vérifiez les méthodes disponibles pour l’authentification multifacteur](../authentication/concept-authentication-methods.md), par exemple SMS/Téléphone, courrier électronique et services tiers.

- Vérifiez si un mécanisme anti-bot est actuellement utilisé avec vos applications.

- Évaluez le risque de tentatives de création de comptes et de journaux frauduleux. Utilisez l’[évaluation de la protection contre les fraudes de Microsoft Dynamics 365](../../active-directory-b2c/partner-dynamics-365-fraud-protection.md) pour bloquer ou défier les tentatives suspectes de créer de nouveaux comptes factices ou de compromettre des comptes existants.  

- Vérifiez les éventuelles postures conditionnelles qui doivent être appliquées dans le cadre de la connexion ou de l’inscription pour les comptes avec votre application.

>[!NOTE]
>Vous pouvez utiliser les [règles d’accès conditionnel](../conditional-access/overview.md) pour ajuster la différence entre l’expérience utilisateur et la sécurité en fonction des objectifs de votre entreprise.

Pour plus d’informations, consultez [Identity Protection et accès conditionnel dans Azure AD B2C](../../active-directory-b2c/conditional-access-identity-protection-overview.md).

### <a name="compliance"></a>Conformité

Pour répondre à certaines exigences réglementaires, vous pouvez envisager d’utiliser réseaux virtuels, des restrictions IP, le pare-feu d’applications web (WAF) et des services similaires afin de améliorer la sécurité de vos systèmes principaux.

Pour répondre aux exigences de conformité de base, tenez compte des éléments suivants :

- Exigences de conformité réglementaires spécifiques que vous devez prendre en charge ; par exemple, PCI-DSS.

- Vérifiez s’il est nécessaire de stocker les données dans un magasin de base de données distinct. Si c’est le cas, vérifiez si ces informations ne doivent jamais être écrites dans le répertoire.

### <a name="user-experience"></a>Expérience utilisateur

Examinez l’exemple de liste de vérification pour définir les exigences de l’expérience utilisateur (UX) :

- Identifiez les intégrations requises pour [étendre les fonctionnalités CIAM et créer des expériences d’utilisateur final transparentes](../../active-directory-b2c/partner-gallery.md).

- Fournissez des captures d’écran et des récits utilisateur pour montrer l’expérience de l’utilisateur final pour l’application existante. Par exemple, fournissez des captures d’écran pour la connexion, l’inscription, la connexion d’inscription combinée (SUSI), la modification de profil et la réinitialisation de mot de passe.

- Recherchez les indications existantes transmises à l’aide des paramètres queryString dans votre solution des identités clients actuelle.

- Si vous vous attendez à une personnalisation poussée de l’UX, par exemple pixel par pixel, vous aurez peut-être besoin d’un développeur front-end pour vous aider.

## <a name="monitor-an-azure-ad-b2c-solution"></a>Surveiller une solution Azure AD B2C

Cette phase comporte les fonctionnalités suivantes :

| Fonctionnalité | Description |
|:---------|:----------|
|  Surveillance  |[Supervisez Azure AD B2C avec Azure Monitor](../../active-directory-b2c/azure-monitor.md). Regarder [cette vidéo](https://www.youtube.com/watch?v=Mu9GQy-CbXI&list=PL3ZTgFEc7LyuJ8YRSGXBUVItCPnQz3YX0&index=1)|
| Audit et journalisation | [Accéder aux journaux d’audit et les consulter](../../active-directory-b2c/view-audit-logs.md)

## <a name="more-information"></a>Autres informations

Pour accélérer les déploiements Azure AD B2C et surveiller le service à grande échelle, consultez les articles suivants :

- [Gérer Azure AD B2C avec Microsoft Graph](../../active-directory-b2c/microsoft-graph-get-started.md)

- [Gérer les comptes d’utilisateur Azure AD B2C avec Microsoft Graph](../../active-directory-b2c/microsoft-graph-operations.md)

- [Déployer des stratégies personnalisées avec Azure Pipelines](../../active-directory-b2c/deploy-custom-policies-devops.md)

- [Gérer les stratégies personnalisées Azure AD B2C avec Azure PowerShell](../../active-directory-b2c/manage-custom-policies-powershell.md)

- [Superviser Azure AD B2C avec Azure Monitor](../../active-directory-b2c/azure-monitor.md)

## <a name="next-steps"></a>Étapes suivantes

- [Bonnes pratiques pour Azure AD B2C](../../active-directory-b2c/best-practices.md)

- [Limites de service Azure AD B2C](../../active-directory-b2c/service-limits.md)
