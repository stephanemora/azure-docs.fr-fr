---
title: Déployer la protection de mot de passe Azure AD (préversion)
description: Déployer la protection de mot de passe Azure AD (préversion) pour interdire les mots de passe incorrects en local
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 5065399f161bcaee2f9518236a28f0f5faa0ea5b
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902025"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Préversion : Déployer la protection de mot de passe Azure AD

|     |
| --- |
| La protection de mot de passe Azure AD et la liste de mots de passe interdits personnalisée sont des fonctionnalités en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Maintenant que nous comprenons [comment appliquer la protection de mot de passe Azure AD pour Windows Server Active Directory](concept-password-ban-bad-on-premises.md), l’étape suivante consiste à planifier et à exécuter le déploiement.

## <a name="deployment-strategy"></a>Stratégie de déploiement

Microsoft recommande que n’importe quel déploiement démarre en mode d’audit. Le mode d’audit est le paramètre initial par défaut où les mots de passe peuvent continuer à être définis et tout mot de passe qui serait bloqué donne lieu à une entrée dans le journal des événements. Une fois que les serveurs proxy et les agents DC sont entièrement déployés en mode d’audit, un monitoring régulier doit être effectué afin de déterminer l’impact de la mise en œuvre de la stratégie de mot de passe sur les utilisateurs et l’environnement si elle était appliquée.

Durant la phase d’audit, de nombreuses organisations constatent ce qui suit :

* Elles doivent améliorer les processus opérationnels existants pour utiliser des mots de passe plus sécurisés.
* Les utilisateurs sont habitués à choisir régulièrement des mots de passe non sécurisés.
* Elles doivent informer les utilisateurs des modifications de la sécurité à venir, l’impact que cela peut avoir sur eux et les aider à mieux comprendre comment choisir des mots de passe plus sécurisés.

Une fois que la fonctionnalité a été exécutée en mode d’audit pendant une durée raisonnable, la configuration de l’application peut passer de **Audit** à **Appliquer**, nécessitant donc des mots de passe plus sécurisés. Durant cette période, effectuer un monitoring strict est une bonne idée.

## <a name="known-limitation"></a>Limitation connue

Il existe une limitation connue dans la préversion du proxy de protection de mot de passe Azure AD. L’utilisation de comptes administrateur de locataire qui requièrent l’authentification multifacteur n’est pas prise en charge. Une mise à jour ultérieure du proxy de protection de mot de passe Azure AD prendra en charge l’inscription du proxy avec les comptes administrateur qui nécessitent l’authentification multifacteur.

## <a name="single-forest-deployment"></a>Déploiement de forêt unique

La préversion de la protection de mot de passe Azure AD est déployée avec le service de proxy sur deux serveurs maximum, et le service de l’agent DC peut être déployé de façon incrémentielle sur tous les contrôleurs de domaine dans la forêt Active Directory.

