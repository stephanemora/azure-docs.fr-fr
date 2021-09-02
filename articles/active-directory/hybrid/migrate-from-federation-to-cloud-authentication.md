---
title: Migrer de la fédération à l’authentification cloud dans Azure Active Directory
description: Cet article contient des informations sur le déplacement d’un environnement d’identité hybride de la fédération à l’authentification cloud.
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.date: 07/08/2021
ms.author: baselden
author: BarbaraSelden
manager: MartinCo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd25928e245e6561be53ace29b017f476da7f1f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122533108"
---
# <a name="migrate-from-federation-to-cloud-authentication"></a>Passer de la fédération à l’authentification cloud 

Dans cet article, vous apprendrez à déployer l’authentification des utilisateurs dans le cloud avec la [Synchronisation du hachage des mots de passe (PHS)](whatis-phs.md) ou l’[Authentification directe (PTA)](how-to-connect-pta.md) Azure Active Directory. Bien que nous présentions le cas d’utilisation pour le passage d’[Active Directory Federation Services (AD FS)](whatis-fed.md) à des méthodes d’authentification cloud, ces conseils s’appliquent également à d’autres systèmes locaux.

Avant de poursuivre, nous vous suggérons de consulter notre guide sur le [Choix de la méthode d’authentification appropriée](choose-ad-authn.md) et de comparer les méthodes les plus adaptées à votre organisation.

Nous vous recommandons d’utiliser PHS pour l’authentification cloud.

## <a name="staged-rollout"></a>Déploiement par étapes

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]

Le lancement intermédiaire est un excellent moyen de tester des groupes d’utilisateurs de manière sélective avec des fonctionnalités d’authentification cloud comme Azure AD Multi-Factor Authentication (MFA), l’accès conditionnel, Identity Protection pour les informations d’identification divulguées ou Identity Governance avant le basculement de vos domaines. 

