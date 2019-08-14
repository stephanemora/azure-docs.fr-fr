---
title: Présentation d'Azure Active Directory Identity Protection (mise à jour) | Microsoft Docs
description: Présentation d'Azure Active Directory Identity Protection (mise à jour)
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2987f8fb116bfcbb1698335c3aca6f1fd8eb633e
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717286"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Présentation d'Azure Active Directory Identity Protection (mise à jour)

L'expérience Identity Protection a été mise à jour afin d'améliorer la protection des identités de votre organisation. Cette expérience mise à jour fournit :

- Une expérience d’administration repensée qui s’articule autour du risque le plus pertinent à l’égard du risque utilisateur et du risque de connexion
- Une expérience d'investigation approfondie avec prise en charge du filtrage, du tri et des téléchargements intelligents
- Une amélioration du calcul du risque utilisateur pour vous aider à concentrer vos efforts sur les utilisateurs les plus susceptibles d'être compromis
- La prise en charge d'une nouvelle API pour permettre un accès par programme aux données relatives aux risques
- Un processus de retour d'expérience administrateur simplifié qui vous permet de protéger immédiatement vos utilisateurs
- Une nouvelle fonctionnalité de Machine Learning supervisée pour une évaluation plus précise des risques

La sécurité est une priorité pour les organisations actuelles. La majorité des violations de la sécurité surviennent lorsque des cybercriminels parviennent à accéder à un environnement en dérobant l'identité d'un utilisateur. Aujourd’hui, les attaquants arrivent de plus en plus à exploiter les failles de fournisseurs tiers et utilisent des attaques par hameçonnage (ou « phishing ») sophistiquées toujours plus efficaces. Dès qu'un cybercriminel accède à un compte d'utilisateur, même si les privilèges de celui-ci sont faibles, il peut facilement accéder à des ressources importantes pour l'entreprise de manière latérale. 

Pour répondre à ces menaces, Azure AD Identity Protection vous donne les moyens de : 

- Empêcher proactivement le détournement des identités compromises 
- Atténuer automatiquement les risques lorsqu'une activité suspecte est détectée 
- Examiner les utilisateurs et les connexions à risque pour traiter les vulnérabilités potentielles  
- Être alerté lorsque le risque d'un utilisateur atteint un seuil spécifié 

Azure AD Identity Protection est une fonctionnalité d'Azure Active Directory Premium P2 qui vous permet de configurer des stratégies afin de réagir automatiquement lorsque l'identité d'un utilisateur est compromise ou qu'une personne autre que le propriétaire du compte tente de se connecter avec l'identité de celui-ci. Outre les autres contrôles d’accès conditionnel fournis par Azure AD, ces stratégies peuvent automatiquement bloquer l’accès ou déclencher des actions d’atténuation telles que la réinitialisation du mot de passe et la mise en œuvre de l’authentification multifacteur. De plus, Identity Protection fournit des fonctionnalités de supervision et de création de rapports qui vous permettent de mieux comprendre les risques et les compromissions potentiels au sein de votre organisation. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]

## <a name="risk-events"></a>Événements à risque

Azure AD Identity Protection détecte les événements à risque suivants : 

| Type d’événement à risque | Description | Type de détection |
| --- | --- | --- |
| Voyage inhabituel | Connexion à partir d'un emplacement inhabituel par rapport aux dernières connexions de l'utilisateur | Hors ligne |
| Adresse IP anonyme | Connexion à partir d'une adresse IP anonyme (par exemple : navigateur Tor, VPN anonymes). | Temps réel |
| Propriétés de connexion inhabituelles | Connexion avec des propriétés inhabituelles pour l'utilisateur concerné | Temps réel |
| Adresse IP liée à un programme malveillant | Connexion à partir d'une adresse IP liée à un programme malveillant | Hors ligne |
| Informations d'identification divulguées | Cet événement à risque indique que les informations d'identification valides de l'utilisateur ont fuité | Hors ligne |

## <a name="types-of-risk"></a>Types de risques 

Identity Protection repose sur deux types de risques :

- Risque à la connexion
- Risque de l’utilisateur

### <a name="sign-in-risk"></a>Risque à la connexion

Un risque de connexion reflète la probabilité qu'une demande d'authentification donnée soit rejetée par le propriétaire de l'identité.

Il existe deux types d'évaluation du risque de connexion : 

