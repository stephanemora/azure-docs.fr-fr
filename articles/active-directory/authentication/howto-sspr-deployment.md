---
title: Déploiement de réinitialisation de mot de passe en libre-service - Azure Active Directory
description: Stratégie pour réussir l’implémentation de la réinitialisation du mot de passe en libre-service Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc2c68c53a7c03d1de08e5cde528f27aa61b0096
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847267"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Déployer la réinitialisation de mot de passe en libre-service Azure AD

> [!NOTE]
> Ce guide décrit la réinitialisation de mot de passe en libre-service et explique comment la déployer. Si vous recherchez l’outil de réinitialisation de mot de passe en libre-service pour revenir à votre compte, accédez à [https://aka.ms/sspr](https://aka.ms/sspr). 

La réinitialisation de mot de passe en libre-service (SSPR) est une fonctionnalité d’Azure Active Directory qui permet aux employés de réinitialiser leurs mots de passe sans devoir contacter le personnel informatique. Les employés doivent s’inscrire ou être inscrits à ce service avant de l’utiliser. Pendant l’inscription, l’employé choisit une ou plusieurs méthodes d’authentification activées par son organisation.

SSPR permet aux employés de rapidement débloquer leur compte et de continuer à travailler, quels que soient l’heure ou l’endroit où ils se trouvent. En permettant aux utilisateurs de débloquer leur compte eux-mêmes, votre organisation peut réduire les pertes de productivité et les coûts de support élevés pour les problèmes les plus courants liés aux mots de passe.

Aidez les utilisateurs à s’inscrire rapidement en déployant SSPR parallèlement à une autre application ou service dans votre organisation. Cette action génère un grand nombre de connexions et booste les inscriptions.

Avant de déployer SSPR, les organisations ont tout intérêt à déterminer le nombre d’appels reçus par le support technique concernant la réinitialisation de mot de passe au fil du temps et le coût moyen de chaque appel. Elles peuvent utiliser ces données après le déploiement pour connaître la valeur ajoutée de SSPR.  

## <a name="how-sspr-works"></a>Fonctionnement de SSPR

1. Lorsqu’un utilisateur tente de réinitialiser un mot de passe, il doit valider la méthode d’authentification précédemment inscrite pour prouver son identité.
1. Ensuite, l’utilisateur entre un nouveau mot de passe.
   1. Pour les utilisateurs cloud uniquement, le nouveau mot de passe est stocké dans Azure Active Directory. Pour plus d’informations, consultez l’article [Fonctionnement de SSPR](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. Pour les utilisateurs hybrides, le mot de passe est réécrit en local sur Active Directory via le service Azure AD Connect. Pour plus d’informations, consultez l’article [Fonctionnement de la réécriture du mot de passe](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Considérations relatives aux licences

Azure Active Directory fonctionne la base d’une licence unique par utilisateur, c’est-à-dire que chaque utilisateur doit disposer d’une licence appropriée pour les fonctionnalités qu’il utilise.

Pour plus d’informations sur les licences, consultez la [page des tarifs Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Activer une inscription combinée pour SSPR et MFA

Microsoft recommande aux organisations d’activer l’expérience combinée d’inscription à SSPR et MFA (authentification multifacteur). Lorsque vous activez cette expérience d’inscription combinée, les utilisateurs ne doivent sélectionner qu’une fois leurs informations d’inscription pour activer ces deux fonctionnalités.

![Inscription d’informations de sécurité combinée](./media/howto-sspr-deployment/combined-security-info.png)

L’expérience d’inscription combinée n’oblige pas les organisations à activer SSPR et Azure MFA. L’inscription combinée fournit aux organisations une meilleure expérience utilisateur par rapport aux composants individuels traditionnels. Vous trouverez plus d’informations sur l’inscription combinée dans l’article [Inscription d’informations de sécurité combinée (préversion)](concept-registration-mfa-sspr-combined.md).

## <a name="plan-the-configuration"></a>Planifier la configuration

Les paramètres suivants sont requis pour activer SSPR avec les valeurs recommandées.

| Domaine | Paramètre | Valeur |
| --- | --- | --- |
| **Propriétés SSPR** | Réinitialisation de mot de passe en libre-service activée | Groupe **sélectionné** pour le projet pilote / **Tous** pour la production |
| **Méthodes d’authentification** | Méthodes d’authentification requises pour l’inscription | Toujours 1 de plus que nécessaire pour la réinitialisation |
|   | Méthodes d’authentification requises pour la réinitialisation | Une ou deux |
| **Inscription** | Obliger les utilisateurs à s’inscrire durant la connexion ? | OUI |
|   | Nombre de jours avant que les utilisateurs ne soient invités à reconfirmer leurs informations d’authentification | 90 à 180 jours |
| **Notifications** | Notifier les utilisateurs lors des réinitialisations de mot de passe ? | OUI |
|   | Notifier tous les administrateurs quand d’autres administrateurs réinitialisent leur mot de passe ? | OUI |
| **Personnalisation** | Personnaliser le lien du support technique | OUI |
|   | URL ou adresse e-mail personnalisée du support technique | Adresse e-mail ou site du support technique |
| **Intégration locale** | Écriture différée des mots de passe dans AD en local | OUI |
|   | Autoriser les utilisateurs à déverrouiller leur compte sans réinitialiser leur mot de passe | OUI |

### <a name="sspr-properties-recommendations"></a>Recommandations pour les propriétés SSPR

Lorsque vous activez la réinitialisation de mot de passe en libre-service, choisissez un groupe de sécurité à utiliser pendant la phase pilote.

Lorsque vous prévoyez d’étendre le service, nous vous recommandons d’utiliser l’option Tous afin de renforcer SSPR pour tous les membres de l’organisation. Si vous ne pouvez pas définir la valeur Tous, sélectionnez le groupe de sécurité Azure AD approprié ou le groupe AD synchronisé avec Azure AD.

### <a name="authentication-methods"></a>Méthodes d’authentification

Définissez les méthodes d’authentification requises pour l’inscription ; il doit y en avoir au moins une de plus que le nombre requis pour la réinitialisation. Plus vous en définissez, plus les utilisateurs ont de flexibilité lorsqu’ils doivent réinitialiser leur mot de passe.

Définissez le **nombre de méthodes requises pour la réinitialisation** sur un niveau approprié pour votre organisation. Le plus simple est d’en choisir une seule, tandis que deux peuvent améliorer l’état de la sécurité.

Consultez la [présentation des méthodes d’authentification](concept-authentication-methods.md) pour obtenir plus d’informations sur les méthodes d'authentification disponibles pour SSPR, les questions de sécurité prédéfinies et la création de questions de sécurité personnalisées.

### <a name="registration-settings"></a>Paramètres d’inscription

Définissez l’option **Obliger les utilisateurs à s’inscrire durant la connexion** sur **Oui**. Ce paramètre signifie que les utilisateurs sont obligés de s’inscrire durant la connexion, garantissant ainsi leur protection.

Définissez le **nombre de jours avant que les utilisateurs soient invités à reconfirmer leurs informations d’authentification** sur une valeur comprise entre **90** et **180** jours, sauf si votre organisation dispose d’une entreprise exige une période plus courte.

### <a name="notifications-settings"></a>Paramètres de notifications

Configurer les paramètres **Notifier les utilisateurs lors des réinitialisations de mot de passe** et **Notifier tous les administrateurs quand d’autres administrateurs réinitialisent leur mot de passe** sur **Oui**. En sélectionnant **Oui** pour les deux paramètres, vous augmentez la sécurité en vous assurant que les utilisateurs soient informés lorsque leur mot de passe est réinitialisé, et que tous les administrateurs soient avertis lorsqu’un administrateur modifie un mot de passe. Si les utilisateurs ou les administrateurs reçoivent une notification et qu’ils ne sont pas à l’origine de la modification, ils peuvent signaler immédiatement une violation de sécurité potentielle.

### <a name="customization"></a>Personnalisation

Il est essentiel de personnaliser l’**adresse e-mail ou URL du support technique** pour garantir les utilisateurs qui rencontrent des problèmes puissent obtenir rapidement de l’aide. Définissez cette option sur une page web ou une adresse e-mail de support technique courante que vos utilisateurs connaissent.

### <a name="on-premises-integration"></a>Intégration locale

Si vous avez un environnement hybride, vérifiez que l’option **Écriture différée des mots de passe dans AD en local** est définie sur **Oui**. Définissez également l’option Autoriser les utilisateurs à déverrouiller leur compte sans réinitialiser leur mot de passe sur Oui, car cela leur laisse davantage de flexibilité.

### <a name="changingresetting-passwords-of-administrators"></a>Modification/réinitialisation des mots de passe des administrateurs

Les comptes d’administrateur sont des comptes spéciaux avec des autorisations élevées. Pour les sécuriser, les restrictions suivantes s’appliquent à la modification des mots de passe des administrateurs :

- Les administrateurs d’entreprise en local ou les administrateurs de domaine ne peuvent pas réinitialiser leur mot de passe via SSPR. Ils peuvent uniquement modifier leur mot de passe dans leur environnement local. Par conséquent, nous vous recommandons de ne pas synchroniser les comptes d’administrateur AD locaux avec Azure AD.
- Un administrateur ne peut pas utiliser les questions/réponses secrètes comme méthode pour réinitialiser le mot de passe.

### <a name="environments-with-multiple-identity-management-systems"></a>Environnements avec plusieurs systèmes de gestion de l’identité

S’il existe plusieurs systèmes de gestion de l’identité dans un environnement tel que les gestionnaires d’identité locaux (par exemple, Oracle AM, SiteMinder ou d’autres systèmes), il faudra peut-être synchroniser les mots de passe écrits dans Active Directory avec les autres systèmes à l’aide d’un outil tel que le service de notification de modification de mot de passe (PCNS) avec Microsoft Identity Manager (MIM). Pour plus d’informations sur ce scénario plus complexe, consultez l’article [Déployer le service de notification de modification de mot de passe MIM sur un contrôleur de domaine](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Planifier le déploiement et le support de SSPR

### <a name="engage-the-right-stakeholders"></a>Impliquer les parties prenantes appropriées

Lorsque des projets technologiques échouent, cela est généralement dû à des attentes qui ne correspondent pas à l’impact, aux résultats et aux responsabilités réels. Pour éviter ces pièges, veillez à impliquer les parties prenantes appropriées et à ce qu’elles comprennent bien leurs rôles dans le projet. Pour ce faire, dressez une liste de leurs contributions et de leurs responsabilités.

### <a name="communications-plan"></a>Plan de communication

La communication est essentielle à la réussite de tout nouveau service. Communiquez de façon proactive avec les utilisateurs sur l’utilisation du service et la manière d’obtenir de l’aide si quelque chose ne fonctionne pas comme prévu. Consultez les [documents sur le déploiement de la réinitialisation de mot de passe en libre-service dans le centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=56768). Vous y trouverez des suggestions pour planifier votre stratégie de communication avec les utilisateurs finaux.

### <a name="testing-plan"></a>Plan de test

Pour vous assurer que le déploiement fonctionne comme prévu, vous devez prévoir un ensemble de cas de test pour valider l’implémentation. Le tableau suivant inclut quelques scénarios de test utiles que vous pouvez utiliser pour documenter les résultats attendus par votre organisation en fonction de vos stratégies.

| Cas métier | Résultat attendu |
| --- | --- |
| Le portail SSPR est accessible à partir du réseau d’entreprise | Déterminé par votre organisation |
| Le portail SSPR est accessible en dehors du réseau d’entreprise | Déterminé par votre organisation |
| Réinitialiser le mot de passe utilisateur à partir du navigateur lorsque l’utilisateur n’est pas activé pour la réinitialisation de mot de passe | L’utilisateur n’est pas en mesure d’accéder aux flux de réinitialisation de mot de passe |
| Réinitialiser le mot de passe utilisateur à partir du navigateur lorsque l’utilisateur n’est pas inscrit pour la réinitialisation de mot de passe | L’utilisateur n’est pas en mesure d’accéder aux flux de réinitialisation de mot de passe |
| L’utilisateur se connecte lorsque l’inscription à la réinitialisation de mot de passe est appliquée | L’utilisateur est invité à inscrire ses informations de sécurité |
| L’utilisateur se connecte lorsque l’inscription à la réinitialisation de mot de passe est terminée | L’utilisateur n’est pas invité à inscrire ses informations de sécurité |
| Le portail SSPR est accessible lorsque l’utilisateur ne dispose pas d’une licence | Accessible |
| Réinitialiser le mot de passe utilisateur à partir de l’écran de verrouillage d’appareil Windows 10 Azure AD joint ou Azure AD hybride joint une fois que l’utilisateur est inscrit | L’utilisateur peut réinitialiser le mot de passe |
| Les données d’utilisation et d’inscription SSPR sont disponibles pour les administrateurs quasiment en temps réel | Disponible via les journaux d’audit |

### <a name="support-plan"></a>Plan de support

Bien que SSPR ne crée généralement pas de problèmes pour l’utilisateur, il est important d’avoir un personnel préparé à gérer les éventuels soucis.

Même si un administrateur peut modifier ou réinitialiser le mot de passe des utilisateurs finaux via le portail Azure AD, il est préférable de résoudre le problème via un processus de support en libre-service.

Dans la section du guide opérationnel de ce document, créez une liste de cas de support et de leurs causes probables, et indiquez comment résoudre ces problèmes.

### <a name="auditing-and-reporting"></a>Audit et création de rapports

Après le déploiement, de nombreuses organisations souhaitent savoir comment ou si la réinitialisation de mot de passe en libre-service (SSPR) est réellement utilisée. La fonctionnalité de création de rapports fournie par Azure Active Directory (Azure AD) vous aide à répondre aux questions à l’aide de rapports prédéfinis.

Les journaux d’audit pour l’inscription et la réinitialisation de mot de passe sont disponibles pendant 30 jours. Par conséquent, si l’audit de sécurité au sein d’une entreprise nécessite une rétention plus longue, les journaux doivent être exportés et utilisés dans un outil SIEM comme [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk ou ArcSight.

Dans un tableau, comme celui ci-dessous, documentez le calendrier de sauvegarde, le système et les parties responsables. Vous n’aurez peut-être pas besoin de sauvegardes séparées pour les audits et les rapports, mais vous devez disposer d’une sauvegarde distincte à partir de laquelle récupérer les données en cas de problème.

|   | Fréquence de téléchargement | Système cible | Partie responsable |
| --- | --- | --- | --- |
| Sauvegarde d’audit |   |   |   |
| Sauvegarde de rapport |   |   |   |
| Sauvegarde de récupération d'urgence |   |   |   |

## <a name="implementation"></a>Implémentation

L’implémentation s’effectue en trois étapes :

- Configurer les utilisateurs et les licences
- Configure Azure AD SSPR pour l’inscription et le libre-service
- Configurer Azure AD Connect pour la réécriture du mot de passe

### <a name="communicate-the-change"></a>Communiquer le changement

Commencez l’implémentation du plan de communication que vous avez développé durant la phase de planification.

### <a name="ensure-groups-are-created-and-populated"></a>Vérifier que les groupes sont créés et remplis

Consultez la section sur la planification des méthodes d'authentification par mot de passe. Veillez à ce que les groupes de l’implémentation pilote ou de production soient disponibles, et vérifiez que tous les utilisateurs appropriés sont ajoutés aux groupes.

### <a name="apply-licenses"></a>Appliquer les licences

Les groupes que vous vous apprêtez à implémenter doivent avoir la licence Premium Azure AD. Vous pouvez attribuer des licences directement au groupe ou utiliser des stratégies de licence existantes (par exemple, via PowerShell ou en fonction du groupe).

Vous trouverez plus d’informations sur l’attribution de licences à des groupes d’utilisateurs dans l’article [Affecter des licences aux utilisateurs par appartenance aux groupes dans Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Configurer SSPR

#### <a name="enable-groups-for-sspr"></a>Activer les groupes pour SSPR

1. Accédez au portail Azure avec un compte administrateur.
1. Sélectionnez Tous les services et, dans la zone de texte Filtre, saisissez Azure Active Directory, puis sélectionnez Azure Active Directory.
1. Dans le panneau Active Directory, sélectionnez Réinitialisation de mot de passe.
1. Dans le volet Propriétés, choisissez Sélectionné. Si vous souhaitez activer tous les utilisateurs, sélectionner Tout.
1. Dans le panneau Stratégie de réinitialisation de mot de passe par défaut, saisissez le nom du premier groupe, sélectionnez-le et cliquez sur Sélectionner en bas de l’écran, puis sélectionnez Enregistrer en haut de l’écran.
1. Répétez ce processus pour chaque groupe.

#### <a name="configure-the-authentication-methods"></a>Configurer les méthodes d’authentification

Consultez votre planification à la section Planification des méthodes de l’authentification par mot de passe de ce document.

1. Sélectionnez Inscription et, sous Demander à l’utilisateur de s’inscrire lors de la connexion, sélectionnez Oui. Ensuite, définissez le nombre de jours avant l’expiration, puis sélectionnez Enregistrer.
1. Sélectionnez Notification et configurez les paramètres selon votre plan, puis sélectionnez Enregistrer.
1. Sélectionnez Personnalisation et configurez les paramètres selon votre plan, puis sélectionnez Enregistrer.
1. Sélectionnez Intégration locale et configurez les paramètres selon votre plan, puis sélectionnez Enregistrer.

### <a name="enable-sspr-in-windows"></a>Activer SSPR sous Windows

Les appareils Windows 10 exécutant la version 1803 ou une version supérieure et qui sont joints à Azure AD ou joints à Azure AD hybride peuvent réinitialiser leurs mots de passe sur l’écran de connexion Windows. Vous trouverez les informations et les étapes nécessaires pour configurer cette fonctionnalité dans l’article [Réinitialisation du mot de passe Azure AD depuis l’écran de connexion](tutorial-sspr-windows.md).

### <a name="configure-password-writeback"></a>Configuration de l’écriture différée du mot de passe

Les étapes requises pour configurer l’écriture différée de mot de passe pour votre organisation sont disponibles dans l’article [Procédure : Configuration de l’écriture différée du mot de passe](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Gérer SSPR

Rôles requis pour gérer les fonctionnalités associées à la réinitialisation de mot de passe en libre-service.

| Rôle métier/personnage | Rôle Azure AD (si nécessaire) |
| :---: | :---: |
| Support technique de niveau 1 | Administrateur de mots de passe |
| Support technique de niveau 2 | Administrateur d’utilisateurs |
| Administrateur SSPR | Administrateur général |

### <a name="support-scenarios"></a>Ses scénarios de support

Pour garantir le succès de votre équipe de support technique, vous pouvez créer une FAQ en fonction des questions que vous recevez de la part des utilisateurs. Le tableau suivant contient des scénarios courants de support.

| Scénarios | Description |
| --- | --- |
| L’utilisateur ne dispose pas de méthodes d’authentification inscrites. | Un utilisateur essaie de se réinitialiser son mot de passe, mais aucune des méthodes d’authentification qu’il a inscrites n’est disponible (par exemple, il a laissé son téléphone portable à la maison et ne peut pas accéder à ses e-mails). |
| L’utilisateur ne reçoit pas un SMS ou un appel sur son téléphone mobile ou professionnel. | Un utilisateur tente de confirmer son identité par SMS ou via un appel, mais ne reçoit pas le SMS/l’appel. |
| L’utilisateur n’a pas accès au portail de réinitialisation de mot de passe. | Un utilisateur souhaite réinitialiser son mot de passe, mais n’est pas activé pour la réinitialisation de mot de passe. Par conséquent, il ne peut pas accéder à la page pour mettre à jour les mots de passe. |
| L’utilisateur ne peut pas définir un nouveau mot de passe. | Un utilisateur termine la vérification pendant le flux de réinitialisation de mot de passe, mais ne peut pas définir un nouveau mot de passe. |
| L’utilisateur ne voit pas de lien Réinitialiser le mot de passe sur un appareil Windows 10. | Un utilisateur tente de réinitialiser le mot de passe à partir de l’écran de verrouillage Windows 10, mais l’appareil n’est pas joint à Azure AD, ou la stratégie d’appareil Intune n’est pas activée. |

Vous pouvez également inclure les informations suivantes pour la résolution de problèmes supplémentaires.

- Groupes activés pour SSPR.
- Méthodes d’authentification configurées.
- Stratégies d’accès associées au réseau d’entreprise.
- Étapes de résolution des problèmes pour les scénarios courants.

Vous pouvez également consulter notre documentation en ligne sur la résolution des problèmes de mot de passe en libre-service pour connaître les étapes générales des scénarios SSPR courants.

## <a name="next-steps"></a>Étapes suivantes

- [Envisager d’implémenter la protection par mot de passe Azure AD](concept-password-ban-bad.md)

- [Envisager d’implémenter le verrouillage intelligent Azure AD](howto-password-smart-lockout.md)
