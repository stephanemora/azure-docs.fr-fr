---
title: Déployer la protection par mot de passe Azure AD - Azure Active Directory
description: Déployer la protection par mot de passe Azure AD pour interdire les mots de passe incorrects localement
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f61ab87a3eb1bd4b81a8e67a182a4cb6a09aa069
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888955"
---
# <a name="deploy-azure-ad-password-protection"></a>Déployer la protection par mot de passe d’Azure AD

Maintenant que vous comprenez [comment appliquer la protection par mot de passe Azure AD pour Windows Server Active Directory](concept-password-ban-bad-on-premises.md), l’étape suivante consiste à planifier et à exécuter votre déploiement.

## <a name="deployment-strategy"></a>Stratégie de déploiement

Nous vous recommandons de commencer les déploiements en mode audit. Le mode audit est la configuration initiale par défaut où il est possible de continuer à définir des mots de passe. Les mots de passe qui seraient bloqués sont enregistrés dans le journal des événements. Après avoir déployé les serveurs proxy et les agents du contrôleur de domaine en mode audit, vous devez surveiller l’impact qu’aura la stratégie de mot de passe sur les utilisateurs et l’environnement lorsque la stratégie sera appliquée.

Durant la phase d’audit, de nombreuses organisations constatent les points suivants :

* Elles doivent améliorer les processus opérationnels existants pour utiliser des mots de passe plus sécurisés.
* Les utilisateurs utilisent souvent des mots de passe non sécurisés.
* Elles doivent informer les utilisateurs des modifications de sécurisation à venir, de l’impact que cela peut avoir sur eux et de la façon de choisir des mots de passe plus sécurisés.

Il est également possible que la validation de mot de passe renforcée affecte l’automatisation de votre déploiement de contrôleur de domaine Active Directory existante. Nous vous recommandons d’effectuer au moins une promotion DC et une rétrogradation DC au cours de l’évaluation de la période d’audit afin d’aider à dévoiler ces problèmes à l’avance.  Pour plus d'informations, consultez les pages suivantes :