- **Risque de connexion (en temps réel)**  : le risque de connexion (en temps réel) repose sur toutes les détections en temps réel qui se déclenchent lors du traitement de la connexion.  
- **Risque de connexion (agrégat)**  : le risque de connexion (agrégat) est le risque total lié à une connexion. Il est calculé par un modèle Machine Learning qui prend en compte :
   - Les détections en temps réel (décrites plus haut)
   - Les détections hors connexion (qui se déclenchent après la connexion) 
   - Toutes les autres fonctionnalités de la connexion

### <a name="user-risk"></a>Risque de l’utilisateur

Un risque utilisateur reflète la probabilité qu'une identité donnée soit compromise. 

Le risque utilisateur est calculé en prenant en compte tous les risques associés à l'utilisateur :

- Toutes les connexions à risque
- Tous les événements à risque non liés à une connexion
- Le risque utilisateur actuel
- Toute action de correction des risques ou de rejet menée sur l’utilisateur jusqu’à ce jour

## <a name="how-identity-protection-detects-risk"></a>Comment Identity Protection détecte-t-il les risques ?  

Azure AD a recours au machine learning pour détecter les anomalies et les activités suspectes, en utilisant à la fois les signaux détectés en temps réel pendant la connexion et les autres signaux liés aux utilisateurs et à leurs activités de connexion. À l’aide de ces données, Identity Protection calcule un risque de connexion en temps réel chaque fois qu’un utilisateur s’authentifie et détermine un niveau de risque global pour chaque utilisateur. Identity Protection vous permet d'agir automatiquement sur ces détections de risques en configurant des stratégies de risque utilisateur et de risque de connexion Identity Protection.  

Pour comprendre comment Identity Protection détecte les risques, deux concepts importants doivent être pris en compte : le risque utilisateur et le risque de connexion. Le risque de connexion reflète la probabilité qu'une demande d'authentification donnée soit rejetée par le propriétaire de l'identité. Il existe deux types de risques de connexion : en temps réel et total. Le risque de connexion en temps réel est détecté au moment de la tentative de connexion (connexions à partir d'adresses IP anonymes, par exemple). Le risque de connexion total est la somme des risques de connexion détectés en temps réel et de tous les autres événements à risque ultérieurs associés aux connexions de l’utilisateur (voyage impossible, par exemple). Le risque utilisateur reflète la probabilité globale qu'une personne malveillante ait compromis une identité donnée. Le risque utilisateur englobe toutes les activités à risque liées à un utilisateur donné, notamment :

- Le risque de connexion en temps réel
- Le risque de connexion ultérieur
- Les détections d'utilisateurs à risque   

 ![Flux](./media/overview-v2/01.png)

Le parcours de détection d'un risque et de réponse d'Identity Protection pour une connexion donnée est résumé dans le graphique ci-dessus.  

## <a name="common-scenarios"></a>Scénarios courants 

Prenons l’exemple d’une employée de Contoso. 

1. L’employée tente de se connecter à Exchange Online à partir du navigateur Tor. Au moment de la connexion, Azure AD détecte des événements à risque en temps réel. 
2. Azure AD détecte que l’employée se connecte à partir d’une adresse IP anonyme, ce qui déclenche un niveau de risque de connexion moyen. 
3. L’employée est invitée à s’authentifier au moyen de l’authentification multifacteur (MFA), car l’administrateur informatique de Contoso a configuré la stratégie d’accès conditionnel en fonction du risque de connexion d’Identity Protection. Pour un risque de connexion moyen ou supérieur, la stratégie exige une authentification multifacteur (MFA). 
4. L’employée parvient à s’authentifier à l’aide de l’authentification MFA et accède à Exchange Online. Son niveau de risque utilisateur ne change pas. 

Que s'est-il passé en arrière-plan ? La tentative de connexion à partir du navigateur Tor a déclenché dans Azure AD un risque de connexion en temps réel en raison de l'adresse IP anonyme utilisée. Lors du traitement de la demande, Azure AD a appliqué la stratégie de risque de connexion configurée dans Identity Protection, car le niveau de risque de connexion de l’employée avait atteint le seuil Moyen. Comme l’employée s’était déjà inscrite pour l’authentification multifacteur, elle a pu s’authentifier à l’aide de cette méthode. Sa capacité à s’authentifier à l’aide de l’authentification multifacteur indique à Azure AD qu’elle est probablement la propriétaire légitime de l’identité. Par conséquent, son niveau de risque utilisateur n’augmente pas. 

Mais que se passerait-il si une personne autre essayait de se connecter ? 

