---
title: Déployer la protection de mot de passe Azure AD - Azure Active Directory
description: Déployer la protection de mot de passe Azure AD pour interdire les mauvais mots de passe en local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1c24ec49652cfe9105aa66fd1d5e26c81afcd14
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904625"
---
# <a name="deploy-azure-ad-password-protection"></a>Déployer la protection par mot de passe d’Azure AD

Maintenant que vous comprenez [comment appliquer la protection de mot de passe Azure AD pour Windows Server Active Directory](concept-password-ban-bad-on-premises.md), l’étape suivante consiste à planifier et exécuter votre déploiement.

## <a name="deployment-strategy"></a>Stratégie de déploiement

Nous vous recommandons de commencer les déploiements en mode audit. Mode d’audit est le paramètre initial par défaut, où les mots de passe peuvent continuer à définir. Les mots de passe qui seraient bloqués sont enregistrées dans le journal des événements. Après avoir déployé les serveurs proxy et les Agents du contrôleur de domaine en mode audit, vous devez surveiller l’impact de la stratégie de mot de passe sur les utilisateurs et de l’environnement lors de la stratégie est appliquée.

Durant la phase d’audit, de nombreuses organisations jugent que :

* Elles doivent améliorer les processus opérationnels existants pour utiliser des mots de passe plus sécurisés.
* Les utilisateurs utilisent souvent des mots de passe non sécurisés.
* Ils doivent informer les utilisateurs sur la modification à venir de mise en œuvre de la sécurité, d’impact possible sur ces derniers et comment choisir des mots de passe plus sécurisés.

Une fois que la fonctionnalité a été exécuté en mode audit pendant un délai raisonnable, vous pouvez changer la configuration à partir de *Audit* à *appliquer* pour exiger des mots de passe plus sécurisés. Durant cette période, effectuer un monitoring strict est une bonne idée.

## <a name="deployment-requirements"></a>Composants requis pour le déploiement

