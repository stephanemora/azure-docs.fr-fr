---
title: Protéger Microsoft 365 des attaques locales
description: Conseils pour protéger Microsoft 365 des attaques locales
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97893dece068dfdde85159f734095401288231d2
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201346"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Protéger Microsoft 365 des attaques locales

Beaucoup de clients connectent leurs réseaux d'entreprise privés à Microsoft 365 pour permettre à leurs utilisateurs, appareils et applications d'en bénéficier. Mais il existe de nombreuses façons bien documentées de compromettre ces réseaux privés. Dans la mesure où Microsoft 365 est le « système nerveux » de nombreuses organisations, il doit impérativement être protégé des atteintes à la sécurité de l'infrastructure locale.

Cet article vous explique comment configurer vos systèmes pour protéger votre environnement cloud Microsoft 365 de toute atteinte à la sécurité locale. Nous nous concentrerons principalement sur les paramètres de configuration des locataires Azure AD, sur la connexion sécurisée des locataires Azure AD aux systèmes locaux, et sur les compromis nécessaires pour utiliser vos systèmes tout en protégeant vos systèmes cloud des atteintes à la sécurité locales.

Nous vous recommandons vivement de suivre ces conseils pour sécuriser votre environnement cloud Microsoft 365.
> [!NOTE]
> Cet article a initialement été publié sous forme de billet de blog. Il a été déplacé ici à des fins de longévité et de maintenance. <br>
Pour créer une version hors connexion de cet article, utilisez la fonctionnalité Imprimer dans un PDF de votre navigateur. Revenez régulièrement sur cette page pour consulter les mises à jour.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Principaux vecteurs de menace liés aux environnements locaux compromis


Votre environnement cloud Microsoft 365 bénéficie d'une infrastructure de surveillance et de sécurité étendue. L'association de l'apprentissage automatique et de l'intelligence humaine pour surveiller le trafic mondial permet de détecter rapidement les attaques et de reconfigurer l'environnement en quasi-temps réel. Dans les déploiements hybrides qui connectent l'infrastructure locale à Microsoft 365, de nombreuses organisations délèguent l'approbation à des composants locaux pour les décisions critiques liés à l'authentification et à la gestion de l'état des objets d'annuaire.
Malheureusement, lors d'une atteinte à la sécurité de l'environnement local, ces relations d'approbation permettent aux attaquants de compromettre votre environnement Microsoft 365.

Les deux principaux vecteurs de menace sont les **relations d'approbation de fédération** et la **synchronisation des comptes.** Ces deux vecteurs peuvent octroyer à un attaquant un accès administratif à votre cloud.

* **Les relations d'approbation fédérées**, telles que l'authentification SAML, vous permettent de vous authentifier auprès de Microsoft 365 via votre infrastructure d'identité locale. Si un certificat de signature de jetons SAML est compromis, la fédération permet à toute personne en possession de ce certificat d'emprunter l'identité de n'importe quel utilisateur de votre cloud. **Si possible, nous vous recommandons de désactiver les relations d'approbation de fédération pour l'authentification auprès de Microsoft 365.**