1. Un acteur malveillant en possession des informations d’identification de l’employée tente de se connecter à son compte Exchange Online à partir du navigateur Tor, car il souhaite masquer sa propre adresse IP. 
2. Azure AD détecte que la tentative de connexion provient d'une adresse IP anonyme, ce qui déclenche un risque de connexion en temps réel. 
3. La personne malveillante reçoit une demande de MFA car l'administrateur informatique de Contoso a configuré la stratégie d'accès conditionnel en fonction du risque de connexion d'Identity Protection de manière à exiger une authentification multifacteur lorsque le risque de connexion est moyen ou supérieur. 
4. L’acteur malveillant ne parvient pas à s’authentifier à l’aide de l’authentification multifacteur et ne peut donc pas accéder au compte Exchange Online de l’employée. 
5. L’échec de l’authentification multifacteur a déclenché le signalement d’un événement à risque et a élevé le niveau de risque associé à l’employée. 

Maintenant qu’un acteur malveillant a tenté d’accéder au compte de l’employée, voyons ce qui se passera la prochaine fois qu’elle tentera de se connecter. 

1. L’employée tente de se connecter à Exchange Online à partir d’Outlook. Au moment de la connexion, Azure AD détecte les événements à risque en temps réel ainsi que les risques utilisateur antérieurs. 
2. Azure AD ne détecte aucun risque de connexion en temps réel, mais détecte un risque utilisateur élevé en raison de l'activité à risque des scénarios précédents.  
3. L’employée est invitée à réinitialiser son mot de passe, car l’administrateur informatique de Contoso a configuré la stratégie de risque utilisateur d’Identity Protection de manière à exiger un changement de mot de passe lorsqu’un utilisateur présentant un risque élevé se connecte. 
4. Étant donné que l’employée est inscrite pour la réinitialisation de mot de passe en libre-service et l’authentification multifacteur, elle peut réinitialiser son mot de passe. 
5. Une fois le mot de passe réinitialisé, les informations d’identification de l’employée ne sont plus compromises et son identité est de nouveau sécurisée. 
6. Les précédents événements à risque liés à l’employée sont résolus et son niveau de risque utilisateur est automatiquement réinitialisé en réponse à l’atténuation de la compromission des informations d’identification. 

## <a name="how-do-i-configure-identity-protection"></a>Comment configurer Identity Protection ? 

Avant de commencer à utiliser Identity Protection, configurez une stratégie de risque utilisateur et une stratégie de risque de connexion. Une fois ces stratégies configurées et appliquées à un groupe de test, vous pouvez simuler des événements à risque pour comprendre comment Identity Protection réagira dans votre environnement. Les guides de démarrage rapide ci-dessous expliquent comment configurer les stratégies susmentionnées et les tester dans votre environnement. 

Identity Protection prend en charge trois rôles dans Azure AD pour équilibrer les activités de gestion autour de votre déploiement : 

| Role | Peut | Ne peut pas |
| --- | --- | --- |
| Administrateur général | Accès complet à Identity Protection, Onboard Identity Protection | |
| Administrateur de sécurité | Accès complet à Identity Protection | Intégrer Identity Protection, réinitialiser les mots de passe d'un utilisateur |
| Lecteur de sécurité | Accès en lecture seule à Identity Protection | Intégrer Identity Protection, corriger des utilisateurs, configurer des stratégies, réinitialiser les mots de passe| 

Pour plus d’informations, consultez [Attribution de rôles d’administrateur dans Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).
 
## <a name="licensing"></a>Gestion des licences

>[!NOTE]
> Avec la préversion publique d'Identity Protection (mise à jour), seuls les clients Azure AD Premium P2 auront accès au rapport sur les utilisateurs à risque et au rapport sur les connexions à risque.

| Fonctionnalité | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Free |
| --- | --- | --- | --- |
| Stratégie de risque d’utilisateur | OUI | Non | Non |
| Stratégie en matière de risque à la connexion | OUI | Non | Non |
| Rapport sur les utilisateurs à risque | Accès total | Informations limitées | Informations limitées |
| Rapports sur les connexions risquées | Accès total | Informations limitées | Informations limitées |
| Stratégie d'inscription MFA | OUI | Non | Non |

## <a name="next-steps"></a>Étapes suivantes 

Pour vous familiariser avec Identity Protection, consultez [Configurer la stratégie de connexion à risque](quickstart-sign-in-risk-policy.md). 
