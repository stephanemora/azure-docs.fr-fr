---
title: Configurer un environnement de test pour votre application
titleSuffix: Microsoft identity platform
description: Découvrez comment configurer un environnement de test Azure Active Directory afin de pouvoir tester votre application intégrée à la plateforme d’identités Microsoft.  Déterminez si vous avez besoin d’un locataire distinct à des fins de test ou si vous pouvez utiliser votre locataire de production.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2021
ms.author: arcrowe
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: e695a7028432b1044c0c1896447d474b2716097f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368334"
---
# <a name="set-up-your-applications-azure-ad-test-environment"></a>Configurer l’environnement de test Azure AD de votre application

En tant que développeur, vous utilisez le cycle de vie de développement logiciel et déplacez votre application entre les environnements de développement, de test et de production.  Lorsque ce processus est utilisé pour des applications protégées par la plateforme d’identités Microsoft, vous devez configurer un environnement Azure Active Directory (Azure AD) qui sera utilisé pour les tests.  Cet environnement peut être utilisé dans les premières phases de test du cycle de vie du développement, ainsi que comme environnement de test permanent à long terme.  Selon vos besoins en matière d’isolation des ressources, vous pouvez utiliser le locataire de production Azure AD de votre organisation ou un locataire entièrement distinct pour les tests.  

Dans cet article, vous apprendrez à configurer un environnement de test Azure AD afin de pouvoir tester votre application intégrée à la plateforme d’identités Microsoft.  Évaluez le niveau d’isolation nécessaire et déterminez si vous avez besoin d’un locataire distinct pour les tests ou si vous pouvez utiliser votre locataire de production.

## <a name="decide-the-level-of-isolation-needed"></a>Décider du niveau d’isolation nécessaire
En général, il est plus facile et moins coûteux d’utiliser votre locataire de production en tant qu’environnement de test. Toutefois, cette option n’est viable que si vous pouvez atteindre le niveau d’isolation adéquat entre les ressources de test et celles de production.  L’isolation est particulièrement importante pour les scénarios à privilèges élevés.

Configurez votre environnement de test dans un locataire distinct (et non dans le locataire de production de votre organisation) dans les cas suivants :
- Vous disposez d’un ensemble de ressources qui nécessite des paramètres uniques à l’échelle du locataire. Par exemple, votre application peut avoir besoin d’accéder aux ressources du locataire en tant que telle et non au nom d’un utilisateur (utilisation d’autorisations réservées aux applications).  L’accès réservé aux applications nécessite le consentement administrateur qui s’applique à l’ensemble du locataire, et ces autorisations sont difficiles à restreindre de manière sûre aux limites d’un locataire.
- Votre tolérance au risque d’accès non autorisé des membres du locataire à vos ressources de test est minimale.
- Les changements de configuration pourraient avoir un impact critique sur votre environnement de production.
- Vous n’êtes pas en mesure de créer des utilisateurs de test ni des données de test associées dans votre locataire.
- Vous prévoyez d’effectuer des connexions automatiques à votre application à des fins de test, et les stratégies de méthode d’authentification configurées dans votre locataire de production nécessitent une certaine interaction avec l’utilisateur.  Par exemple, si l’authentification multifacteur est requise pour tous les utilisateurs, vous ne pourrez pas effectuer de connexions automatiques pour les tests d’intégration.
- Vous devez vous assurer que les administrateurs généraux ne peuvent pas gérer les ressources de test spécifiques ni y accéder. Vous devrez isoler cette ressource dans un locataire distinct avec des administrateurs généraux distincts.
- L’ajout de ressources et/ou de charges de travail non liées à la production à votre locataire de production [dépasserait la limitation ou les limites de service](test-throttle-service-limits.md) pour ce locataire.

