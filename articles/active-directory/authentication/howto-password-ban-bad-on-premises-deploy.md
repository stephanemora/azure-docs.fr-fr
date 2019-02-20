---
title: Déployer la préversion de la protection par mot de passe Azure AD
description: Déployer la préversion de la protection par mot de passe Azure AD pour interdire les mots de passe incorrects localement
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
ms.openlocfilehash: 0a4a4b760652ce38e27e12e9eb73fbe7692eddbc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204370"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Aperçu : Déployer la protection par mot de passe Azure AD

|     |
| --- |
| La protection par mot de passe Azure AD est une fonctionnalité d’évaluation publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Maintenant que nous comprenons [comment appliquer la protection par mot de passe Azure AD pour Windows Server Active Directory](concept-password-ban-bad-on-premises.md), l’étape suivante consiste à planifier et à exécuter le déploiement.

## <a name="deployment-strategy"></a>Stratégie de déploiement

Microsoft recommande que n’importe quel déploiement démarre en mode d’audit. Le mode d’audit est le paramètre initial par défaut où les mots de passe peuvent continuer à être définis et tout mot de passe qui serait bloqué donne lieu à une entrée dans le journal des événements. Une fois que les serveurs proxy et les agents DC sont entièrement déployés en mode d’audit, un monitoring régulier doit être effectué afin de déterminer l’impact de la mise en œuvre de la stratégie de mot de passe sur les utilisateurs et l’environnement si elle était appliquée.

Durant la phase d’audit, de nombreuses organisations constatent ce qui suit :

* Elles doivent améliorer les processus opérationnels existants pour utiliser des mots de passe plus sécurisés.
* Les utilisateurs sont habitués à choisir régulièrement des mots de passe non sécurisés.
* Elles doivent informer les utilisateurs des modifications de la sécurité à venir, l’impact que cela peut avoir sur eux et les aider à mieux comprendre comment choisir des mots de passe plus sécurisés.

Une fois que la fonctionnalité a été exécutée en mode d’audit pendant une durée raisonnable, la configuration de l’application peut passer de **Audit** à **Appliquer**, nécessitant donc des mots de passe plus sécurisés. Durant cette période, effectuer un monitoring strict est une bonne idée.

## <a name="deployment-requirements"></a>Composants requis pour le déploiement

* Tous les contrôleurs de domaine sur lesquels le service de l’agent DC de protection par mot de passe Azure AD sera installé doivent exécuter Windows Server 2012 ou ultérieur.
* Tous les ordinateurs sur lesquels le service proxy de protection par mot de passe Azure AD sera installé doivent exécuter Windows Server 2012 R2 ou ultérieur.
* Le runtime C universel doit être installé sur tous les ordinateurs sur lesquels les composants de protection par mot de passe Azure AD sont installés, notamment les contrôleurs de domaine.
Pour cela, téléchargez de préférence tous les correctifs sur l’ordinateur avec Windows Update. Sinon, il est possible d’installer un package de mise à jour propre au système d’exploitation ; voir [Mise à jour du runtime C universel C sous Windows](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows).
* Une connectivité réseau doit exister entre au moins un contrôleur de domaine dans chaque domaine et au moins un serveur hébergeant le service proxy de protection par mot de passe Azure AD. Cette connectivité doit autoriser le contrôleur de domaine à accéder au port du mappeur de point de terminaison RPC (135) et au port du serveur RPC sur le service de proxy. Le port du serveur RPC est par défaut un port RPC dynamique, mais il peut être configuré (voir ci-dessous) pour utiliser un port statique.
* Tous les ordinateurs hébergeant le service proxy de protection par mot de passe Azure AD doivent disposer d’un accès réseau aux points de terminaison suivants :

    |Point de terminaison |Objectif|
    | --- | --- |
    |`https://login.microsoftonline.com`|Demandes d’authentification|
    |`https://enterpriseregistration.windows.net`|Fonctionnalité de protection par mot de passe Azure AD|

* Un compte d’administrateur général pour inscrire la forêt et le service proxy de protection par mot de passe Azure AD auprès d’Azure AD.
* Un compte disposant des privilèges d’administrateur de domaine Active Directory dans le domaine racine de la forêt pour inscrire la forêt Windows Server Active Directory auprès d’Azure AD.
* Les domaines Active Directory exécutant le logiciel du service d’agent de contrôleur de domaine doivent utiliser DFSR pour la réplication sysvol.