* [Ntdsutil.exe ne peut pas définir un mot de passe de mode de réparation des services d’annuaire faible](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [La promotion du réplica du contrôleur de domaine échoue en raison d’un mot de passe de mode de réparation des services d’annuaire faible](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [La rétrogradation du contrôleur de domaine échoue en raison d’un mot de passe administrateur local faible](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Une fois que la fonctionnalité a été exécutée en mode audit pendant une période raisonnable, vous pouvez basculer la configuration de *Audit* à *Appliquer* pour exiger des mots de passe plus sécurisés. Durant cette période, effectuer un monitoring strict est une bonne idée.

## <a name="deployment-requirements"></a>Composants requis pour le déploiement

* Les exigences en termes de licence pour la protection des mots de passe Azure AD sont fournies dans l’article [Éliminer les mots de passe incorrects de votre organisation](concept-password-ban-bad.md#license-requirements).
* Tous les ordinateurs sur lesquels le logiciel agent de contrôleur de domaine de protection par mot de passe Azure AD seront installés doivent exécuter Windows Server 2012 ou une version ultérieure. Cette exigence n’implique pas que le domaine ou la forêt Active Directory doive également être au niveau fonctionnel du domaine ou de la forêt Windows Server 2012. Comme mentionné dans [Principes de conception](concept-password-ban-bad-on-premises.md#design-principles), aucun niveau fonctionnel de domaine (DFL) ou de forêt (FFL) minimal n’est requis pour le logiciel de l’agent DC ni le logiciel de proxy à exécuter.
* Toutes les machines sur lesquelles le service d’agent de contrôleur de domaine est installé doivent avoir .NET 4.5 installé.
* Tous les ordinateurs sur lesquels le service proxy de protection par mot de passe Azure AD sera installé doivent exécuter Windows Server 2012 R2 ou ultérieur.
   > [!NOTE]
   > Le déploiement du service proxy est une condition obligatoire pour le déploiement de la protection de mot de passe Azure AD même si le contrôleur de domaine peut avoir une connectivité internet directe sortante. 
   >
* Toutes les machines sur lesquelles le service proxy de protection par mot de passe Azure AD sera installé doivent disposer de .NET 4.7.
  .NET 4.7 doit déjà être installé sur les instances de Windows Server entièrement mises à jour. Si nécessaire, téléchargez et exécutez le programme d’installation disponible sur la page [Programme d’installation hors connexion de .NET Framework 4.7 pour Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Le runtime C universel doit être installé sur toutes les machines, y compris les contrôleurs de domaine, sur lesquelles les composants de protection par mot de passe Azure AD sont installés. Vous pouvez obtenir le runtime en vous assurant que vous disposez de toutes les mises à jour à partir de Windows Update. Ou vous pouvez l’obtenir dans un package de mise à jour spécifique au système d’exploitation. Pour plus d’informations, consultez [Mise à jour du runtime C universel sous Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Une connectivité réseau doit exister entre au moins un contrôleur de domaine dans chaque domaine et au moins un serveur hébergeant le service proxy de protection par mot de passe. Cette connectivité doit autoriser le contrôleur de domaine à accéder au port 135 du mappeur de point de terminaison RPC et au port du serveur RPC sur le service proxy. Par défaut, le port du serveur RPC est un port RPC dynamique, mais il peut être configuré pour [utiliser un port statique](#static).
* Tous les ordinateurs sur lesquels le service proxy de protection par mot de passe Azure AD sera installé doivent disposer d’un accès réseau aux points de terminaison suivants :

    |**Point de terminaison**|**Objectif**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Demandes d’authentification|
    |`https://enterpriseregistration.windows.net`|Fonctionnalité de protection par mot de passe Azure AD|
 
* Conditions préalables à l’utilisation du programme de mise à jour de l’agent Microsoft Azure AD Connect

  Le service du programme de mise à jour de l’agent Microsoft Azure AD Connect est installé parallèlement au service proxy de protection par mot de passe Azure AD. Une configuration supplémentaire est requise pour que le service du programme de mise à jour de l’agent Microsoft Azure AD Connect puisse fonctionner :

  Si votre environnement utilise un serveur proxy HTTP, vous devez suivre les instructions spécifiées dans [Travailler avec des serveurs proxy locaux existants](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).

  Le service du programme de mise à jour de l’agent Microsoft Azure AD Connect nécessite également les étapes 1.2 spécifiées dans [Exigences relatives à TLS](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements).

  L’accès réseau doit être activé pour l’ensemble des ports et des URL spécifiés dans les [procédures de configuration de l’environnement proxy d’application](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

  > [!WARNING]
  > Le proxy de protection par mot de passe et le proxy d’application Azure AD installent différentes versions du service du programme de mise à jour de l’agent Microsoft Azure AD Connect. Cela explique pourquoi les instructions font référence au contenu du proxy d’application. Ces différentes versions étant incompatibles lorsqu’elles sont installées côte à côte. Il n’est donc pas recommandé d’installer le proxy de protection par mot de passe et le proxy d’application Azure AD côte à côte sur le même ordinateur.

* Toutes les machines qui hébergent le service proxy de protection par mot de passe doivent être configurés pour autoriser les contrôleurs de domaine à ouvrir une session sur le service proxy. Cette capacité est contrôlée par le biais de l’affectation du privilège « Accéder à cet ordinateur à partir du réseau ».
* Toutes les machines qui hébergent le service proxy de protection par mot de passe doivent être configurées de manière à autoriser le trafic HTTP TLS 1.2 sortant.
* Un compte d’administrateur général pour inscrire la forêt et le service proxy de protection par mot de passe auprès d’Azure AD.
* Un compte disposant des privilèges d’administrateur de domaine Active Directory dans le domaine racine de la forêt pour inscrire la forêt Windows Server Active Directory auprès d’Azure AD.
* Les domaines Active Directory qui exécutent le logiciel du service d’agent DC doivent utiliser la réplication du système de fichiers distribué (DFSR) pour la réplication sysvol.

  Si votre domaine n’utilise pas encore DFSR, vous DEVEZ le faire migrer pour l’utiliser avant d’installer la protection par mot de passe Azure Active Directory. Pour plus d’informations, consultez le lien suivant :

  [Guide de migration de la réplication SYSVOL : Réplication FRS à DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > Le logiciel de l’agent DC de protection par mot de passe Azure AD s’installe actuellement sur des contrôleurs de domaine dans des domaines qui utilisent encore FRS (la technologie qui précède DFSR) pour la réplication sysvol, mais le logiciel ne fonctionnera pas correctement dans cet environnement. Des effets secondaires supplémentaires peuvent entraîner l’échec de la réplication des fichiers individuels, et le succès apparent des procédures de restauration sysvol qui ne parviennent pas à répliquer tous les fichiers en mode silencieux. Vous devez faire migrer votre domaine pour utiliser DFSR dès que possible, à la fois pour les avantages inhérents à DFSR et pour débloquer le déploiement de la protection par mot de passe Azure AD. Les versions ultérieures du logiciel seront automatiquement désactivées lors de l’exécution dans un domaine qui utilise encore FRS.

* Le service de distribution de clés doit être activé sur tous les contrôleurs de domaine figurant dans le domaine qui exécutent Windows Server 2012. Par défaut, ce service est activé par le début du déclencheur manuel.

## <a name="single-forest-deployment"></a>Déploiement d’une seule forêt

Le diagramme suivant montre comment les composants de base de la protection par mot de passe Azure AD opèrent ensemble dans un environnement Active Directory local.

![Comment les composants de protection de mot de passe Azure AD fonctionnent ensemble](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Il est judicieux d’examiner comment le logiciel fonctionne avant de le déployer. Consultez [Vue d’ensemble conceptuelle de la protection par mot de passe Azure AD](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Télécharger le logiciel

Deux programmes d’installation sont requis pour la protection de mot de passe Azure AD. Ils sont disponibles à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Installer et configurer le service proxy de protection par mot de passe

1. Choisissez un ou plusieurs serveurs pour héberger le service proxy de protection par mot de passe.
   * Chacun de ces services fournit uniquement des stratégies de mot de passe pour une forêt unique. La machine hôte doit être jointe à un domaine dans cette forêt. Les domaines racine et enfant sont tous deux pris en charge. Vous avez besoin d’une connectivité réseau entre au moins un contrôleur de domaine dans chaque domaine de la forêt et la machine de protection de mot de passe.
   * Vous pouvez exécuter le service proxy sur un contrôleur de domaine à des fins de test. Toutefois, ce contrôleur de domaine nécessite alors une connectivité internet, ce qui peut poser un problème de sécurité. Nous recommandons cette configuration à des fins de test uniquement.
   * À des fins de redondance, nous recommandons au moins deux serveurs proxy. Consultez [Haute disponibilité](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Installez le service proxy de protection par mot de passe Azure AD à l’aide de l’installateur de logiciel `AzureADPasswordProtectionProxySetup.exe`.
   * L’installation du logiciel ne nécessite pas un redémarrage. L’installation du logiciel peut être automatisée à l’aide de procédures MSI standard, par exemple :

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Le service Pare-feu Windows doit être en cours d’exécution avant de procéder à l’installation du package AzureADPasswordProtectionProxySetup.msi, afin d’éviter une erreur d’installation. Si le Pare-feu Windows est configuré pour ne pas s’exécuter, la solution de contournement consiste à activer et démarrer temporairement le service de Pare-feu Windows pendant l’installation. Le logiciel de proxy n’a aucune dépendance particulière sur le Pare-feu Windows après l’installation. Si vous utilisez un pare-feu tiers, il doit encore être configuré pour répondre aux exigences de déploiement. Celles-ci incluent l’autorisation de l’accès entrant au port 135 et au port du serveur RPC proxy. Consultez [Exigences en matière de déploiement](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Ouvrez une fenêtre PowerShell en tant qu’administrateur.
   * Le logiciel de proxy de protection par mot de passe inclut un nouveau module PowerShell, *AzureADPasswordProtection*. Les étapes suivantes exécutent diverses applets de commande à partir de ce module PowerShell. Importez le nouveau module comme suit :

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Pour vérifier que le service est en cours d’exécution, utilisez la commande PowerShell suivante :

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Le résultat doit afficher un **état** « En cours d’exécution ».

1. Enregistrez le proxy.
   * Une fois l’étape 3 effectuée, le service proxy est en cours d’exécution sur l’ordinateur, mais n’a pas encore les informations d’identification nécessaires pour communiquer avec Azure AD. Une inscription auprès d’Azure AD est requise :

     `Register-AzureADPasswordProtectionProxy`

     Cette applet de commande exige les informations d’identification d’administrateur général pour votre locataire Azure. Vous avez également besoin des privilèges d’administrateur de domaine Active Directory local dans le domaine racine de la forêt. Vous devez également exécuter cette applet de commande à l’aide d’un compte avec des privilèges d’administrateur local.

     Une fois que cette commande a réussi pour un service proxy, des appels supplémentaires de cette commande réussiront mais ils ne sont pas nécessaires.

      L’applet de commande `Register-AzureADPasswordProtectionProxy` prend en charge les trois modes d’authentification suivants. Les deux premiers modes prennent en charge Azure Multi-Factor Authentication, contrairement au troisième mode. Pour plus d’informations, consultez les commentaires ci-dessous.

     * Mode d’authentification interactive :

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ce mode ne fonctionne pas sur les systèmes d’exploitation Server Core. À la place, utilisez l’un des modes d’authentification suivants. De plus, ce mode peut échouer si la configuration de sécurité renforcée d’Internet Explorer est activée. La solution de contournement consiste à désactiver cette configuration, à inscrire le proxy, puis à la réactiver.

     * Mode d’authentification de code d’appareil :

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Vous terminez alors l’authentification en suivant les instructions affichées sur un autre appareil.

     * Mode d’authentification silencieuse (basée sur un mot de passe) :

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ce mode échoue si l’authentification multifacteur Azure est requise pour votre compte. Dans ce cas, utilisez l’un des deux modes d’authentification précédents, ou bien utilisez un autre compte qui ne nécessite pas d’authentification multifacteur.
        >
        > L’authentification multifacteur peut également être requise si l’inscription de l’appareil Azure (qui est utilisée en arrière-plan par la protection de mot de passe Azure AD) a été configurée pour exiger globalement une authentification multifacteur. Pour contourner ce problème, vous pouvez utiliser un autre compte prenant en charge l’authentification multifacteur avec l’un des deux modes d’authentification précédents, ou vous pouvez également assouplir temporairement l’exigence d’authentification multifacteur pour l’inscription d’un appareil Azure. Pour ce faire, accédez au portail de gestion Azure, ensuite accédez à Azure Active Directory, puis à Appareils, Paramètres de l’appareil et définissez « Exiger l’authentification multifacteur pour joindre des appareils » sur Non. Veillez à reconfigurer ce paramètre sur Oui une fois l’inscription terminée.
        >
        > Nous vous recommandons de contourner les exigences de l’authentification multifacteur à des fins de test uniquement.

       Vous n’êtes pas tenu actuellement de spécifier le paramètre *-ForestCredential*, qui est réservé pour de futures fonctionnalités.

   L’inscription du service proxy de protection par mot de passe est requise une seule fois au cours de la durée de vie du service. Après cela, le service proxy effectuera automatiquement toutes les autres tâches de maintenance nécessaires.

   > [!TIP]
   > Il peut y avoir un retard notable avant la fin, la première fois que vous exécutez cette applet de commande pour un locataire Azure spécifique. À moins qu’une erreur soit signalée, ne vous inquiétez pas de ce retard.

1. Inscrivez la forêt.
   * Vous devez initialiser la forêt Active Directory locale avec les informations d’identification nécessaires pour communiquer avec Azure à l’aide de l’applet de commande PowerShell `Register-AzureADPasswordProtectionForest`.

      Cette applet de commande exige les informations d’identification d’administrateur général pour votre locataire Azure.  Vous devez également exécuter cette applet de commande à l’aide d’un compte avec des privilèges d’administrateur local. Elle requiert également des privilèges d’administrateur d’entreprise Active Directory en local. Cette étape est exécutée une seule fois par forêt.

      L’applet de commande `Register-AzureADPasswordProtectionForest` prend en charge les trois modes d’authentification suivants. Les deux premiers modes prennent en charge Azure Multi-Factor Authentication, contrairement au troisième mode. Pour plus d’informations, consultez les commentaires ci-dessous.

     * Mode d’authentification interactive :

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ce mode ne fonctionnera pas sur les systèmes d’exploitation Server Core. À la place, utilisez l’un des deux modes d’authentification suivants. De plus, ce mode peut échouer si la configuration de sécurité renforcée d’Internet Explorer est activée. La solution de contournement consiste à désactiver cette configuration, à inscrire la forêt, puis à la réactiver.  

     * Mode d’authentification de code d’appareil :

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Vous terminez alors l’authentification en suivant les instructions affichées sur un autre appareil.

     * Mode d’authentification silencieuse (basée sur un mot de passe) :

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ce mode échoue si l’authentification multifacteur Azure est requise pour votre compte. Dans ce cas, utilisez l’un des deux modes d’authentification précédents, ou bien utilisez un autre compte qui ne nécessite pas d’authentification multifacteur.
        >
        > L’authentification multifacteur peut également être requise si l’inscription de l’appareil Azure (qui est utilisée en arrière-plan par la protection de mot de passe Azure AD) a été configurée pour exiger globalement une authentification multifacteur. Pour contourner ce problème, vous pouvez utiliser un autre compte prenant en charge l’authentification multifacteur avec l’un des deux modes d’authentification précédents, ou vous pouvez également assouplir temporairement l’exigence d’authentification multifacteur pour l’inscription d’un appareil Azure. Pour ce faire, accédez au portail de gestion Azure, ensuite accédez à Azure Active Directory, puis à Appareils, Paramètres de l’appareil et définissez « Exiger l’authentification multifacteur pour joindre des appareils » sur Non. Veillez à reconfigurer ce paramètre sur Oui une fois l’inscription terminée.
        >
        > Nous vous recommandons de contourner les exigences de l’authentification multifacteur à des fins de test uniquement.

       Ces exemples fonctionnent uniquement si l’utilisateur actuellement connecté est également un administrateur de domaine Active Directory pour le domaine racine. Si ce n’est pas le cas, vous pouvez fournir d’autres informations d’identification de domaine via le paramètre *-ForestCredential*.

   > [!NOTE]
   > Si plusieurs serveurs proxy sont installés dans votre environnement, peu importe le serveur proxy que vous utilisez pour inscrire la forêt.
   >
   > [!TIP]
   > Il peut y avoir un retard notable avant la fin, la première fois que vous exécutez cette applet de commande pour un locataire Azure spécifique. À moins qu’une erreur soit signalée, ne vous inquiétez pas de ce retard.

   L’inscription de la forêt Active Directory est requise une seule fois au cours de la durée de vie de la forêt. Après cela, les agents du contrôleur de domaine dans la forêt effectuent automatiquement toutes les autres tâches de maintenance nécessaires. Après l’exécution réussie de `Register-AzureADPasswordProtectionForest` pour une forêt, les appels supplémentaires de l’applet de commande réussissent mais sont inutiles.

   Pour que `Register-AzureADPasswordProtectionForest` réussisse, il faut qu’au moins un contrôleur de domaine exécutant Windows Server 2012 ou version ultérieure soit disponible dans le domaine du serveur proxy. Le logiciel de l’agent DC ne doit pas nécessairement être installé sur des contrôleurs de domaine avant cette étape.

1. Configurez le service proxy de protection par mot de passe pour communiquer via un proxy HTTP.

   Si votre environnement nécessite l’utilisation d’un proxy HTTP spécifique pour communiquer avec Azure, utilisez cette méthode : Créez un fichier *AzureADPasswordProtectionProxy.exe.config* dans le dossier %ProgramFiles%\Azure AD Password Protection Proxy\Service. Incluez le contenu suivant :

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Si votre proxy HTTP exige une authentification, ajoutez la balise *useDefaultCredentials* :

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Dans les deux cas, remplacez `http://yourhttpproxy.com:8080` par l’adresse et le port de votre serveur proxy HTTP spécifique.

   Si votre proxy HTTP est configuré pour utiliser une stratégie d’autorisation, vous devez accorder l’accès au compte d’ordinateur Active Directory de la machine qui héberge le service proxy de protection par mot de passe.

   Nous vous recommandons d’arrêter et de redémarrer le service proxy, une fois que vous avez créé ou mis à jour le fichier *AzureADPasswordProtectionProxy.exe.config*.

   Le service proxy ne prend pas en charge l’utilisation d’informations d’identification spécifiques pour la connexion à un proxy HTTP.

1. Facultatif : Configurez le service proxy de protection par mot de passe pour être à l’écoute sur un port spécifique.
   * Le logiciel de l’agent DC pour la protection par mot de passe sur les contrôleurs de domaine utilise RPC sur TCP pour communiquer avec le service proxy. Par défaut, le service proxy est à l’écoute sur n’importe quel point de terminaison RPC dynamique disponible. Toutefois, vous pouvez configurer le service pour être à l’écoute sur un port TCP spécifique, si cela est nécessaire en raison de la topologie du réseau ou de la configuration du pare-feu dans votre environnement.
      * <a id="static" /></a>Pour configurer le service afin qu’il s’exécute sous un port statique, utilisez l’applet de commande `Set-AzureADPasswordProtectionProxyConfiguration`.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Vous devez arrêter et redémarrer le service pour que ces modifications prennent effet.

      * Pour configurer le service afin qu’il s’exécute sous un port dynamique, utilisez la même procédure mais redéfinissez *StaticPort* sur zéro :

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Vous devez arrêter et redémarrer le service pour que ces modifications prennent effet.

   > [!NOTE]
   > Le service proxy de protection par mot de passe nécessite un redémarrage manuel après tout changement de la configuration des ports. Toutefois, vous n’êtes pas obligé de redémarrer le logiciel de l’agent DC sur les contrôleurs de domaine après avoir apporté ces modifications de configuration.

   * Pour exécuter une requête afin d’obtenir la configuration actuelle du service, utilisez l’applet de commande `Get-AzureADPasswordProtectionProxyConfiguration` :

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Installer le service d’agent DC

   Installez le service d’agent DC de protection par mot de passe à l’aide du package `AzureADPasswordProtectionDCAgentSetup.msi`.

   L’installation ou la désinstallation du logiciel nécessitent un redémarrage. Cette exigence découle du fait que les DLL de filtrage de mots de passe ne sont chargées ou déchargées que par un redémarrage.

   Vous pouvez installer le service d’agent DC sur une machine qui n’est pas encore un contrôleur de domaine. Dans ce cas, le service démarre et s’exécute, mais reste inactif jusqu’à ce que la machine soit promue comme contrôleur de domaine.

   Vous pouvez automatiser l’installation du logiciel à l’aide de procédures MSI standard. Par exemple :

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   Vous pouvez omettre l'indicateur `/norestart` si vous préférez que le programme d’installation redémarre automatiquement l’ordinateur.

L’installation se termine une fois que le logiciel de l’agent DC a été installé sur un contrôleur de domaine et que cet ordinateur a été redémarré. Aucune configuration supplémentaire n'est nécessaire ou possible.

## <a name="upgrading-the-proxy-agent"></a>Mise à niveau de l’agent proxy

Lorsqu’une version plus récente du logiciel proxy de protection par mot de passe Azure AD est disponible, la mise à niveau s’effectue en exécutant la dernière version du programme d’installation du logiciel `AzureADPasswordProtectionProxySetup.exe`. La dernière version du logiciel est disponible dans le [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Il n’est pas nécessaire de désinstaller la version actuelle du logiciel proxy : le programme d’installation effectuera une mise à niveau sur place. Aucun redémarrage n’est nécessaire lors de la mise à niveau du logiciel proxy. La mise à niveau du logiciel peut être automatisée à l’aide de procédures MSI standard, par exemple : `AzureADPasswordProtectionProxySetup.exe /quiet`.

L’agent proxy prend en charge la mise à niveau automatique. La mise à niveau automatique utilise le service du programme de mise à jour de l’agent Microsoft Azure AD Connect, installé à côté du service proxy. La mise à niveau automatique est activée par défaut et peut être activée ou désactivée à l’aide de l’applet de commande `Set-AzureADPasswordProtectionProxyConfiguration`. Pour connaître le paramétrage actuel, vous pouvez utiliser l’applet de commande `Get-AzureADPasswordProtectionProxyConfiguration`. Microsoft recommande de laisser la mise à niveau automatique activée en permanence.

L'applet de commande `Get-AzureADPasswordProtectionProxy` peut être utilisée pour interroger la version logicielle de tous les agents proxy actuellement installés dans une forêt.

## <a name="upgrading-the-dc-agent"></a>Mise à niveau de l’agent DC

Lorsqu’une version plus récente du logiciel de l’agent DC de protection par mot de passe Azure AD est disponible, la mise à niveau s'effectue en exécutant la dernière version du package logiciel `AzureADPasswordProtectionDCAgentSetup.msi`. La dernière version du logiciel est disponible dans le [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Il n’est pas nécessaire de désinstaller la version actuelle du logiciel de l’agent DC : le programme d’installation effectuera une mise à niveau sur place. Un redémarrage est toujours nécessaire lors de la mise à niveau du logiciel de l’agent DC. Cette exigence découle du comportement de base de Windows. 

La mise à niveau du logiciel peut être automatisée à l’aide de procédures MSI standard, par exemple : `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`.

Vous pouvez omettre l'indicateur `/norestart` si vous préférez que le programme d’installation redémarre automatiquement l’ordinateur.

L'applet de commande `Get-AzureADPasswordProtectionDCAgent` peut être utilisée pour interroger la version logicielle de tous les agents DC actuellement installés dans une forêt.

## <a name="multiple-forest-deployments"></a>Déploiement de plusieurs forêts

Il n’y a aucune configuration supplémentaire requise pour déployer la protection de mot de passe Azure AD dans plusieurs forêts. Chaque forêt est configurée indépendamment comme cela est décrit dans la section « Déploiement d’une seule forêt ». Chaque proxy de protection par mot de passe ne peut prendre en charge que les contrôleurs de domaine de la forêt à laquelle il est joint. Le logiciel de protection par mot de passe figurant dans une forêt donnée n’a pas connaissance des logiciels de protection par mot de passe déployés dans d’autres forêts, quelles que soient les configurations d’approbation d’Active Directory.

## <a name="read-only-domain-controllers"></a>Contrôleurs de domaines en lecture seule

Les modifications/définitions de mot de passe ne sont pas traitées ni rendues persistantes sur les contrôleurs de domaine en lecture seule. Elles sont transférés vers des contrôleurs de domaine accessibles en écriture. Par conséquent, vous n’êtes pas tenu d’installer le logiciel de l’agent DC sur les contrôleurs de domaine en lecture seule.

## <a name="high-availability"></a>Haute disponibilité

La préoccupation principale concernant la disponibilité de la protection par mot de passe est la disponibilité des serveurs proxy quand les contrôleurs de domaine d’une forêt tentent de télécharger les nouvelles stratégies ou d’autres données à partir d’Azure. Chaque agent DC utilise un algorithme de style tourniquet (round-robin) simple lorsqu’il décide quel serveur proxy appeler. L’agent ignore les serveurs proxy qui ne répondent pas. Pour la plupart des déploiements Active Directory totalement connectés avec réplication saine de l’état de l’annuaire et du dossier sysvol, deux serveurs proxy suffisent pour garantir la disponibilité. Cela entraîne un téléchargement en temps voulu des nouvelles stratégies et d’autres données. Toutefois, vous pouvez déployer des serveurs proxy supplémentaires.

La conception du logiciel de l’agent DC atténue les problèmes habituels associés à la haute disponibilité. L’agent DC gère un cache local de la stratégie de mot de passe la plus récemment téléchargée. Même si tous les serveurs proxy inscrits deviennent indisponibles, les agents DC continuent d’appliquer leur stratégie de mot de passe mis en cache. Une fréquence de mise à jour raisonnable pour les stratégies de mot de passe dans un déploiement à grande échelle se compte généralement en jours, pas en heures, ni moins. Par conséquent, les pannes brèves des serveurs proxy n’ont pas d’impact important sur la protection par mot de passe Azure AD.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez installé les services dont vous avez besoin pour la protection par mot de passe Azure AD sur vos serveurs locaux, [effectuez une configuration post-installation et collectez les informations de rapport](howto-password-ban-bad-on-premises-operations.md) pour achever votre déploiement.

[Vue d’ensemble conceptuelle de la protection de mot de passe Azure AD](concept-password-ban-bad-on-premises.md)