Si aucune de ces conditions ne s’applique à vous, suivez ces étapes pour [configurer votre environnement de test dans votre locataire de production](#set-up-a-test-environment-in-your-production-tenant).  Toutefois, si l’une d’entre elles s’applique, vous devez [configurer un environnement de test dans un locataire distinct](#set-up-a-test-environment-in-a-separate-tenant).

## <a name="set-up-a-test-environment-in-a-separate-tenant"></a>Configurer un environnement de test dans un locataire distinct
Si vous ne pouvez pas restreindre en toute sécurité votre application de test à votre locataire de production, vous pouvez créer un locataire distinct à des fins de développement et de test. 

### <a name="get-a-test-tenant"></a>Obtenir un locataire de test
Si vous ne disposez pas déjà d’un locataire de test dédié, vous pouvez en créer un gratuitement à l’aide du programme pour développeur Microsoft 365 ou en créer un manuellement. 

#### <a name="join-the-microsoft-365-developer-program-recommended"></a>Rejoindre le programme pour développeur Microsoft 365 (recommandé) 
Le [programme pour développeur Microsoft 365](https://developer.microsoft.com/microsoft-365/dev-program) est gratuit et permet d’ajouter automatiquement au locataire des comptes d’utilisateur de test et des exemples de packs de données.
1. Cliquez sur le bouton **Rejoindre maintenant** à l’écran.
2. Connectez-vous avec un nouveau compte Microsoft ou utilisez un compte (professionnel) existant.
3. Sur la page d’inscription, sélectionnez votre région, saisissez le nom de votre entreprise et acceptez les conditions générales du programme avant de cliquer sur **Suivant**.
4. Cliquez sur **Configurer l’abonnement**. Spécifiez la région dans laquelle vous souhaitez créer votre locataire, créez un nom d’utilisateur, un domaine, puis entrez un mot de passe. Cela créera un nouveau locataire et le premier administrateur du locataire.
5. Entrez les informations de sécurité nécessaires pour protéger le compte administrateur de votre nouveau locataire. Ceci configurera l’authentification multifacteur pour le compte.

#### <a name="manually-create-a-tenant"></a>Créer un locataire manuellement  
Vous pouvez [créer un locataire manuellement](quickstart-create-new-tenant.md), lequel sera vide à la création et devra être configuré avec des données de test.

### <a name="populate-your-tenant-with-users"></a>Remplir votre locataire avec des utilisateurs  
Pour des raisons pratiques, vous pouvez vous inviter, ainsi que d’autres membres de votre équipe de développement, à devenir des utilisateurs invités dans le locataire.  Cela créera des objets invités distincts dans le locataire de test, mais signifie que vous n’aurez à gérer qu’un seul ensemble d’informations d’identification pour votre compte d’entreprise et votre compte de test.
1. À partir du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
2. Accédez à **Users**.
3. Cliquez sur **Nouvel utilisateur invité** et invitez l’adresse e-mail de votre compte professionnel.
4. Répétez l’opération pour les autres membres de l’équipe de développement ou de test de votre application.

Vous pouvez également créer des utilisateurs de test dans votre locataire de test.  Si vous avez utilisé l’un des exemples de packs Microsoft 365, il se peut que vous ayez déjà des utilisateurs de test dans votre locataire.  Sinon, vous devriez être en mesure d’en créer vous-même en tant qu’administrateur client.
1. À partir du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
2. Accédez à **Users**.
3. Cliquez sur **Nouvel utilisateur** et créez de nouveaux utilisateurs de test dans votre répertoire.

### <a name="get-an-azure-ad-subscription-optional"></a>Obtenir un abonnement à Azure AD (facultatif)
Si vous souhaitez tester pleinement les fonctionnalités Premium d’Azure AD sur votre application, vous devez inscrire votre locataire à une [licence Premium P1 ou Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).

Si vous vous êtes inscrit dans le cadre du programme pour développeur Microsoft 365, votre locataire de test disposera de licences Azure AD P2.  Sinon, vous pouvez toujours activer une [évaluation gratuite d’un mois d’Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

### <a name="create-and-configure-an-app-registration"></a>Créer et configurer une inscription d’application
Vous devez créer une inscription d’application à utiliser dans votre environnement de test.  Il doit s’agir d’une inscription distincte de la possible inscription de votre application de production, afin de maintenir une isolation de sécurité entre votre environnement de test et votre environnement de production.  La manière de configurer votre application dépend du type d’application que vous créez.  Pour plus d’informations, consultez les étapes relatives à l’inscription d’application de votre scénario d’application dans le volet de navigation de gauche, par exemple cet article sur l’[inscription d’une application web](scenario-web-app-sign-user-app-registration.md).
   
### <a name="populate-your-tenant-with-policies"></a>Remplir votre locataire avec des stratégies
Si votre application est principalement destinée à être utilisée par une seule organisation(communément appelée « locataire unique ») et que vous avez accès à ce locataire de production, vous devez essayer de répliquer les paramètres de votre locataire de production qui peuvent avoir un effet sur le comportement de votre application.  Cela réduira les risques d’erreurs inattendues lors de l’exploitation en production.

#### <a name="conditional-access-policies"></a>Stratégies d’accès conditionnel 
La réplication des stratégies d’accès conditionnel permet d’éviter tout blocage inattendu de l’accès lors du passage en production et permet à votre application de gérer correctement les erreurs qu’elle est susceptible de recevoir.

Il peut être nécessaire qu’un administrateur de l’entreprise consulte les stratégies d’accès conditionnel de votre locataire de production.  
1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre compte de locataire de production.
1. Accédez à **Azure Active Directory** > **Applications d’entreprise** > **Accès conditionnel**.
1. Affichez la liste des stratégies de votre locataire.  Cliquez sur la première.
1. Accédez à **Applications ou actions cloud**.  
1. Si la stratégie s’applique uniquement à un groupe sélectionné d’applications, passez à la stratégie suivante.  Si ce n’est pas le cas, elle s’appliquera probablement aussi à vos applications lorsque vous passerez en production.  Vous devez copier la stratégie sur votre locataire de test.

Dans un nouvel onglet ou une nouvelle session de navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous à votre locataire de test.
1. Accédez à **Azure Active Directory** > **Applications d’entreprise** > **Accès conditionnel**.
1. Cliquez sur **Nouvelle stratégie**.
1. Copiez les paramètres de la stratégie du locataire de production, identifiés lors des étapes précédentes.

#### <a name="permission-grant-policies"></a>Stratégies d’octroi d’autorisations
La réplication des stratégies d’octroi d’autorisations vous permet de ne pas être confronté à des invites inattendues du consentement administrateur lors du passage en production. 

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre compte de locataire de production.
1. Cliquez sur **Azure Active Directory**.
1. Accédez à **Applications d’entreprise**. 
1. Depuis votre locataire de production, ouvrez les paramètres en accédant à **Azure Active Directory** > **Applications d’entreprise** > **Consentement et autorisations** > **Consentement de l’utilisateur**.  Copiez les paramètres dans votre locataire de test.  
    
#### <a name="token-lifetime-policies"></a>Stratégies de durée de vie des jetons
La réplication des stratégies de durée de vie des jetons garantit que les jetons émis pour votre application n’expirent pas de manière inattendue en production.  
 
Les stratégies de durée de vie des jetons ne peuvent actuellement être gérées qu’à l’aide de PowerShell. Lisez la section sur les [durées de vie configurables des jetons](active-directory-configurable-token-lifetimes.md) pour savoir comment identifier les stratégies de durée de vie des jetons qui s’appliquent à l’ensemble de votre organisation de production.  Copiez ces stratégies dans votre locataire de test.
 
## <a name="set-up-a-test-environment-in-your-production-tenant"></a>Configurer un environnement de test dans votre locataire de production
Si vous pouvez restreindre en toute sécurité votre application de test dans votre locataire de production, configurez votre locataire à des fins de test.

### <a name="create-and-configure-an-app-registration"></a>Créer et configurer une inscription d’application
Vous devez créer une inscription d’application à utiliser dans votre environnement de test.  Il doit s’agir d’une inscription distincte de la possible inscription de votre application de production, afin de maintenir une isolation de sécurité entre votre environnement de test et votre environnement de production.  La manière de configurer votre application dépend du type d’application que vous créez.  Pour plus d’informations, consultez les [étapes relatives à l’inscription d’application de votre scénario d’application](scenario-web-app-sign-user-app-registration.md) dans le volet de navigation de gauche.

### <a name="create-some-test-users"></a>Créer des utilisateurs de test
Vous devrez créer des utilisateurs de test avec des données de test associées pour tester vos scénarios.  Cette étape peut être effectuée par un administrateur.
1. À partir du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
2. Accédez à **Users**.
3. Cliquez sur **Nouvel utilisateur** et créez de nouveaux utilisateurs de test dans votre répertoire.

### <a name="add-the-test-users-to-a-group-optional"></a>Ajouter les utilisateurs de test à un groupe (facultatif)
Pour des raisons pratiques, vous pouvez affecter tous ces utilisateurs à un groupe, ce qui facilite les autres opérations d’affectation.  
1. À partir du [portail Azure](https://portal.azure.com), cliquez sur **Azure Active Directory**.
2. Rendez-vous dans **Groupes**.
3. Cliquez sur **Nouveau groupe**.
4. Sélectionnez **Sécurité** ou **Microsoft 365** pour le type de groupe.
5. Nommez votre groupe.
6. Ajoutez les utilisateurs de test créés à l’étape précédente.

### <a name="restrict-your-test-application-to-specific-users"></a>Restreindre votre application de test à des utilisateurs spécifiques
Vous pouvez limiter les utilisateurs de votre locataire autorisés à utiliser votre application de test à des utilisateurs ou groupes spécifiques par le biais de l’attribution d’utilisateurs.  Lorsque vous [avez créé une application par le biais d’Inscriptions d’applications](#create-and-configure-an-app-registration), une représentation de votre application a également été créée dans **Applications d’entreprise**.  Utilisez les paramètres **Applications d’entreprise** pour limiter les personnes autorisées à utiliser l’application dans votre locataire.

> [!IMPORTANT]
> Si votre application est une [application mutualisée](v2-supported-account-types.md), cette opération n’empêchera pas les utilisateurs d’autres locataires de se connecter à votre application et de l’utiliser.  Elle limitera uniquement les utilisateurs du locataire dans lequel l’attribution d’utilisateurs est configurée. 

Pour obtenir des instructions détaillées sur la restriction d’une application à des utilisateurs spécifiques d’un locataire, consultez la page relative à la [restriction de votre application à un ensemble d’utilisateurs](howto-restrict-your-app-to-a-set-of-users.md).

## <a name="next-steps"></a>Étapes suivantes
 
En savoir plus sur [la limitation et les limites de service](test-throttle-service-limits.md) que vous pouvez rencontrer en configurant un environnement de test.

Pour plus d’informations sur les environnements de test, consultez [Sécurisation des environnements Azure avec Azure Active Directory](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/).
  