Reportez-vous au plan de mise en œuvre du déploiement par étapes pour comprendre les scénarios [pris en charge](how-to-connect-staged-rollout.md#supported-scenarios) et [non pris en charge](how-to-connect-staged-rollout.md#unsupported-scenarios). Nous vous recommandons d’utiliser le déploiement par étapes pour effectuer des tests avant de découper les domaines.

Pour savoir comment configurer le déploiement par étapes, consultez le [Guide interactif du déploiement par étapes](https://mslearn.cloudguides.com/guides/Test%20migration%20to%20cloud%20authentication%20using%20staged%20rollout%20in%20Azure%20AD) (migration vers l’authentification cloud à l’aide du déploiement par étapes dans Azure AD).

## <a name="migration-process-flow"></a>Flux du processus de migration

![Processus de migration vers l’authentification cloud](media/deploy-cloud-user-authentication/process-flow-migration.png)

## <a name="prerequisites"></a>Configuration requise

Avant de commencer votre migration, assurez-vous que vous respectez ces conditions préalables.

### <a name="required-roles"></a>Rôles nécessaires

Pour utiliser le lancement intermédiaire, vous devez être l’administrateur général de votre locataire. 

Pour activer l’authentification unique transparente sur une forêt Windows Active Directory particulière, vous devez être l’administrateur de domaine.

### <a name="step-up-azure-ad-connect-server"></a>Passer à la version supérieure du serveur Azure AD Connect

Installez [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) ou [mettez-le à niveau vers la dernière version](how-to-upgrade-previous-version.md). Lorsque vous passez un serveur Azure AD Connect à une édition supérieure, vous réduisez le temps nécessaire à la migration d’AD FS vers les méthodes d’authentification cloud de plusieurs heures à quelques minutes. 

### <a name="document-current-federation-settings"></a>Documenter les paramètres de fédération actuels

Pour trouver le paramètre de fédération actuel, exécutez l’applet de commande [Get-MsolDomainFederationSettings](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login). 

Vérifiez les paramètres qui peuvent avoir été personnalisés pour la conception de votre fédération et la documentation du déploiement. En particulier, recherchez les personnalisations dans **PreferredAuthenticationProtocol**, **SupportsMfa** et **PromptLoginBehavior**.

### <a name="back-up-federation-settings"></a>Sauvegarder les paramètres de fédération

Bien que ce déploiement ne modifie aucune autre partie de confiance dans votre batterie AD FS, vous pouvez sauvegarder vos paramètres :

 - Utilisez l’[outil de restauration rapide AD FS](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) de Microsoft pour restaurer une batterie existante ou en créer une nouvelle.

- Exportez l’approbation de la partie de confiance de la plateforme d’identité Microsoft 365 et les règles de revendication personnalisées associées que vous avez ajoutées à l’aide de l’exemple PowerShell suivant :

    ```powershell

    (Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"

    ```

## <a name="plan-the-project"></a>Planifier le projet

Les échecs de projets informatiques, lorsqu’ils se produisent, proviennent généralement d’une disparité entre les attentes et l’impact, les responsabilités et les résultats. Pour éviter ces écueils, [assurez-vous de faire appel aux bonnes personnes](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders), et que les rôles des parties prenantes soient bien compris.

### <a name="plan-communications"></a>Planifier les communications

Après la migration vers l’authentification cloud, l’expérience de connexion de l’utilisateur pour accéder à Microsoft 365 et à d’autres ressources qui sont authentifiées par Azure AD change. Les utilisateurs qui sont en dehors du réseau voient seulement la page de connexion Azure AD. 

Communiquez de manière proactive avec vos utilisateurs sur ce qui va changer, à quel moment les changements seront appliqués et comment ils peuvent obtenir de l’aide en cas de problème.

### <a name="plan-the-maintenance-window"></a>Planifier la fenêtre de maintenance

Après la conversion de domaine, Azure AD peut continuer à envoyer des requêtes d’authentification héritées d’Exchange Online à vos serveurs AD FS pendant une période maximale de quatre heures. Le retard est dû au fait que le cache d’Exchange Online pour l’[authentification des applications héritées](../fundamentals/concept-fundamentals-block-legacy-authentication.md) peut prendre jusqu’à 4 heures pour réaliser le passage de la fédération à l’authentification cloud.

Pendant cette période de quatre heures, vous pouvez inviter les utilisateurs à fournir des informations d’identification à plusieurs reprises lors de la réauthentification à des applications qui utilisent l’authentification héritée. Bien que l’utilisateur puisse toujours s’authentifier auprès d’AD FS, Azure AD n’accepte plus le jeton émis de l’utilisateur, car cette approbation de la fédération est maintenant supprimée.

Les clients hérités (Exchange ActiveSync, Outlook 2010/2013) ne sont pas affectés, car Exchange Online conserve un cache de leurs informations d’identification pour un laps de temps défini. Le cache est utilisé pour réauthentifier l’utilisateur en mode silencieux. L’utilisateur ne doit pas retourner à AD FS. Les informations d’identification stockées sur l’appareil pour ces clients sont utilisées pour se réauthentifier eux-mêmes en mode silencieux une fois le cache effacé. Les utilisateurs ne doivent normalement pas recevoir d’invites demandant un mot de passe à la suite du processus de conversion de domaine.

Les clients d’authentification moderne (Office 2016 et Office 2013, applications iOS et Android) utilisent un jeton d’actualisation valide pour obtenir de nouveaux jetons d’accès permettant de continuer à accéder aux ressources au lieu de retourner à AD FS. Ces clients sont protégés des demandes de mot de passe résultant du processus de conversion de domaine. Les clients continuent de fonctionner sans configuration supplémentaire.

### <a name="plan-for-rollback"></a>Plan de restauration

> [!TIP]
> Envisagez de planifier le basculement des domaines pendant les heures creuses en cas d’exigences de restauration. 

Pour planifier la restauration, utilisez les [paramètres actuels documentés de la fédération](#document-current-federation-settings) et vérifiez la [documentation sur la conception et le déploiement de la fédération](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide). 

Le processus de restauration comprend la conversion des domaines managés en domaines fédérés avec l’applet de commande [Convert-MSOLDomainToFederated](/powershell/module/msonline/convert-msoldomaintofederated). Si nécessaire, configurez des règles de revendications supplémentaires.

## <a name="migration-considerations"></a>Considérations relatives à la migration 

Voici les principaux éléments à prendre en compte lors de la migration.

### <a name="plan-for-customizations-settings"></a>Planifier les paramètres de personnalisation 

Le fichier onload.js ne peut pas être dupliqué dans Azure AD. Si votre instance AD FS est fortement personnalisée et s’appuie sur des paramètres de personnalisation spécifiques dans le fichier onload.js, vérifiez si Azure AD peut répondre à vos besoins actuels en matière de personnalisation et planifiez en conséquence. Communiquez ces modifications à venir à vos utilisateurs.

#### <a name="sign-in-experience"></a>Expérience de connexion

Vous ne pouvez pas personnaliser l’expérience de connexion Azure AD. Quelle que soit la façon dont vos utilisateurs se sont connectés précédemment, vous avez besoin d’un nom de domaine complet, comme un nom d’utilisateur principal (UPN) ou un e-mail pour vous connecter à Azure AD. 

#### <a name="organization-branding"></a>Personnalisation de l’organisation

Vous pouvez [personnaliser la page de connexion Azure AD](../fundamentals/customize-branding.md). Certaines modifications visuelles sur les pages de connexion d’AD FS doivent être attendues après la conversion. 

>[!NOTE] 
>La personnalisation de l’organisation n’est pas disponible dans les licences Azure AD gratuites, sauf si vous disposez d’une licence Microsoft 365.

### <a name="plan-for-conditional-access-policies"></a>Planifier les stratégies d’accès conditionnel

Évaluez si vous utilisez actuellement l’accès conditionnel pour l’authentification, ou si vous utilisez des stratégies de contrôle d’accès dans AD FS. 

Vous pouvez envisager de remplacer les stratégies de contrôle d’accès AD FS par des [stratégies d’accès conditionnel](../conditional-access/overview.md) et des [règles d’accès du client Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules) d’Azure AD équivalentes. Vous pouvez utiliser des groupes Azure AD ou locaux pour l’accès conditionnel.

**Désactiver l’authentification héritée** - En raison du risque accru associé aux protocoles d’authentification traditionnels, créez une [stratégie d’accès conditionnel pour bloquer l’authentification traditionnelle](../conditional-access/howto-conditional-access-policy-block-legacy.md).

### <a name="plan-support-for-mfa"></a>Planifier la prise en charge de l’authentification multifacteur

Chaque domaine fédéré dans Azure AD a un indicateur SupportsMFA. 

**Si l’indicateur SupportsMFA a la valeur True**, Azure AD redirige les utilisateurs vers AD FS ou d’autres fournisseurs de fédération pour qu’ils effectuent l’authentification multifacteur. Par exemple, si un utilisateur accède à une application pour laquelle une stratégie d’accès conditionnel nécessitant l’authentification multifacteur a été configurée, l’utilisateur est redirigé vers AD FS. L’ajout d’Azure AD MFA comme méthode d’authentification dans AD FS permet d’appeler Azure AD MFA une fois vos configurations terminées.

**Si l’indicateur SupportsMFA est défini sur False**, il est probable que vous n’utilisez pas l’authentification multifacteur Azure ; vous utilisez probablement des règles de réclamation sur des parties de confiance d’AD FS pour déclencher l’authentification multifacteur. 

Vous pouvez vérifier l’état de votre indicateur **SupportsMFA** avec la cmdlet Windows PowerShell suivante :
```powershell
 Get-MsolDomainFederationSettings –DomainName yourdomain.com
 ```

>[!NOTE] 
>Le serveur Microsoft MFA est proche de la fin de sa durée de vie du support et, si vous l’utilisez, vous devez passer à Azure AD MFA. Pour plus d’informations, consultez la documentation **[Migrer de Microsoft MFA Server vers Azure Multi-factor Authentication](../authentication/how-to-migrate-mfa-server-to-azure-mfa.md)** .
Si vous envisagez d’utiliser Azure AD Multi-Factor Authentication, nous vous recommandons une **[inscription combinée à la réinitialisation de mot de passe en libre-service (SSPR) et à l’authentification multifacteur](../authentication/concept-registration-mfa-sspr-combined.md)** pour permettre à vos utilisateurs d’inscrire leurs méthodes d’authentification en une seule fois. 

## <a name="plan-for-implementation"></a>Planifier l’implémentation

Cette section comprend le pré-travail avant que vous basculiez votre méthode de connexion et convertissiez les domaines.

### <a name="create-necessary-groups-for-staged-rollout"></a>Créer les groupes nécessaires pour le déploiement par étapes

*Si vous n’utilisez pas de déploiement intermédiaire, ignorez cette étape.*

Créer des groupes pour le déploiement par étapes. Vous devrez également créer des groupes pour les stratégies d’accès conditionnel si vous décidez de les ajouter.

Nous vous recommandons d’utiliser un groupe maître dans Azure AD, également appelé groupe cloud uniquement. Vous pouvez utiliser des groupes de sécurité Azure AD ou des groupes Microsoft 365 pour déplacer les utilisateurs vers l’authentification multifacteur et pour les stratégies d’accès conditionnel. Pour plus d’informations, consultez [Création d’un groupe de sécurité Azure AD](../fundamentals/active-directory-groups-create-azure-portal.md) et [Vue d’ensemble des groupes Microsoft 365 pour les administrateurs](/microsoft-365/admin/create-groups/office-365-groups).

Les membres d’un groupe sont automatiquement activés pour le lancement intermédiaire. Les groupes dynamiques et imbriqués ne sont pas pris en charge pour le lancement intermédiaire.

### <a name="pre-work-for-sso"></a>Pré-travail pour l’authentification unique

La version d’authentification unique que vous utilisez dépend du système d’exploitation de votre appareil et de l’état de jointure.

- **Pour Windows 10, Windows Server 2016 et versions ultérieures**, il est recommandé d’utiliser l’authentification unique via le [jeton d’actualisation principal (PRT)](../devices/concept-primary-refresh-token.md) avec les [appareils joints Azure AD](../devices/concept-azure-ad-join.md), les [appareils joints Azure AD hybrides](../devices/concept-azure-ad-join-hybrid.md) ou les [appareils inscrits dans Azure AD](../devices/concept-azure-ad-register.md). 

- **Pour les appareils Windows 7 et 8.1**, nous vous recommandons d’utiliser [l’authentification unique fluide](how-to-connect-sso.md) avec jointure de domaine pour enregistrer l’ordinateur dans Azure AD. Vous n’avez pas à synchroniser ces comptes comme pour les appareils Windows 10. Cependant, vous devez effectuer ce [travail préalable pour une authentification unique fluide avec PowerShell](how-to-connect-staged-rollout.md#pre-work-for-seamless-sso).

### <a name="pre-work-for-phs-and-pta"></a>Pré-travail pour PHS et PTA

Selon le choix de la méthode d’inscription, effectuez le [travail préalable pour PHS](how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync) ou le [pour PTA](how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication).

## <a name="implement-your-solution"></a>Implémenter votre solution

Enfin, vous devez basculer la méthode de connexion sur PHS ou PTA, comme prévu, et convertir les domaines de la fédération en authentification cloud. 

### <a name="using-staged-rollout"></a>Utilisation du déploiement par étapes

Si vous utilisez le déploiement échelonné, suivez les étapes indiquées dans les liens ci-dessous :

1. [Activez le lancement intermédiaire d’une fonctionnalité spécifique sur votre locataire.](how-to-connect-staged-rollout.md#enable-staged-rollout)

2. Une fois les tests terminés, [convertissez les domaines de fédérés en managés](#convert-domains-from-federated-to-managed).

### <a name="without-using-staged-rollout"></a>Sans déploiement par étapes 

Vous avez deux options pour permettre ce changement :

- **Option A :** Basculer à l’aide d’Azure AD Connect
  
  *Disponible si vous avez initialement configuré votre environnement AD FS/fédéré par ping avec Azure AD Connect*.

- **Option B :** Basculer en utilisant Azure AD Connect et PowerShell
 
  *Disponible si vous n’avez pas initialement configuré vos domaines fédérés à l’aide d’Azure AD Connect ou si vous utilisez des services de fédération tiers*.

Pour choisir l’une de ces options, vous devez connaître vos paramètres actuels.

#### <a name="verify-current-azure-ad-connect-settings"></a>Vérifier les paramètres d’Azure AD Connect actuels

Connectez-vous au [portail Azure AD](https://aad.portal.azure.com/), sélectionnez **Azure AD Connect** et vérifiez les paramètres **USER SIGN_IN** comme indiqué dans ce diagramme :

![Vérifier les paramètres d’Azure AD Connect actuels](media/deploy-cloud-user-authentication/current-user-settings-on-azure-ad-portal.png)


**Pour vérifier la configuration de la fédération :**

1. Sur votre serveur Azure AD Connect, ouvrez **Azure AD Connect** et sélectionnez **Configurer**.

2. Sous **Tâches supplémentaires > Gérer la fédération**, sélectionnez **Voir la configuration de la fédération**. 

    ![Voir la gestion de la fédération](media/deploy-cloud-user-authentication/manage-federation.png)

    Si la configuration d’AD FS apparaît dans cette section, vous pouvez considérer qu’AD FS a été initialement configuré à l’aide d’Azure AD Connect. Consultez l’image ci-dessous comme exemple :

    ![Voir la configuration d’ADFS](media/deploy-cloud-user-authentication/federation-configuration.png)

    Si AD FS n’est pas listé dans les paramètres actuels, vous devez convertir manuellement vos domaines de l’identité fédérée à l’identité managée avec PowerShell.

#### <a name="option-a"></a>Option A

**Passage de la fédération à la nouvelle méthode de connexion à l’aide d’Azure AD Connect**

1. Sur votre serveur Azure AD Connect, ouvrez **Azure AD Connect** et sélectionnez **Configurer**.

2. Sous la page **Tâches supplémentaires**, sélectionnez **Changer de connexion utilisateur**, puis sélectionnez **Suivant**.
   
    ![Voir les tâches supplémentaires](media/deploy-cloud-user-authentication/additional-tasks.png)

3. Sur la page **Connexion à Azure AD**, saisissez vos informations d’identification d’administrateur global.

4. Sur la page **Connexion de l’utilisateur** :

    - Si vous sélectionnez le bouton d’option **Authentification directe**, cochez **Activer l’authentification unique**, puis sélectionnez **Suivant**.

    -  Si vous sélectionnez le bouton d’option **Synchronisation de hachage de mot de passe**, assurez-vous de cocher la case **Ne pas convertir les comptes d’utilisateur**. L’option est dépréciée. Cochez **Activer l’authentification unique**, puis sélectionnez **Suivant**.

      ![Cochez la case Activer l’authentification unique sur la page de connexion de l’utilisateur](media/deploy-cloud-user-authentication/user-sign-in.png)

5. Dans la page **Activer l’authentification unique**, entrez les informations d’identification d’un compte d’administrateur de domaine, puis sélectionnez **Suivant**.

    ![Activer l’ouverture de session unique](media/deploy-cloud-user-authentication/enable-single-sign-on.png)

    Les informations d’identification du compte d’administrateur de domaine sont nécessaires pour activer l’authentification unique fluide. Le processus effectue les actions suivantes, qui nécessitent ces autorisations élevées :
      - Un compte d’ordinateur nommé AZUREADSSO (qui représente Azure AD) est créé dans votre instance Active Directory locale.
      - La clé de déchiffrement Kerberos du compte d’ordinateur est partagée de façon sécurisée avec Azure AD.
      - Deux noms de principal du service Kerberos sont créés pour représenter les deux URL utilisées lors de la connexion à Azure AD.

    Les informations d’identification d’administrateur de domaine ne sont pas stockées dans Azure AD Connect ou Azure AD et sont ignorées une fois le processus terminé. Elles sont utilisées pour activer cette fonctionnalité.

6. Dans la page **Prêt à configurer**, vérifiez que la case **Démarrez le processus de synchronisation une fois la configuration terminée** est cochée. Ensuite, sélectionnez **Configurer**.

    ![Page « Prêt à configurer »](media/deploy-cloud-user-authentication/ready-to-configure.png)

 > [!IMPORTANT] 
 > À ce stade, tous vos domaines fédérés passent à l’authentification managée. La méthode de connexion de l’utilisateur sélectionné est la nouvelle méthode d’authentification.

1. Dans le Portail Azure AD, sélectionnez **Azure Active Directory**, puis **Azure AD Connect**.

2. Vérifiez ces paramètres :

      - **Fédération** est défini sur **Désactivé**.
      - **Authentification unique fluide** est défini sur **Activé**.
      - **Synchronisation de hachage de mot de passe** est défini sur **Activé**.

  ![ Revérifier les paramètres utilisateur actuels](media/deploy-cloud-user-authentication/reverify-settings.png)

3. Si vous passez à PTA, effectuez les étapes suivantes.

##### <a name="deploy-more-authentication-agents-for-pta"></a>Déployer davantage d’agents d’authentification pour PTA

>[!NOTE]
> Pour bénéficier de l’authentification directe, vous devez déployer des agents légers sur le serveur Azure AD Connect et sur vos serveurs locaux qui exécutent Windows Server. Pour réduire la latence, installez les agents aussi près que possible de vos contrôleurs de domaine Active Directory.

Pour la plupart des clients, deux ou trois agents d’authentification suffisent à offrir la haute disponibilité et la capacité nécessaire. Un locataire peut avoir un maximum de 12 agents inscrits. Le premier agent est toujours installé sur le serveur Azure AD Connect lui-même. Pour en savoir plus sur les limitations de l’agent et sur les options de déploiement des agents, consultez [Authentification directe Azure AD : Limitations actuelles](how-to-connect-pta-current-limitations.md).

1. Sélectionnez **Authentification directe**.
2. Dans la page **Authentification directe**, sélectionnez le bouton **Télécharger**.
3. Dans la page **Télécharger l’agent**, sélectionnez **Accepter les conditions et télécharger**.

    Le téléchargement des agents d’authentification supplémentaires démarre. Installez l’agent d’authentification secondaire sur un serveur joint à un domaine.

4. Exécutez l’installation de l’agent d’authentification. Lors de l’installation, vous devez fournir les informations d’identification d’un compte d’administrateur général.

    ![ Agent d’authentification Microsoft Azure AD Connect](media/deploy-cloud-user-authentication/install-azure-ad-connect-installation-agent.png)

5. Lorsque l’agent d’authentification est installé, vous pouvez revenir à la page d’intégrité de PTA pour vérifier l’état des autres agents.

#### <a name="option-b"></a>Option B

**Passage de la fédération à la nouvelle méthode de connexion à l’aide d’Azure AD Connect et de PowerShell**

*Disponible si vous n’avez pas initialement configuré vos domaines fédérés à l’aide d’Azure AD Connect ou si vous utilisez des services de fédération tiers.*

Sur votre serveur Azure AD Connect, suivez les étapes 1 à 5 de l’[Option A](#option-a). Vous remarquerez que sur la page de connexion de l’utilisateur, l’option **Ne pas configurer** est présélectionnée.

![ Consultez l’option Ne pas configurer sur la page de connexion utilisateur.](media/deploy-cloud-user-authentication/do-not-configure-on-user-sign-in-page.png)

1. Dans le Portail Azure AD, sélectionnez **Azure Active Directory**, puis **Azure AD Connect**. 

2. Vérifiez ces paramètres :

  - **Fédération** est défini sur **Activé**.
  - **Authentification unique fluide** est défini sur **Désactivé**.
  - **Synchronisation de hachage de mot de passe** est défini sur **Activé**.

    ![ Vérifier les paramètres utilisateur actuels sur le portail Azure](media/deploy-cloud-user-authentication/verify-current-user-settings-on-azure-ad-portal.png)

**Dans le cas de PTA uniquement**, suivez ces étapes pour installer d’autres serveurs agents PTA.

1. Dans le Portail Azure AD, sélectionnez **Azure Active Directory**, puis **Azure AD Connect**.

2. Sélectionnez **Authentification directe**. Vérifiez que l’état est **Actif**.

    ![ Paramètres d’authentification directe](media/deploy-cloud-user-authentication/pass-through-authentication-settings.png)

   Si l’agent d’authentification n’est pas actif, suivez ces [étapes de dépannage](tshoot-connect-pass-through-authentication.md) avant de passer au processus de conversion du domaine de l’étape suivante. Si vous convertissez vos domaines avant de vérifier que vos agents d’authentification directe ont bien été installés et qu’ils sont à l’état **Actif** dans le portail Azure, vous risquez de provoquer une interruption de l’authentification.

3. [Déployez davantage d’agents d’authentification](#deploy-more-authentication-agents-for-pta).

### <a name="convert-domains-from-federated-to-managed"></a>Convertir les domaines fédérés en domaines managés

**À ce stade, l’authentification fédérée est toujours active et opérationnelle pour vos domaines**. Pour poursuivre le déploiement, vous devez convertir chaque domaine de l’identité fédérée en identité managée.

>[!IMPORTANT]
> Vous n’êtes pas obligé de convertir tous les domaines en même temps. Vous pouvez choisir de démarrer avec un domaine de test sur votre locataire de production ou de démarrer avec votre domaine qui a le plus petit nombre d’utilisateurs.

**Effectuez la conversion en utilisant le module PowerShell Azure AD :**

1. Dans PowerShell, connectez-vous au portail Azure en utilisant un compte d’administrateur général.

2. Pour convertir le premier domaine, exécutez la commande suivante :
   ```powershell
    Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
    ```
    Consultez [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

3. Dans le portail Azure AD, sélectionnez **Azure Active Directory > Azure AD Connect**.

4. Vérifiez que le domaine a été converti en domaine managé en exécutant la commande suivante :
    ```powershell
    Get-MsolDomain -DomainName <domain name>
    ```
## <a name="complete-your-migration"></a>Terminer votre migration

Effectuez les tâches suivantes pour vérifier la méthode d’inscription et terminer le processus de conversion.

### <a name="test-the-new-sign-in-method"></a>Tester la nouvelle méthode de connexion

Quand votre locataire utilisait l’identité fédérée, les utilisateurs étaient redirigés de la page de connexion d’Azure AD vers votre environnement AD FS. Maintenant que le locataire est configuré pour utiliser la nouvelle méthode de connexion au lieu de l’authentification fédérée, les utilisateurs ne sont pas redirigés vers AD FS. 

**Au lieu de cela, ils se connectent directement sur la page de connexion d’Azure AD.**

Suivez les étapes de ce lien : [Valider la connexion avec PHS/PTA et l’authentification unique fluide](how-to-connect-staged-rollout.md#validation) (si nécessaire)

### <a name="remove-a-user-from-staged-rollout"></a>Supprimer un utilisateur du lancement intermédiaire

Si vous avez utilisé le déploiement par étapes, n’oubliez pas de désactiver les fonctionnalités de lancement intermédiaire une fois que vous avez terminé le basculement. 

**Pour désactiver la fonctionnalité de déploiement intermédiaire, assurez-vous que le contrôle est sur Désactiver.**

### <a name="sync-userprincipalname-updates"></a>Synchroniser les mises à jour de userPrincipalName

Historiquement, les mises à jour de l’attribut **UserPrincipalName**, qui utilise le service de synchronisation à partir de l’environnement local, sont bloquées sauf si les deux conditions suivantes sont remplies :

   - L’utilisateur est dans un domaine d’identité (non fédérée) managée.
   - Aucune licence n’a été affectée à l’utilisateur.

Pour savoir comment vérifier ou activer cette fonctionnalité, consultez [Synchroniser les mises à jour de userPrincipalName](how-to-connect-syncservice-features.md).

## <a name="manage-your-implementation"></a>Gérer l’implémentation

### <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Substituer la clé de déchiffrement Kerberos pour l’authentification unique fluide

Nous vous recommandons de changer la clé de déchiffrement Kerberos au moins tous les 30 jours, pour vous aligner sur la façon dont les membres du domaine Active Directory soumettent des changements de mot de passe. Comme aucun appareil n’est associé à l’objet de compte d’ordinateur AZUREADSSO, vous devez effectuer ce changement de clé manuellement.

Voir la FAQ : [Comment puis-je substituer la clé de déchiffrement Kerberos du compte d’ordinateur AZUREADSSO ?](how-to-connect-sso.md).

### <a name="monitoring-and-logging"></a>Surveillance et journalisation

Surveillez les serveurs qui exécutent les agents d’authentification pour conserver la disponibilité de la solution. En plus des compteurs de performance généraux du serveur, les agents d’authentification exposent des objets de performance qui peuvent être utilisés pour comprendre les erreurs et les statistiques associées à l’authentification.

Les agents d’authentification consignent les opérations dans les journaux des événements Windows sous Application and Service Logs. Vous pouvez également activer la journalisation pour le dépannage.

Pour confirmer les différentes actions effectuées lors du déploiement par étapes, vous pouvez [Vérifier les événements pour PHS, PTA ou authentification unique fluide](how-to-connect-staged-rollout.md#auditing).

### <a name="troubleshoot"></a>Dépanner

Votre équipe de support doit comprendre comment résoudre les problèmes d’authentification qui surviennent pendant ou après le passage de l’authentification fédérée à l’authentification managée. Utilisez la documentation de dépannage suivante pour aider votre équipe de support à se familiariser avec les procédures de dépannage courantes, et les mesures à prendre pour isoler et résoudre le problème.

-  [Azure AD PHS](tshoot-connect-password-hash-synchronization.md)
- [Azure AD PTA](tshoot-connect-pass-through-authentication.md)
- [Authentification unique fluide Azure AD](tshoot-connect-sso.md)

## <a name="decommission-ad-fs-infrastructure"></a>Désactiver l’infrastructure AD FS

### <a name="migrate-app-authentication-from-ad-fs-to-azure-ad"></a>Migrer l’authentification des applications d’AD FS vers Azure AD

La migration exige l’évaluation de la configuration locale de l’application, puis le mappage de cette configuration dans Azure AD.

Si vous envisagez de continuer à utiliser des applications AD FS avec des applications sur site et SaaS à l’aide de SAML/WS-Fed ou du protocole OAuth, vous utiliserez à la fois AD ​​FS et Azure AD après avoir converti les domaines pour l’authentification de l’utilisateur. Dans ce cas, vous pouvez protéger vos applications et ressources locales avec Secure Hybrid Access (SHA) via le [Proxy d’application Azure AD ](../app-proxy/what-is-application-proxy.md) ou l’une des [intégrations partenaires d’Azure AD](../manage-apps/secure-hybrid-access.md). À l’aide du Proxy d’application ou d’un de nos partenaires, vous pouvez fournir un accès à distance sécurisé à vos applications locales. Les utilisateurs bénéficient d’une connexion facile à leurs applications à partir de n’importe quel appareil après une [authentification unique](../manage-apps/add-application-portal-setup-sso.md).

Vous pouvez déplacer les applications SaaS actuellement fédérées avec ADFS sur Azure AD. Reconfigurez pour vous authentifier auprès d’Azure AD via un connecteur intégré de notre [galerie d’applications Azure App](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), ou en [inscrivant l’application dans Azure AD](../develop/quickstart-register-app.md).

Pour plus d’informations, consultez – 

- [Déplacement de l’authentification d’application des services de fédération Active Directory (AD FS) vers Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md) et
- [Playbook de migration d’application AD FS vers Azure AD pour les développeurs](/samples/azure-samples/ms-identity-adfs-to-aad/ms-identity-dotnet-adfs-to-aad)

### <a name="remove-relying-party-trust"></a>Supprimer une approbation de partie de confiance

Si vous disposez d’Azure AD Connect Health, vous pouvez [surveiller l’utilisation](how-to-connect-health-adfs.md) depuis le portail Azure. Si l’utilisation ne montre aucune nouvelle requête d’authentification et après avoir vérifié le bon fonctionnement de l’authentification de tous les utilisateurs et clients via Azure AD, vous pouvez supprimer sans danger l’approbation de partie de confiance de Microsoft 365.

Si vous n’utilisez pas AD FS à d’autres fins (c’est-à-dire pour d’autres approbations de partie de confiance), vous pouvez mettre AD FS hors service sans problème.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur la migration d’applications](../manage-apps/migration-resources.md)
- [Déployer d’autres fonctionnalités d’identité](../fundamentals/active-directory-deployment-plans.md)