## <a name="single-forest-deployment"></a>Déploiement de forêt unique

Le diagramme suivant montre comment les composants de base de la protection par mot de passe Azure AD opèrent ensemble dans un environnement Active Directory local.  

![Comment les composants de protection par mot de passe Azure AD fonctionnent ensemble](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>Télécharger le logiciel

Deux programmes d’installation sont nécessaires pour la protection par mot de passe Azure AD. Vous pouvez les télécharger à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Installer et configurer le service proxy de protection par mot de passe Azure AD

1. Choisissez un ou plusieurs serveurs pour héberger le service proxy de protection par mot de passe Azure AD.
   * Chacun de ces services peut uniquement fournir des stratégies de mot de passe pour une seule forêt, et l’ordinateur hôte doit être joint à un domaine (le domaine racine et le domaine enfant sont tous les deux pris en charge) dans cette forêt. Pour que le service proxy de protection par mot de passe Azure AD fonctionne comme prévu, il doit exister une connectivité réseau entre au moins un contrôleur de domaine dans chaque domaine de la forêt et l’ordinateur du proxy de protection par mot de passe Azure AD.
   * Il est possible d’installer et d’exécuter le service proxy de protection par mot de passe Azure AD sur un contrôleur de domaine à des fins de test. L’inconvénient est que le contrôleur de domaine exige alors une connexion Internet qui peut constituer un problème de sécurité. Microsoft recommande d’utiliser cette configuration uniquement à des fins de test.
   * À des fins de redondance, au moins deux serveurs proxy sont recommandés. Consultez [Haute disponibilité](howto-password-ban-bad-on-premises-deploy.md#high-availability).

2. Installez le service proxy de protection par mot de passe Azure AD à l’aide du package MSI AzureADPasswordProtectionProxySetup.msi.
   * L’installation du logiciel ne nécessite pas un redémarrage. L’installation du logiciel peut être automatisée à l’aide de procédures MSI standard, par exemple : `msiexec.exe /i AzureADPasswordProtectionProxySetup.msi /quiet /qn`

      > [!NOTE]
      > Le service Pare-feu Windows doit être en cours d’exécution avant de procéder à l’installation du package MSI AzureADPasswordProtectionProxySetup.msi, faute de quoi une erreur d’installation se produit. Si le Pare-feu Windows est configuré pour ne pas s’exécuter, la solution de contournement consiste à temporairement activer et démarrer le service de Pare-feu Windows pendant le processus d’installation. Le logiciel de proxy n’a aucune dépendance particulière sur le logiciel du Pare-feu Windows après l’installation. Si vous utilisez un pare-feu tiers, il doit tout de même être configuré pour répondre aux exigences du déploiement (autoriser l’accès entrant sur le port 135 et le port du serveur RPC proxy, qu’il soit dynamique ou statique). Voir [Exigences en matière de déploiement](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)

3. Ouvrez une fenêtre PowerShell en tant qu’administrateur.
   * Le logiciel du proxy de protection par mot de passe Azure AD inclut un nouveau module PowerShell nommé AzureADPasswordProtection. Les étapes suivantes sont basées sur l’exécution de diverses cmdlets à partir de ce module PowerShell et supposent que vous avez ouvert une nouvelle fenêtre PowerShell et importé le nouveau module comme suit :

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * Vérifiez que le service est en cours d’exécution à l’aide de la commande PowerShell suivante : `Get-Service AzureADPasswordProtectionProxy | fl`.
     Le résultat doit être que **l’état** retourne le résultat « En cours d’exécution ».

4. Enregistrez le proxy.
   * Une fois l’étape 3 effectuée, le service proxy de protection par mot de passe Azure AD est en cours d’exécution sur l’ordinateur, mais n’a pas encore les informations d’identification nécessaires pour communiquer avec Azure AD. L’inscription auprès d’Azure AD est nécessaire pour activer cette capacité à l’aide de la cmdlet PowerShell `Register-AzureADPasswordProtectionProxy`. La cmdlet requiert des informations d'identification d’administrateur global pour votre locataire Azure, ainsi que des privilèges d’administrateur de domaine Active Directory local dans le domaine racine de la forêt. Une fois cette opération réussie pour un service de proxy donné, des appels supplémentaires de `Register-AzureADPasswordProtectionProxy` continuent de réussir, mais ne sont pas nécessaires.

      L’applet de commande Register-AzureADPasswordProtectionProxy prend en charge trois modes d’authentification différents, comme suit.

      * Mode d’authentification interactive :

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > Ce mode ne fonctionne pas sur les systèmes d’exploitation Server Core. Utilisez à la place l’un des autres modes d’authentification autre comme indiqué ci-dessous.

         > [!NOTE]
         > Ce mode peut échouer si la configuration de sécurité renforcée d’Internet Explorer est activée. La solution de contournement consiste à désactiver la sécurité renforcée d’Internet Explorer, à inscrire le proxy, puis à réactiver la sécurité renforcée d’Internet Explorer.

      * Mode d’authentification de code d’appareil :

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         Vous pouvez alors effectuer l’authentification en suivant les instructions affichées sur un autre appareil.

      * Mode d’authentification silencieuse (basée sur un mot de passe) :

         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > Ce mode échoue si, pour une raison quelconque, l’authentification exige l’authentification multifacteur (MFA). Si c’est le cas, utilisez l’un des deux modes précédents pour effectuer une authentification MFA.

      Il n’est actuellement pas nécessaire de spécifier le paramètre -ForestCredential, qui est réservé pour de futures fonctionnalités.

   > [!NOTE]
   > L’inscription du service proxy de protection par mot de passe Azure AD est censée être une étape unique au cours de la durée de vie du service. Le service proxy effectue automatiquement toutes les autres tâches de maintenance nécessaires à partir de là. Une fois cette opération réussie pour un proxy donné, des appels supplémentaires de « Register-AzureADPasswordProtectionProxy » continuent de réussir, mais ne sont pas nécessaires.

   > [!TIP]
   > Il peut y avoir un retard important (plusieurs secondes) la première fois que cette cmdlet est exécutée pour un locataire Azure donné avant la fin d’exécution de la cmdlet. Sauf si une erreur est signalée, ce délai ne doit pas être considéré comme inquiétant.

5. Inscrivez la forêt.
   * La forêt Active Directory locale doit être initialisée avec les informations d’identification nécessaires pour communiquer avec Azure à l’aide de l’applet de commande PowerShell `Register-AzureADPasswordProtectionForest`. La cmdlet requiert des informations d'identification d’administrateur global pour votre locataire Azure, ainsi que des privilèges d’administrateur de domaine Active Directory local dans le domaine racine de la forêt. Cette étape est exécutée une seule fois par forêt.

      L’applet de commande Register-AzureADPasswordProtectionForest prend en charge trois modes d’authentification différents, comme suit.

      * Mode d’authentification interactive :

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > Ce mode ne fonctionne pas sur les systèmes d’exploitation Server Core. Utilisez à la place l’un des autres modes d’authentification autre comme indiqué ci-dessous.

         > [!NOTE]
         > Ce mode peut échouer si la configuration de sécurité renforcée d’Internet Explorer est activée. La solution de contournement consiste à désactiver la sécurité renforcée d’Internet Explorer, à inscrire le proxy, puis à réactiver la sécurité renforcée d’Internet Explorer.  

      * Mode d’authentification de code d’appareil :

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         Vous pouvez alors effectuer l’authentification en suivant les instructions affichées sur un autre appareil.

      * Mode d’authentification silencieuse (basée sur un mot de passe) :
         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > Ce mode échoue si l’authentification exige l’authentification multifacteur (MFA). Si c’est le cas, utilisez l’un des deux modes précédents pour effectuer une authentification MFA.

      Les exemples ci-dessus fonctionnent uniquement si l’utilisateur actuellement connecté est également un administrateur de domaine Active Directory pour le domaine racine. Si ce n’est pas le cas, vous pouvez fournir d’autres informations d’identification de domaine par le biais du paramètre -ForestCredential.

   > [!NOTE]
   > Si plusieurs serveurs proxy sont installés dans votre environnement, peu importe le serveur proxy utilisé pour inscrire la forêt.

   > [!TIP]
   > Il peut y avoir un retard important (plusieurs secondes) la première fois que cette cmdlet est exécutée pour un locataire Azure donné avant la fin d’exécution de la cmdlet. Sauf si une erreur est signalée, ce délai ne doit pas être considéré comme inquiétant.

   > [!NOTE]
   > L’inscription de la forêt Active Directory est censée être une étape unique au cours de la durée de vie de la forêt. Les agents du contrôleur de domaine qui s’exécutent dans la forêt effectuent automatiquement toutes les autres tâches de maintenance nécessaires à partir de là. Une fois cette opération réussie pour une forêt donnée, des appels supplémentaires de `Register-AzureADPasswordProtectionForest` continuent de réussir, mais ne sont pas nécessaires.

   > [!NOTE]
   > Pour que `Register-AzureADPasswordProtectionForest` réussisse, il faut qu’au moins un contrôleur de domaine Windows Server 2012 (ou version ultérieure) soit disponible dans le domaine du serveur proxy. Il n’est cependant pas nécessaire que les logiciels d’agent de contrôleur de domaine soient installés sur des contrôleurs de domaine avant cette étape.

6. Configurez le service proxy de protection par mot de passe Azure AD pour communiquer par le biais d’un proxy HTTP.

   Si votre environnement nécessite l’utilisation d’un proxy HTTP spécifique pour communiquer avec Azure, vous devez procéder comme suit pour y parvenir.

   Créez un fichier nommé `proxyservice.exe.config` dans le dossier `%ProgramFiles%\Azure AD Password Protection Proxy\Service` avec le contenu suivant :

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

   Si votre proxy HTTP exige une authentification, ajoutez la balise useDefaultCredentials comme suit :

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

   Dans les deux cas, vous devez remplacer `http://yourhttpproxy.com:8080` par l’adresse et le port de votre serveur proxy HTTP spécifique.

   Si votre proxy HTTP est configuré avec une stratégie d’autorisation, l’accès doit être accordé au compte d’ordinateur Active Directory de l’ordinateur qui héberge le service proxy de protection par mot de passe Azure AD.

   Vous devez arrêter et redémarrer le service proxy de protection par mot de passe Azure AD après avoir créé ou mis à jour le fichier `proxyservice.exe.config`.

   Le service proxy de protection par mot de passe Azure AD ne prend pas en charge l’utilisation des informations d’identification spécifiques pour la connexion à un proxy HTTP.

7. Facultatif : Configurez le service proxy de protection par mot de passe Azure AD pour l’écoute sur un port spécifique.
   * RPC sur TCP est utilisé par le logiciel Agent DC de protection par mot de passe Azure AD sur les contrôleurs de domaine pour communiquer avec le service proxy de protection par mot de passe Azure AD. Par défaut, le service proxy de protection par mot de passe Azure AD est à l’écoute sur n’importe quel point de terminaison RPC dynamique disponible. Si nécessaire en raison de la topologie du réseau ou de la configuration du pare-feu, le service peut être configuré à la place pour écouter sur un port TCP spécifique.
      * Pour configurer le service afin qu’il s’exécute sous un port statique, utilisez la cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`.
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Vous devez arrêter et redémarrer le service pour que ces modifications prennent effet.

      * Pour configurer le service afin qu’il s’exécute sous un port dynamique, utilisez la même procédure mais redéfinissez StaticPort sur zéro, comme suit :
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Vous devez arrêter et redémarrer le service pour que ces modifications prennent effet.

   > [!NOTE]
   > Le service proxy de protection par mot de passe Azure AD nécessite un redémarrage manuel après tout changement de la configuration du port. Il n’est pas nécessaire de redémarrer le logiciel de service de l’agent DC en cours d’exécution sur les contrôleurs de domaine après avoir apporté des modifications de cette nature à la configuration.

   * La configuration actuelle du service peut être interrogée à l’aide de la cmdlet `Get-AzureADPasswordProtectionProxyConfiguration` comme le montre l’exemple suivant :

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Installer le service de l’agent DC de protection par mot de passe Azure AD

   Installez le service de l’agent DC de protection par mot de passe Azure AD à l’aide du package MSI `AzureADPasswordProtectionDCAgent.msi`.

   L’installation du logiciel nécessite un redémarrage lors de l’installation et la désinstallation en raison de l’exigence du système d’exploitation selon laquelle les DLL de filtre de mot de passe sont uniquement chargées ou déchargées après un redémarrage.

   L’installation du service de l’agent DC sur un ordinateur qui n’est pas encore un contrôleur de domaine n’est pas prise en charge. Dans ce cas, le service démarre et s’exécute, mais est inactif jusqu’à ce que l’ordinateur devienne un contrôleur de domaine.

   L’installation du logiciel peut être automatisée à l’aide de procédures MSI standard, par exemple :

   `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > L’exemple de commande msiexec ci-dessus entraîne un redémarrage immédiat. Cela peut être évité en spécifiant l’indicateur `/norestart`.

Une fois le logiciel Agent DC de protection par mot de passe Azure AD installé sur un contrôleur de domaine et redémarré, son installation est terminée. Aucune configuration supplémentaire n'est nécessaire ou possible.

## <a name="multiple-forest-deployments"></a>Déploiement de plusieurs forêts

Il n’y a aucune exigence supplémentaire pour déployer la protection par mot de passe Azure AD dans plusieurs forêts. Chaque forêt est configurée indépendamment comme décrit dans la section sur le déploiement de forêt unique. Chaque proxy de protection par mot de passe Azure AD ne peut prendre en charge que les contrôleurs de domaine de la forêt à laquelle il est joint. Le logiciel de protection par mot de passe Azure AD dans une forêt donnée n’a pas connaissance du logiciel de protection par mot de passe Azure AD déployé dans une autre forêt, quelles que soient les configurations d’approbation d’Active Directory.

## <a name="read-only-domain-controllers"></a>Contrôleurs de domaines en lecture seule

Les modifications/définitions de mot de passe ne sont jamais traitées et rendues persistantes sur les contrôleurs de domaine en lecture seule (RODC) ; au lieu de cela, elles sont transmises à des contrôleurs de domaine accessibles en écriture. Par conséquent, il n’est pas nécessaire d’installer le logiciel de l’agent DC sur le contrôleur de domaine en lecture seule.

## <a name="high-availability"></a>Haute disponibilité

La préoccupation principale pour assurer une haute disponibilité de la protection par mot de passe Azure AD est la disponibilité des serveurs proxy quand des contrôleurs de domaine dans une forêt tentent de télécharger les nouvelles stratégies ou d’autres données à partir d’Azure. Chaque agent DC utilise un algorithme de style tourniquet (round-robin) simple lorsqu’il décide que serveur proxy appeler et ignore les serveurs proxy qui ne répondent pas. Pour la majorité des déploiements Active Directory totalement connectés avec réplication saine (de l’annuaire et de l’état sysvol), deux (2) serveurs proxy doivent suffire pour garantir la disponibilité et donc les téléchargements en temps voulu de nouvelles stratégies et d’autres données. Des serveurs proxy supplémentaires peuvent être déployés, si vous le souhaitez.

Les problèmes habituels associés à la haute disponibilité sont atténués par la conception du logiciel de l’agent DC. L’agent DC gère un cache local de la stratégie de mot de passe la plus récemment téléchargée. Même si tous les serveurs proxy inscrits deviennent indisponibles pour une raison quelconque, les agents DC continuent d’appliquer leur stratégie de mot de passe mis en cache. Une fréquence de mise à jour raisonnable pour les stratégies de mot de passe dans un déploiement à grande échelle se compte généralement en jours, pas en heures, ni moins. Par conséquent, de brèves pannes des serveurs proxy n’ont pas d’impact significatif sur le fonctionnement de la fonctionnalité de protection de mot de passe Azure AD ou ses avantages en termes de sécurité.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez installé les services exigés pour la protection par mot de passe Azure AD sur vos serveurs locaux, effectuez la [configuration post-installation et collectez les informations de rapport](howto-password-ban-bad-on-premises-operations.md) pour achever votre déploiement.

[Vue d’ensemble conceptuelle de la protection par mot de passe Azure AD](concept-password-ban-bad-on-premises.md)