![Comment les composants de protection de mot de passe Azure AD fonctionnent ensemble](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>Télécharger le logiciel

Deux programmes d’installation sont requis pour la protection de mot de passe Azure AD. Vous pouvez les télécharger depuis le [centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Installer et configurer le service de proxy de protection de mot de passe Azure AD

1. Choisissez un ou plusieurs serveurs pour héberger le service de proxy de protection de mot de passe Azure AD.
   * Chacun de ces services peut uniquement fournir des stratégies de mot de passe pour une seule forêt, et l’ordinateur hôte doit être joint à un domaine (la racine et l’enfant sont tous deux également pris en charge) dans cette forêt. Pour que le service de proxy de protection de mot de passe Azure AD fonctionne comme prévu, il doit exister une connectivité réseau entre au moins un contrôleur de domaine dans chaque domaine de la forêt et l’ordinateur hôte du proxy de protection de mot de passe Azure AD.
   * Il est possible d’installer et d’exécuter le service de proxy de protection de mot de passe Azure AD sur un contrôleur de domaine à des fins de test, mais ensuite une connexion Internet est requise.

   > [!NOTE]
   > La préversion publique prend en charge un maximum de deux (2) serveurs proxy par forêt.

2. Installez le logiciel du service de proxy de la stratégie de mot de passe à l’aide du package MSI AzureADPasswordProtectionProxy.msi.
   * L’installation du logiciel ne nécessite pas un redémarrage. L’installation du logiciel peut être automatisée à l’aide de procédures MSI standard, par exemple : `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

3. Ouvrez une fenêtre PowerShell en tant qu’administrateur.
   * Le logiciel du proxy de protection de mot de passe Azure AD inclut un nouveau module PowerShell nommé AzureADPasswordProtection. Les étapes suivantes sont basées sur l’exécution de diverses cmdlets à partir de ce module PowerShell et supposent que vous avez ouvert une nouvelle fenêtre PowerShell et importé le nouveau module comme suit :
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > Le logiciel d’installation modifie la variable d’environnement PSModulePath de l’ordinateur hôte. Afin que cette modification prenne effet, pour que le module PowerShell AzureADPasswordProtection puisse être importé et utilisé, vous devrez peut-être ouvrir une nouvelle fenêtre de console PowerShell.

   * Vérifiez que le service est en cours d’exécution à l’aide de la commande PowerShell suivante : `Get-Service AzureADPasswordProtectionProxy | fl`.
      * Le résultat doit être que **l’état** retourne le résultat « En cours d’exécution ».

4. Enregistrez le proxy.
   * Une fois l’étape 3 effectuée, le service de proxy de protection de mot de passe Azure AD est en cours d’exécution sur l’ordinateur, mais n’a pas encore les informations d’identification nécessaires pour communiquer avec Azure AD. L’inscription auprès d’Azure AD est nécessaire pour activer cette capacité à l’aide de la cmdlet PowerShell `Register-AzureADPasswordProtectionProxy`. La cmdlet requiert des informations d'identification d’administrateur global pour votre locataire Azure, ainsi que des privilèges d’administrateur de domaine Active Directory local dans le domaine racine de la forêt. Une fois cette opération réussie pour un service de proxy donné, des appels supplémentaires de `Register-AzureADPasswordProtectionProxy` continuent de réussir, mais ne sont pas nécessaires.
      * La cmdlet peut être exécutée comme suit :

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         L’exemple fonctionne uniquement si l’utilisateur actuellement connecté est également un administrateur de domaine Active Directory pour le domaine racine. Une alternative consiste à fournir les informations d’identification de domaine nécessaires via le paramètre `-ForestCredential`.

   > [!TIP]
   > Il peut y avoir un retard important (plusieurs secondes) la première fois que cette cmdlet est exécutée pour un locataire Azure donné avant la fin d’exécution de la cmdlet. Sauf si une erreur est signalée, ce délai ne doit pas être considéré comme inquiétant.

   > [!NOTE]
   > L’inscription du service de proxy de protection de mot de passe Azure AD est censée être une étape unique au cours de la durée de vie du service. Le service de proxy effectue automatiquement toutes les autres tâches de maintenance nécessaires à partir de là. Une fois cette opération réussie pour une forêt donnée, des appels supplémentaires de Register-AzureADPasswordProtectionProxy continuent de réussir, mais ne sont pas nécessaires.

5. Inscrivez la forêt.
   * La forêt Active Directory locale doit être initialisée avec les informations d’identification nécessaires pour communiquer avec Azure à l’aide de la cmdlet Powershell `Register-AzureADPasswordProtectionForest`. La cmdlet requiert des informations d'identification d’administrateur global pour votre locataire Azure, ainsi que des privilèges d’administrateur de domaine Active Directory local dans le domaine racine de la forêt. Cette étape est exécutée une seule fois par forêt.
      * La cmdlet peut être exécutée comme suit :

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         L’exemple fonctionne uniquement si l’utilisateur actuellement connecté est également un administrateur de domaine Active Directory pour le domaine racine. Une alternative consiste à fournir les informations d’identification de domaine nécessaires via le paramètre -ForestCredential.

         > [!NOTE]
         > Si plusieurs serveurs proxy sont installés dans votre environnement, peu importe le serveur proxy spécifié dans la procédure ci-dessus.

         > [!TIP]
         > Il peut y avoir un retard important (plusieurs secondes) la première fois que cette cmdlet est exécutée pour un locataire Azure donné avant la fin d’exécution de la cmdlet. Sauf si une erreur est signalée, ce délai ne doit pas être considéré comme inquiétant.

   > [!NOTE]
   > L’inscription de la forêt Active Directory est censée être une étape unique au cours de la durée de vie de la forêt. Les agents du contrôleur de domaine qui s’exécutent dans la forêt effectuent automatiquement toutes les autres tâches de maintenance nécessaires à partir de là. Une fois cette opération réussie pour une forêt donnée, des appels supplémentaires de `Register-AzureADPasswordProtectionForest` continuent de réussir, mais ne sont pas nécessaires.

6. Facultatif : configurez le service de proxy de protection de mot de passe Azure AD pour l’écoute sur un port spécifique.
   * RPC sur TCP est utilisé par le logiciel Agent DC de protection de mot de passe Azure AD sur les contrôleurs de domaine pour communiquer avec le service de proxy de protection de mot de passe Azure AD. Par défaut, le service Proxy de la stratégie de mot de passe de protection de mot de passe Azure AD est à l’écoute sur n’importe quel point de terminaison RPC dynamique disponible. Si nécessaire en raison de la topologie du réseau ou de la configuration du pare-feu, le service peut être configuré à la place pour écouter sur un port TCP spécifique.
      * Pour configurer le service afin qu’il s’exécute sous un port statique, utilisez la cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Vous devez arrêter et redémarrer le service pour que ces modifications prennent effet.

      * Pour configurer le service afin qu’il s’exécute sous un port dynamique, utilisez la même procédure mais redéfinissez StaticPort sur zéro, comme suit :
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Vous devez arrêter et redémarrer le service pour que ces modifications prennent effet.

   > [!NOTE]
   > Le service de proxy de protection de mot de passe Azure AD nécessite un redémarrage manuel après toute modification de la configuration du port. Il n’est pas nécessaire de redémarrer le logiciel de service de l’agent DC en cours d’exécution sur les contrôleurs de domaine après avoir apporté des modifications de cette nature à la configuration.

   * La configuration actuelle du service peut être interrogée à l’aide de la cmdlet `Get-AzureADPasswordProtectionProxyConfiguration` comme le montre l’exemple suivant :

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Installer le service de l’agent DC de protection de mot de passe Azure AD

* Installez le service de l’agent DC de protection de mot de passe Azure AD à l’aide du package MSI `AzureADPasswordProtectionDCAgent.msi` :
   * L’installation du logiciel nécessite un redémarrage lors de l’installation et la désinstallation en raison de l’exigence du système d’exploitation selon laquelle les DLL de filtre de mot de passe sont uniquement chargées ou déchargées après un redémarrage.
   * L’installation du service de l’agent DC sur un ordinateur qui n’est pas encore un contrôleur de domaine n’est pas prise en charge. Dans ce cas, le service démarre et s’exécute, mais est inactif jusqu’à ce que l’ordinateur devienne un contrôleur de domaine.

   L’installation du logiciel peut être automatisée à l’aide de procédures MSI standard, par exemple : `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > L’exemple de commande msiexec entraîne un redémarrage immédiat. Ceci peut être évité en spécifiant l’indicateur `/norestart`.

Une fois installé sur un contrôleur de domaine et redémarré, l’installation du logiciel Agent DC de protection de mot de passe Azure AD est terminée. Aucune configuration supplémentaire n'est nécessaire ou possible.

## <a name="multiple-forest-deployments"></a>Déploiement de plusieurs forêts

Il n’y a aucune configuration supplémentaire requise pour déployer la protection de mot de passe Azure AD dans plusieurs forêts. Chaque forêt est configurée indépendamment comme décrit dans la section sur le déploiement de forêt unique. Chaque proxy de protection de mot de passe Azure AD ne peut prendre en charge que les contrôleurs de domaine de la forêt à laquelle il est joint. Le logiciel de protection de mot de passe Azure AD dans une forêt donnée n’a pas connaissance du logiciel de protection de mot de passe Azure AD déployé dans une autre forêt, indépendamment des configurations d’approbation d’Active Directory.

## <a name="read-only-domain-controllers"></a>Contrôleurs de domaines en lecture seule

Les modifications/définitions de mot de passe ne sont jamais traitées et rendues persistantes sur les contrôleurs de domaine en lecture seule (RODC) ; au lieu de cela, elles sont transmises à des contrôleurs de domaine accessibles en écriture. Par conséquent, il n’est pas nécessaire d’installer le logiciel de l’agent DC sur le contrôleur de domaine en lecture seule.

## <a name="high-availability"></a>Haute disponibilité

La préoccupation principale pour assurer une disponibilité élevée de la protection de mot de passe Azure AD est la disponibilité des serveurs proxy, lorsque des contrôleurs de domaine dans une forêt tentent de télécharger les nouvelles stratégies ou d’autres données à partir d’Azure. La préversion publique prend en charge un maximum de deux serveurs proxy par forêt. Chaque agent DC utilise un algorithme de style tourniquet (round-robin) simple lorsqu’il décide que serveur proxy appeler et ignore les serveurs proxy qui ne répondent pas.
Les problèmes habituels associés à la haute disponibilité sont atténués par la conception du logiciel de l’agent DC. L’agent DC gère un cache local de la stratégie de mot de passe la plus récemment téléchargée. Même si tous les serveurs proxy inscrits deviennent indisponibles pour une raison quelconque, les agents DC continuent d’appliquer leur stratégie de mot de passe mis en cache. Une fréquence de mise à jour raisonnable pour les stratégies de mot de passe dans un déploiement à grande échelle se compte généralement en jours, pas en heures, ni moins.   Par conséquent, de brèves pannes des serveurs proxy n’ont pas d’impact significatif sur le fonctionnement de la fonctionnalité de protection de mot de passe Azure AD ou ses avantages en termes de sécurité.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez installé les services requis pour la protection de mot de passe Azure AD sur vos serveurs locaux, terminez la [configuration post-installation et collectez les informations de rapport](howto-password-ban-bad-on-premises-operations.md) pour achever votre déploiement.

[Vue d’ensemble conceptuelle de la protection de mot de passe Azure AD](concept-password-ban-bad-on-premises.md)