* **La synchronisation des comptes** peut être utilisée pour modifier des utilisateurs privilégiés (ainsi que leurs informations d'identification) ou des groupes bénéficiant de privilèges administratifs dans Microsoft 365. **Veillez à ce que les objets synchronisés ne détiennent aucun privilège dépassant le champ d'action d'un utilisateur Microsoft 365,** soit directement soit via une inclusion dans des rôles ou des groupes approuvés. Assurez-vous que ces objets ne disposent d'aucune attribution directe ou imbriquée dans des rôles ou groupes cloud approuvés.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Protéger Microsoft 365 des atteintes à la sécurité locales


Pour lutter contre les vecteurs de menace décrits précédemment, nous vous recommandons de vous conformer aux principes illustrés ci-dessous :

![Architecture de référence pour assurer la protection de Microsoft 365 ](media/protect-m365/protect-m365-principles.png)

*  **Isolez complètement vos comptes administrateur Microsoft 365.** Ceux-ci doivent être :

    * contrôlés dans Azure AD ;

     * authentifiés au moyen de l'authentification multifacteur (MFA) ;

     *  sécurisés par l'accès conditionnel Azure AD ;

     *  uniquement accessibles via les stations de travail gérées par Azure.

Il s'agit de comptes à usage restreint. **Aucun compte local doté de privilèges administratifs ne doit figurer dans Microsoft 365.** Pour plus d'informations, consultez cette [présentation des rôles administrateur Microsoft 365](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).
Consultez également [Rôles liés à Microsoft 365 dans Azure Active Directory](../roles/m365-workload-docs.md).

*  **Gérez les appareils à partir de Microsoft 365.** Utilisez les fonctionnalités de jonction Azure AD et de gestion des périphériques mobiles (GMP) basée sur le cloud pour éliminer de votre infrastructure locale de gestion des périphériques les dépendances susceptibles de nuire aux contrôles des appareils et de la sécurité.

* **Aucun compte local ne dispose de privilèges élevés vis-à-vis de Microsoft 365.**
    Les comptes qui accèdent à des applications locales exigeant une authentification NTLM, LDAP ou Kerberos doivent disposer d'un compte dans l'infrastructure d'identité locale de l'organisation. Assurez-vous que ces comptes, de même que les comptes de service, ne sont pas inclus dans des rôles ou des groupes cloud privilégiés, et que les modifications apportées à ces comptes ne peuvent pas nuire à l'intégrité de votre environnement cloud. Les logiciels locaux privilégiés ne doivent pas avoir d'impact sur les comptes ou rôles Microsoft 365 privilégiés.

*  **Utilisez l'authentification cloud Azure AD** pour éliminer les dépendances sur vos informations d'identification locales. Ayez toujours recours à une authentification forte, telle que Windows Hello, Rex, Microsoft Authenticator ou Azure AD MFA.

## <a name="specific-recommendations"></a>Recommandations spécifiques


Les sections suivantes fournissent des conseils spécifiques sur le respect des principes décrits ci-dessus.

### <a name="isolate-privileged-identities"></a>Isoler les identités privilégiées


Dans Azure AD, les utilisateurs disposant de rôles privilégiés, tels que les administrateurs, sont généralement ceux sur lesquels reposent la création et la gestion du reste de l'environnement. Conformez-vous aux pratiques suivantes pour réduire l'impact d'une atteinte à la sécurité.

* Utilisez des comptes « cloud uniquement » pour les rôles privilégiés Azure AD et Microsoft 365.

* Déployez des [appareils à accès privilégié](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) afin de disposer d'un accès privilégié pour gérer Microsoft 365 et Azure AD.

*  Déployez [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) pour l'accès juste-à-temps (JAT) à tous les comptes humains disposant de rôles privilégiés et nécessitant une authentification forte pour activer les rôles.

* Accordez aux rôles administratifs le [moins de privilèges possible pour accomplir leurs tâches](../roles/delegate-by-task.md).

* Pour bénéficier d'une expérience d'attribution de rôle enrichie incluant la délégation et plusieurs rôles en même temps, envisagez d'utiliser des groupes de sécurité Azure AD ou des groupes Microsoft 365 (collectivement appelés « groupes cloud ») et [activez le contrôle d'accès en fonction du rôle](../roles/groups-assign-role.md). Vous pouvez également utiliser des [unités administratives](../roles/administrative-units.md) pour limiter l'étendue des rôles à une partie de l'organisation.

* Déployez des [comptes d'accès d'urgence](../roles/security-emergency-access.md), et n'utilisez PAS de coffres de mots de passe locaux pour stocker les informations d'identification.