* Tous les contrôleurs de domaine permettant d’obtenir l’Agent du contrôleur de domaine de service pour la protection de mot de passe Azure AD installée doit exécuter Windows Server 2012 ou version ultérieure. Cette exigence n’implique pas que le domaine Active Directory ou la forêt doit également être au niveau fonctionnel de Windows Server 2012 domaine ou forêt. Comme mentionné dans [principes de conception](concept-password-ban-bad-on-premises.md#design-principles), aucun niveau fonctionnel du domaine ou le FFL requis pour un contrôleur de domaine agent ou proxy exécuter le logiciel minimale.
* Tous les ordinateurs qui obtiennent le service agent contrôleur de domaine est installé doivent avoir le .NET 4.5 est installé.
* Tous les ordinateurs qui obtiennent le proxy de service pour la protection de mot de passe Azure AD installée doit exécuter Windows Server 2012 R2 ou version ultérieure.
* Tous les ordinateurs où sera installé le service de Proxy de Protection de mot de passe Azure AD doivent avoir .NET 4.7 est installé.
  .NET 4.7 doit déjà être installé sur un serveur Windows entièrement mis à jour. Si ce n’est pas le cas, téléchargez et exécutez le programme d’installation, consultez [le .NET Framework 4.7 un programme d’installation hors connexion pour Windows](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Tous les ordinateurs, y compris les contrôleurs de domaine, qui obtiennent des composants de protection de mot de passe Azure AD installés doit le Universal C Runtime est installé. Vous pouvez obtenir le runtime en vous assurant que toutes les mises à jour à partir de Windows Update. Ou vous pouvez l’obtenir dans un package de mise à jour du système d’exploitation spécifique. Pour plus d’informations, consultez [mise à jour pour le Runtime C universel dans Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Connectivité de réseau doit exister entre au moins un contrôleur de domaine dans chaque domaine et au moins un serveur qui héberge le service de proxy pour la protection de mot de passe. Cette connectivité doit autoriser le contrôleur de domaine accéder aux port mappeur de point de terminaison RPC 135 et le port du serveur RPC sur le service de proxy. Par défaut, le port du serveur RPC est un port RPC dynamique, mais il peut être configuré pour [utiliser un port statique](#static).
* Tous les ordinateurs qui hébergent le service de proxy doivent avoir accès au réseau pour les points de terminaison suivants :

    |**Point de terminaison**|**Objectif**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Demandes d’authentification|
    |`https://enterpriseregistration.windows.net`|Fonctionnalité de protection par mot de passe Azure AD|

* Tous les ordinateurs qui hébergent le service de proxy pour la protection de mot de passe doivent être configurés pour autoriser le trafic TLS 1.2 HTTP sortant.
* Un compte d’administrateur général pour inscrire le service de proxy pour la protection de mot de passe et de la forêt avec Azure AD.
* Un compte disposant des privilèges administrateur de domaine Active Directory dans le domaine racine de forêt pour inscrire la forêt Windows Server Active Directory avec Azure AD.
* N’importe quel domaine Active Directory qui exécute le logiciel du service Agent du contrôleur de domaine doit utiliser la réplication de système de fichiers distribués (DFSR) pour la réplication sysvol.
* Le Service de Distribution de clés doit être activé sur tous les contrôleurs de domaine dans le domaine qui exécutent Windows Server 2012. Par défaut, ce service est activé par le biais de début du déclencheur manuel.

## <a name="single-forest-deployment"></a>Déploiement d’une seule forêt

Le diagramme suivant montre comment les composants de base de protection de mot de passe Azure AD fonctionnent ensemble dans un environnement de Active Directory en local.

![Comment les composants de protection de mot de passe Azure AD fonctionnent ensemble](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Il est judicieux d’examiner comment le logiciel fonctionne avant de le déployer. Consultez [une vue d’ensemble conceptuelle de protection de mot de passe Azure AD](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Télécharger le logiciel

Il existe deux programmes d’installation requis pour la protection de mot de passe Azure AD. Elles sont disponibles à partir de la [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Installer et configurer le service de proxy pour la protection de mot de passe

1. Choisissez un ou plusieurs serveurs pour héberger le service de proxy pour la protection de mot de passe.
   * Chacun de ces services permettre uniquement fournir des stratégies de mot de passe pour une forêt unique. L’ordinateur hôte doit être joint à un domaine dans cette forêt. Domaines racines et enfants sont tous deux pris en charge. Vous avez besoin de connectivité réseau entre au moins un contrôleur de domaine dans chaque domaine de la forêt et l’ordinateur de protection de mot de passe.
   * Vous pouvez exécuter le service de proxy sur un contrôleur de domaine pour le test. Mais ce contrôleur de domaine puis nécessite une connectivité internet, ce qui peut être un problème de sécurité. Nous recommandons cette configuration pour test uniquement.
   * Nous recommandons au moins deux serveurs proxy pour la redondance. Consultez [haute disponibilité](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Service du Proxy de Protection de mot de passe d’installation Azure AD à l’aide du `AzureADPasswordProtectionProxySetup.exe` installateur de logiciel.
   * L’installation du logiciel ne nécessite pas un redémarrage. L’installation du logiciel peut être automatisée à l’aide de procédures MSI standard, par exemple :

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > Le service de pare-feu de Windows doit être en cours d’exécution avant d’installer le package AzureADPasswordProtectionProxySetup.msi pour éviter une erreur d’installation. Si le pare-feu Windows est configuré pour ne pas exécuter, la solution de contournement consiste à activer temporairement et d’exécuter le service de pare-feu lors de l’installation. Le logiciel de serveur proxy n’a aucune dépendance spécifique sur le pare-feu de Windows après l’installation. Si vous utilisez un pare-feu tiers, il doit toujours être configuré pour répondre aux exigences de déploiement. Citons notamment autoriser l’accès entrant au port 135 et le port RPC du serveur proxy. Consultez [exigences relatives au déploiement](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Ouvrez une fenêtre PowerShell en tant qu’administrateur.
   * Le logiciel de proxy de protection de mot de passe inclut un nouveau module PowerShell, *AzureADPasswordProtection*. Les étapes suivantes exécutent diverses applets de commande à partir de ce module PowerShell. Importez le nouveau module comme suit :

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Pour vérifier que le service est en cours d’exécution, utilisez la commande PowerShell suivante :

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Le résultat doit afficher un **état** « Running ».

1. Enregistrez le proxy.
   * Une fois l’étape 3 terminée, le service de proxy est en cours d’exécution sur l’ordinateur. Mais le service ne dispose pas encore les informations d’identification nécessaires pour communiquer avec Azure AD. L’inscription auprès d’Azure AD est nécessaire :

     `Register-AzureADPasswordProtectionProxy`

     Cette applet de commande requiert des informations d’identification d’administrateur général pour votre client Azure. Vous devez également sur site Active Directory domaine des privilèges d’administrateur dans le domaine racine de forêt. Une fois cette commande réussie qu’une seule fois pour un service de proxy, les appels supplémentaires de celui-ci réussiront mais ne sont pas nécessaires.

      Le `Register-AzureADPasswordProtectionProxy` applet de commande prend en charge les modes de trois authentification ci-dessous.

     * Mode d’authentification interactive :

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ce mode ne fonctionne pas sur les systèmes d’exploitation de Server Core. Au lieu de cela, utilisez un des modes d’authentification ci-dessous. En outre, ce mode peut échouer si la Configuration de sécurité renforcée d’Internet Explorer est activée. La solution de contournement consiste à désactiver cette Configuration, inscrire le serveur proxy, puis réactivez-la.

     * Mode d’authentification de code d’appareil :

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Vous permet d’effectuer l’authentification en suivant les instructions affichées sur un autre appareil.

     * Mode d’authentification silencieuse (basée sur un mot de passe) :

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ce mode échoue si l’authentification multifacteur Azure est requise. Dans ce cas, utilisez un des modes d’authentification à deux précédentes.

       Vous ne disposez pas de spécifier le *- ForestCredential* paramètre, qui est réservée pour les futures fonctionnalités.

   Inscription du service de proxy pour la protection de mot de passe est nécessaire qu’une seule fois dans la durée de vie du service. Après cela, le service de proxy effectue automatiquement les autres tâches de maintenance nécessaires.

   > [!TIP]
   > Il peut y avoir un retard notable avant la fin de la première fois que l’exécution de cette applet de commande pour un client Azure spécifique. Ne vous inquiétez pas, sauf si une erreur est signalée, à propos de ce délai.

1. Inscrivez la forêt.
   * Vous devez initialiser la forêt Active Directory en local avec les informations d’identification nécessaires pour communiquer avec Azure à l’aide de la `Register-AzureADPasswordProtectionForest` applet de commande PowerShell. L’applet de commande requiert des informations d’identification d’administrateur général pour votre client Azure. Elle requiert également sur site Active Directory domaine des privilèges d’administrateur dans le domaine racine de forêt. Cette étape est exécutée une seule fois par forêt.

      Le `Register-AzureADPasswordProtectionForest` applet de commande prend en charge les modes de trois authentification ci-dessous.

     * Mode d’authentification interactive :

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ce mode ne fonctionnera pas sur les systèmes d’exploitation de Server Core. Au lieu de cela, utilisez un des modes de deux authentification ci-dessous. En outre, ce mode peut échouer si la Configuration de sécurité renforcée d’Internet Explorer est activée. La solution de contournement consiste à désactiver cette Configuration, inscrire le serveur proxy, puis réactivez-la.  

     * Mode d’authentification de code d’appareil :

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Vous permet d’effectuer l’authentification en suivant les instructions affichées sur un autre appareil.

     * Mode d’authentification silencieuse (basée sur un mot de passe) :

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ce mode échoue si l’authentification multifacteur Azure est requise. Dans ce cas, utilisez un des modes d’authentification à deux précédentes.

       Ces exemples réussissent uniquement si l’utilisateur actuellement connecté est également un administrateur de domaine Active Directory pour le domaine racine. Si ce n’est pas le cas, vous pouvez fournir des informations d’identification de l’autre domaine via le *- ForestCredential* paramètre.

   > [!NOTE]
   > Si plusieurs serveurs proxy sont installés dans votre environnement, peu importe quel serveur proxy que vous utilisez pour inscrire la forêt.
   >
   > [!TIP]
   > Il peut y avoir un retard notable avant la fin de la première fois que l’exécution de cette applet de commande pour un client Azure spécifique. Ne vous inquiétez pas, sauf si une erreur est signalée, à propos de ce délai.

   L’inscription de la forêt Active Directory est nécessaire qu’une seule fois dans la durée de vie de la forêt. Après cela, les Agents du contrôleur de domaine dans la forêt effectuera automatiquement les autres tâches de maintenance nécessaires. Après avoir `Register-AzureADPasswordProtectionForest` s’exécute avec succès pour une forêt, les appels supplémentaires de l’applet de commande réussit, mais ne sont pas nécessaires.

   Pour `Register-AzureADPasswordProtectionForest` pour réussir, au moins un contrôleur de domaine exécutant Windows Server 2012 ou version ultérieure doit être disponible dans le domaine du serveur de proxy. Mais le logiciel de l’Agent du contrôleur de domaine ne doit pas être installé sur des contrôleurs de domaine avant cette étape.

1. Configurer le service de proxy pour la protection de mot de passe communiquer via un proxy HTTP.

   Si votre environnement nécessite l’utilisation d’un proxy HTTP spécifique pour communiquer avec Azure, utilisez cette méthode : Créer un *AzureADPasswordProtectionProxy.exe.config* fichier dans le dossier de mot de passe Protection Proxy\Service %ProgramFiles%\Azure AD. Inclure le contenu suivant :

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

   Si votre serveur proxy HTTP requiert une authentification, ajoutez le *useDefaultCredentials* balise :

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

   Dans les deux cas, remplacez `http://yourhttpproxy.com:8080` avec l’adresse et le port de votre serveur de proxy HTTP spécifique.

   Si votre proxy HTTP est configuré pour nous une stratégie d’autorisation, vous devez accorder l’accès au compte d’ordinateur Active Directory de l’ordinateur qui héberge le service de proxy pour la protection de mot de passe.

   Nous vous recommandons d’arrêter et redémarrer le service de proxy, une fois que vous créez ou mettez à jour le *AzureADPasswordProtectionProxy.exe.config* fichier.

   Le service de proxy ne prend pas en charge l’utilisation des informations d’identification spécifiques pour la connexion à un proxy HTTP.

1. Facultatif : Configurer le service de proxy pour la protection de mot de passe à l’écoute sur un port spécifique.
   * Le logiciel de l’Agent du contrôleur de domaine pour la protection de mot de passe sur les contrôleurs de domaine utilise RPC sur TCP pour communiquer avec le service de proxy. Par défaut, le service de proxy écoute sur n’importe quel point de terminaison RPC dynamique disponible. Mais vous pouvez configurer le service pour l’écoute sur un port TCP spécifique, si cela est nécessaire en raison de la topologie de réseau ou de la configuration requise du pare-feu dans votre environnement.
      * <a id="static" /></a>Pour configurer le service afin qu’il s’exécute sous un port statique, utilisez la cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Vous devez arrêter et redémarrer le service pour que ces modifications prennent effet.

      * Pour configurer le service s’exécute sous un port dynamique, utilisez la même procédure, mais définissez *StaticPort* remettre à zéro :

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Vous devez arrêter et redémarrer le service pour que ces modifications prennent effet.

   > [!NOTE]
   > Le service de proxy pour la protection de mot de passe nécessite un redémarrage manuel après que toute modification de configuration du port. Mais vous n’êtes pas obligé de redémarrer le contrôleur de domaine service logiciel de l’Agent sur les contrôleurs de domaine après avoir apporté ces modifications de configuration.

   * Pour exécuter une requête pour la configuration actuelle du service, utilisez le `Get-AzureADPasswordProtectionProxyConfiguration` applet de commande :

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Installer le service Agent du contrôleur de domaine

   Installer le service Agent du contrôleur de domaine pour la protection de mot de passe à l’aide de la `AzureADPasswordProtectionDCAgentSetup.msi` package.

   L’installation de logiciel, ou la désinstallation, nécessite un redémarrage. Il s’agit, car le filtre de mot de passe DLL sont uniquement chargé ou déchargé par un redémarrage.

   Vous pouvez installer le service Agent du contrôleur de domaine sur un ordinateur qui n’est pas encore un contrôleur de domaine. Dans ce cas, le service de démarrer et exécuter mais reste inactif jusqu'à ce que l’ordinateur est promu un contrôleur de domaine.

   Vous pouvez automatiser l’installation du logiciel à l’aide de procédures standard de MSI. Par exemple : 

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn`

   > [!WARNING]
   > L’exemple de la commande de msiexec ici provoque un redémarrage immédiat. Pour éviter cela, utilisez le `/norestart` indicateur.

L’installation est terminée une fois que le logiciel de l’Agent du contrôleur de domaine est installé sur un contrôleur de domaine, et cet ordinateur est redémarré. Aucune configuration supplémentaire n'est nécessaire ou possible.

## <a name="multiple-forest-deployments"></a>Déploiement de plusieurs forêts

Il n’y a aucune configuration supplémentaire requise pour déployer la protection de mot de passe Azure AD dans plusieurs forêts. Chaque forêt est configuré indépendamment comme décrit dans la section « déploiement d’une seule forêt ». Chaque proxy de mot de passe de protection peut prendre uniquement en charge les contrôleurs de domaine à partir de la forêt auquel il est joint. Le logiciel de protection de mot de passe dans n’importe quelle forêt n’a pas connaissance du logiciel de protection de mot de passe est déployée dans d’autres forêts, indépendamment des configurations d’approbation Active Directory.

## <a name="read-only-domain-controllers"></a>Contrôleurs de domaines en lecture seule

Modifications de mot de passe/jeux ne sont pas traitées et rendues persistantes sur les contrôleurs de domaine en lecture seule (RODC). Ils sont transférés vers les contrôleurs de domaine accessible en écriture. Par conséquent, vous n’avez pas à installer le logiciel de l’Agent du contrôleur de domaine sur le RODC.

## <a name="high-availability"></a>Haute disponibilité

Le problème de disponibilité principal pour la protection de mot de passe est la disponibilité des serveurs proxy lorsque les contrôleurs de domaine dans une forêt essaient de télécharger de nouvelles stratégies ou autres données à partir d’Azure. Chaque Agent contrôleur de domaine utilise un algorithme de tourniquet (Round Robin)-style simple lorsque vous décidez quel serveur proxy doit appeler. L’Agent ignore les serveurs proxy qui ne répondent pas. Pour les déploiements Active Directory plus entièrement connectés qui ont une réplication saine de l’état du dossier directory et sysvol, deux serveurs proxy est suffisante pour garantir la disponibilité. Cela entraîne un téléchargement en temps voulu des nouvelles stratégies et d’autres données. Mais vous pouvez déployer des serveurs proxy supplémentaires.

La conception du logiciel de l’Agent du contrôleur de domaine permet d’atténuer les problèmes habituels qui sont associés à haute disponibilité. L’Agent du contrôleur de domaine maintient un cache local de la stratégie de mot de passe plus récemment téléchargé. Même si tous les inscrit les serveurs proxy deviennent indisponibles, les Agents de contrôleur de domaine continuent à appliquer leur stratégie de mot de passe mis en cache. Une fréquence de mise à jour raisonnable pour les stratégies de mot de passe dans un déploiement à grande échelle est généralement *jours*, non en heures ou inférieur. Par conséquent, les pannes brèves des serveurs proxy considérablement n’ont aucun impact protection de mot de passe Azure AD.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez installé les services dont vous avez besoin pour la protection de mot de passe Azure AD sur vos serveurs locaux, [effectuer une configuration de post-installation et rassembler les informations de rapport](howto-password-ban-bad-on-premises-operations.md) pour achever votre déploiement.

[Vue d’ensemble conceptuelle de protection de mot de passe Azure AD](concept-password-ban-bad-on-premises.md)