Pour plus d'informations, consultez [Sécuriser l'accès privilégié](https://aka.ms/SPA). Consultez également [Pratiques d'accès sécurisé pour les administrateurs dans Azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Utiliser l'authentification cloud 

Les informations d'identification constituent le premier vecteur d'attaque. Conformez-vous aux pratiques suivantes pour renforcer la sécurité des informations d'identification.

* [Déployez l'authentification sans mot de passe](../authentication/howto-authentication-passwordless-deployment.md) : réduisez autant que possible l'utilisation de mots de passe en déployant des informations d'identification sans mot de passe. Ces informations d'identification sont gérées et validées en mode natif dans le cloud. Choisissez parmi :

   * [Windows Hello Entreprise](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Application Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [Clés de sécurité FIDO2](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Déployez l'authentification multifacteur](https://aka.ms/deploymentplans/mfa) : Approvisionnez [plusieurs informations d'identification fortes à l'aide d'Azure AD MFA](../fundamentals/resilience-in-credentials.md). Ainsi, l'accès aux ressources cloud nécessitera des informations d'identification gérées dans Azure AD en plus d'un mot de passe local qui peut être manipulé.

   * Pour plus d'informations, consultez [Créer une stratégie de gestion du contrôle d'accès résiliente avec Azure Active Directory](https://aka.ms/resilientaad).

**Limitations et compromis**

* La gestion des mots de passe des comptes hybrides nécessite des composants hybrides tels que des agents de protection par mot de passe et des agents de réécriture du mot de passe. Si votre infrastructure locale est compromise, des attaquants peuvent contrôler les ordinateurs sur lesquels ces agents résident. Bien que cela ne compromette pas votre infrastructure cloud, vos comptes cloud ne protégeront pas ces composants des atteintes à la sécurité locales.

*  Les comptes locaux synchronisés à partir d'Active Directory sont marqués pour ne jamais expirer dans Azure AD, en partant du principe que les stratégies de mot de passe AD locales atténueront ce problème. Si votre instance AD locale est compromise et que la synchronisation à partir d'AD Connect doit être désactivée, vous devez définir l'option [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="provision-user-access-from-the-cloud"></a>Approvisionner l'accès utilisateur à partir du cloud

L'approvisionnement fait référence à la création de comptes et de groupes d'utilisateurs dans des applications ou fournisseurs d'identité.

![Diagramme de l'architecture d'approvisionnement](media/protect-m365/protect-m365-provision.png)

* **Approvisionnement entre des applications cloud de RH et Azure AD :** permet d'isoler une atteinte à la sécurité locale sans interrompre le cycle JML (Joiner-Mover-Leaver) entre vos applications cloud de RH et Azure AD.

* **Applications cloud :** dans la mesure du possible, déployez [Azure AD App Provisioning](../app-provisioning/user-provisioning.md) plutôt que des solutions d'approvisionnement locales. Cela permettra d'éviter que certaines de vos applications SaaS ne soient corrompues par des profils utilisateur malveillants suite à des violations locales. 

* **Identités externes :** utilisez [Azure AD B2B Collaboration](../external-identities/what-is-b2b.md).
    Cela réduira la dépendance vis-à-vis des comptes locaux pour la collaboration externe avec les partenaires, les clients et les fournisseurs. Évaluez avec soin toute fédération directe avec d'autres fournisseurs d'identité. Nous vous recommandons de limiter les comptes invités B2B de l'une des manières suivantes.

   *  Limitez l'accès invité à des groupes de navigation et à d'autres propriétés de l'annuaire. Utilisez les paramètres de collaboration externe pour limiter la capacité des invités à lire des groupes dont ils ne sont pas membres. 

    *   Bloquez l'accès au portail Azure. Vous pouvez raréfier les exceptions nécessaires.  Créez une stratégie d'accès conditionnel qui comprend tous les invités et utilisateurs externes, puis [implémentez une stratégie pour bloquer l'accès](/azure/role-based-access-control/conditional-access-azure-management). 

* **Forêts déconnectées :** utilisez l'[approvisionnement cloud Azure AD](../cloud-provisioning/what-is-cloud-provisioning.md). Cela vous permettra de vous connecter à des forêts déconnectées, ce qui vous évitera d'avoir à établir une connectivité ou des approbations entre forêts, lesquelles pourraient étendre l'impact d'une violation locale. * 
 
**Limitations et compromis :**

* Lorsqu'elle est utilisée pour approvisionner des comptes hybrides, l'instance Azure AD des systèmes de RH dans le cloud s'appuie sur la synchronisation locale pour acheminer les données entre AD et Azure AD. Si la synchronisation est interrompue, les enregistrements des nouveaux employés ne seront pas disponibles dans Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Utiliser des groupes cloud pour la collaboration et l'accès

Les groupes cloud vous permettent de dissocier votre collaboration et votre accès de votre infrastructure locale.

* **Collaboration :** utilisez des groupes Microsoft 365 et Microsoft Teams pour bénéficier d'une collaboration moderne. Désactivez les listes de distribution locales et procédez à une [Mise à niveau des listes de distribution vers des groupes Microsoft 365 dans Outlook](https://docs.microsoft.com/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide).

* **Accès :** utilisez des groupes de sécurité Azure AD ou des groupes Microsoft 365 pour autoriser l'accès aux applications dans Azure AD.
* **Licence Office 365 :** utilisez une licence basée sur les groupes pour approvisionner Office 365 avec des groupes cloud uniquement. Cela permettra de dissocier le contrôle de l'appartenance à un groupe de l'infrastructure locale.

Les propriétaires des groupes utilisés pour l'accès doivent être considérés comme des identités privilégiées afin d'éviter la prise de contrôle de l'appartenance à un groupe en cas d'atteinte à la sécurité locale.
La prise en charge comprend la manipulation directe de l'appartenance à un groupe au niveau local ou la manipulation d'attributs locaux qui peuvent affecter l'appartenance dynamique à un groupe dans Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Gérer des périphériques à partir du cloud


Utilisez les fonctionnalités d'Azure AD pour gérer les appareils en toute sécurité.

-   **Utiliser des stations de travail Windows 10 :** [déployez des appareils joints à Azure AD](../devices/azureadjoin-plan.md) avec des stratégies GPM. Activez [Windows AutoPilot](https://docs.microsoft.com/mem/autopilot/windows-autopilot) pour une expérience d'approvisionnement entièrement automatisée.

    -   Dépréciez les ordinateurs Windows 8.1 (et versions antérieures).

    -   Ne déployez pas d'ordinateurs avec SE serveur en tant que stations de travail.

    -   Utilisez [Microsoft Intune](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) comme source d'autorité pour toutes les charges de travail de gestion des périphériques.

-   [**Déployez des appareils à accès privilégié**](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles) afin de bénéficier d'un accès privilégié pour gérer Microsoft 365 et Azure AD.

 ## <a name="workloads-applications-and-resources"></a>Charges de travail, applications et ressources 

-   **Systèmes SSO locaux :** dépréciez toute infrastructure de fédération et de gestion d'accès au web locale, et configurez les applications de manière à utiliser Azure AD.  

-   **Applications SaaS et applications métier prenant en charge les protocoles d'authentification modernes :** [utilisez Azure AD pour l'authentification unique (SSO)](../manage-apps/what-is-single-sign-on.md). Plus vous configurez d'applications afin d'utiliser Azure AD pour l'authentification, moins il y a de risque en cas d'atteinte à la sécurité locale.


* **Applications héritées** 

   * L'authentification, l'autorisation et l'accès à distance aux applications héritées qui ne prennent pas en charge l'authentification moderne peuvent être activés par le biais d'[Azure AD Application Proxy](../manage-apps/application-proxy.md), ou par une solution de contrôle de livraison d'applications ou réseau utilisant des [intégrations partenaires d'accès hybride sécurisé.](../manage-apps/secure-hybrid-access.md)   

   * Choisissez un fournisseur de VPN qui prend en charge l'authentification moderne et intégrez son authentification à Azure AD. En cas d'atteinte à la sécurité non locale, vous pouvez utiliser Azure AD pour désactiver ou bloquer l'accès en désactivant le VPN.

*  **Serveurs d'applications et de charges de travail**

   * Les applications ou les ressources qui avaient besoin de serveurs peuvent être migrées vers Azure IaaS et utiliser [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) (Azure AD DS) pour dissocier l'approbation et la dépendance vis-à-vis de l'instance AD locale. Pour réaliser cette dissociation, les réseaux virtuels utilisés pour Azure AD DS ne doivent pas être connectés aux réseaux d'entreprise.

   * Suivez les instructions relatives à la [hiérarchisation des informations d'identification](https://aka.ms/TierModel). Les serveurs d'applications sont généralement considérés comme des ressources de niveau 1.

 ## <a name="conditional-access-policies"></a>Stratégies d’accès conditionnel

Utilisez l'accès conditionnel Azure AD pour interpréter les signaux et prendre des décisions d'authentification sur la base de ceux-ci. Pour plus d'informations, consultez le [Plan de déploiement de l'accès conditionnel.](https://aka.ms/deploymentplans/ca)

* [Protocoles d'authentification hérités](../fundamentals/auth-sync-overview.md) : utilisez l'accès conditionnel pour [bloquer les protocoles d'authentification hérités](../conditional-access/howto-conditional-access-policy-block-legacy.md) lorsque cela est possible. En outre, désactivez les protocoles d'authentification hérités au niveau de l'application en utilisant une configuration spécifique à l'application.

   * Consultez les informations spécifiques à [Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) et[SharePoint Online](https://docs.microsoft.com/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps).

* Implémentez les [configurations recommandées pour l'identité et l'accès aux appareils.](https://docs.microsoft.com/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)

* Si vous utilisez une version d'Azure AD qui n'inclut pas l'accès conditionnel, veillez à utiliser les [paramètres de sécurité par défaut d'Azure AD](../fundamentals/concept-fundamentals-security-defaults.md).

   * Pour plus d'informations sur les licences des fonctionnalités Azure AD, consultez le [Guide de tarification d'Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitoring"></a>Surveillance 

Une fois que vous avez configuré votre environnement pour protéger Microsoft 365 de toute atteinte à la sécurité locale, vous devez [surveiller l'environnement de manière proactive](../reports-monitoring/overview-monitoring.md).
### <a name="scenarios-to-monitor"></a>Scénarios à surveiller

Surveillez les scénarios clés suivants, en plus des scénarios propres à votre organisation. Par exemple, vous devez surveiller de manière proactive l'accès à vos applications et ressources stratégiques.

* **Activité suspecte** : tous les [événements Azure AD à risque](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#risk-detection-and-remediation) doivent être surveillés pour détecter toute activité suspecte. [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) est intégré en mode natif à Azure Security Center.

   * Définissez les [emplacements nommés](../reports-monitoring/quickstart-configure-named-locations.md) du réseau pour éviter les détections bruyantes sur les signaux basés sur l'emplacement. 
*  **Alertes liées à l'analyse comportementale des utilisateurs et des entités** : utilisez l'analyse comportementale des utilisateurs et des entités pour bénéficier d'insights sur la détection des anomalies.
   * Microsoft Cloud App Discovery (MCAS) fournit [une analyse comportementale des utilisateurs et des entités dans le cloud](https://docs.microsoft.com/cloud-app-security/tutorial-ueba).

   * Vous pouvez [intégrer l'analyse comportementale des utilisateurs et des entités locaux à partir d'Azure ATP](https://docs.microsoft.com/defender-for-identity/install-step2). MCAS lit les signaux d'Azure AD Identity Protection. 

* **Activité des comptes d'accès d'urgence** : tout accès par le biais de [comptes d'accès d'urgence](../roles/security-emergency-access.md) doit être surveillé et des alertes doivent être créées à des fins d'investigation. Cette surveillance doit englober ce qui suit : 

   * Connexions. 

   * Gestion des informations d'identification. 

   * Toutes les mises à jour relatives aux appartenances à des groupes. 

   *    Attributions des applications. 
* **Activité des rôles privilégiés** : configurez et examinez les [alertes de sécurité générées par Azure AD PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-configure-security-alerts?tabs=new#security-alerts).
    Surveillez l'attribution directe des rôles privilégiés en dehors de PIM en générant des alertes chaque fois qu'un utilisateur est directement attribué.
* **Configurations Azure AD à l'échelle des locataires** : toute modification apportée aux configurations à l'échelle des locataires doit générer des alertes dans le système. Ces alertes portent notamment sur ce qui suit :
  *  Mise à jour des domaines personnalisés  

  * Modifications apportées à la liste d'autorisation/de blocage Azure AD B2B.
  * Fournisseurs d'identité Azure AD B2B autorisés (fournisseurs d'identité SAML par fédération directe ou connexion aux réseaux sociaux).  
  * Modifications de la stratégie d'accès conditionnel ou de risque 

* **Objets liés aux applications et aux principaux de service** :
   * nouvelles applications ou nouveaux principaux de service susceptibles de nécessiter des stratégies d'accès conditionnel. 

   * Informations d'identification supplémentaires ajoutées aux principaux de service.
   * Activité de consentement d'application. 

* **Rôles personnalisés** :
   * Mises à jour des définitions des rôles personnalisés. 

   * Création de nouveaux rôles personnalisés. 

### <a name="log-management"></a>Gestion du journal

Définissez une stratégie de stockage et de conservation des journaux, ainsi que la conception et l'implémentation associées, afin de faciliter l'utilisation d'un ensemble d'outils cohérents (tels que les systèmes SIEM comme Azure Sentinel), de requêtes courantes et de guides opérationnels.

* **Journaux Azure AD** : ingérez les journaux et les signaux générés en suivant les meilleures pratiques, y compris les paramètres de diagnostic, la conservation des journaux et l'ingestion SIEM. La stratégie de journalisation doit inclure les journaux Azure AD suivants :
   * Activité de connexion 

   * Journaux d’audit 

   * Événements à risque 

Azure AD fournit une [intégration Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md) pour le journal des activités de connexion et les journaux d'audit. Les événements à risque peuvent être ingérés via l'[API Microsoft Graph](https://aka.ms/AzureADSecuredAzure/32b). Vous pouvez [transmettre les journaux Azure AD aux journaux Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Journaux de sécurité du système d'exploitation de l'infrastructure hybride.** Tous les journaux du système d'exploitation de l'infrastructure d'identité hybride doivent être archivés et soigneusement surveillés en tant que <br>système de niveau 0 compte tenu des implications en termes de surface d'exposition. notamment : 

   *  Azure AD Connect. [Azure AD Connect Health](https://aka.ms/AzureADSecuredAzure/32e) doit être déployé pour surveiller la synchronisation des identités.

   *  Agents de proxy d'application 


   * Agents d'écriture différée de mot de passe 

   * Ordinateurs avec passerelle de protection par mot de passe  

   * NPS disposant de l'extension Azure MFA RADIUS 

## <a name="next-steps"></a>Étapes suivantes
* [Créer une résilience dans la gestion des identités et des accès avec Azure AD](resilience-overview.md)

* [Accès externe sécurisé aux ressources](secure-external-access-resources.md) 
* [Intégrer toutes vos applications à Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)
