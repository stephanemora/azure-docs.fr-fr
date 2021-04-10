---
title: Résoudre des problèmes liés à l’Agent Windows Virtual Desktop – Azure
description: Comment résoudre des problèmes courants liés à l’agent et à la connectivité.
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 86296385a0e657246e415f326261ce401e3cdeaf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604183"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>Résoudre des problèmes courants liés à l’Agent Windows Virtual Desktop

L’Agent Windows Virtual Desktop peut occasionner des problèmes de connexion en raison de plusieurs facteurs :
   - erreur sur le répartiteur conduisant l’agent à arrêter le service ;
   - problèmes liés aux mises à jour ;
   - problèmes liés à l’installation de l’agent qui interrompt la connexion à l’hôte de la session.

Cet article vous guide dans les solutions de ces scénarios courants et explique comment résoudre les problèmes de connexion.

>[!NOTE]
>Pour résoudre les problèmes liés à la connectivité de session et à l’agent Windows Virtual Desktop, nous vous recommandons de consulter les journaux des événements dans **Observateur d’événements** > **Journaux Windows** > **Application**. Recherchez les événements qui ont l’une des sources suivantes pour identifier votre problème :
>
>- Agent WVD
>- Programme de mise à jour de l’agent WVD
>- RDAgentBootLoader
>- MsiInstaller

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>Erreur : RDAgentBootLoader et/ou le chargeur de l’agent Bureau à distance ont cessé de fonctionner

Si vous constatez l’un des problèmes suivants, cela signifie que le chargeur de démarrage qui charge l’agent n’a pas pu installer celui-ci correctement, et que le service de l’agent n’est pas en cours d’exécution :
- **RDAgentBootLoader** est arrêté ou n’est pas en cours d’exécution.
- Il n’existe aucun état pour le **Chargeur de l’agent Bureau à distance**.

Pour résoudre ce problème, démarrez le chargeur de démarrage RDAgent :

1. Dans la fenêtre services, cliquez avec le bouton droit sur **Chargeur de l’agent Bureau à distance**.
2. Sélectionnez **Démarrer**. Si cette option est grisée pour vous, cela signifie que vous ne disposez pas des autorisations d’administrateur et que vous devez les obtenir pour démarrer le service.
3. Attendez 10 secondes, puis cliquez avec le bouton droit sur **Chargeur de l’agent Bureau à distance**.
4. Sélectionnez **Actualiser**.
5. Si le service s’arrête après que vous l’avez démarré et actualisé, il se peut que vous rencontriez un problème d’inscription. Pour plus d’informations, consultez [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token).

## <a name="error-invalid_registration_token"></a>Erreur : INVALID_REGISTRATION_TOKEN

Accédez à **Observateur d’événements** > **Journaux Windows** > **Application**. Si vous voyez un événement dont l’ID est 3277, et dont la description contient la chaîne **INVALID_REGISTRATION_TOKEN**, cela signifie que le jeton d’inscription que vous avez n’est pas reconnu comme étant valide.

Pour résoudre ce problème, créez un jeton d’inscription valide :

1. Pour créer un jeton d’inscription, suivez les étapes décrites dans la section [Générer une nouvelle clé d’inscription pour la machine virtuelle](#step-3-generate-a-new-registration-key-for-the-vm).
2. Ouvrez l’Éditeur du Registre. 
3. Accédez à **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **Microsoft** > **RDInfraAgent**.
4. Sélectionnez **IsRegistered**. 
5. Dans la zone de saisie **Données de valeur**, tapez **0**, puis cliquez sur **OK**. 
6. Sélectionnez **RegistrationToken**. 
7. Dans la zone de saisie **Données de valeur**, collez le jeton d’inscription recueilli à l’étape 1. 

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de IsRegistered 0](media/isregistered-token.png)

8. Ouvrez une invite de commandes en tant qu’administrateur.
9. Entrez **net stop RDAgentBootLoader**. 
10. Entrez **net start RDAgentBootLoader**. 
11. Ouvrez l’Éditeur du Registre.
12. Accédez à **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **Microsoft** > **RDInfraAgent**.
13. Vérifiez que **IsRegistered** a la valeur 1 et que rien ne figure dans la colonne de données pour **RegistrationToken**. 

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de IsRegistered 1](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form"></a>Erreur : l’agent ne peut pas se connecter au répartiteur avec INVALID_FORM

Accédez à **Observateur d’événements** > **Journaux Windows** > **Application**. Si vous voyez un événement dont l’ID est 3277 et dont la description contient la chaîne « INVALID_FORM », cela signifie qu’une erreur s’est produite lors de la communication entre l’agent et le répartiteur. L’agent ne peut pas se connecter au répartiteur ou atteindre une URL particulière en raison de certains paramètres de pare-feu ou DNS.

Pour résoudre ce problème, vérifiez que vous pouvez atteindre BrokerURI et BrokerURIGlobal :
1. Ouvrez l’Éditeur du Registre. 
2. Accédez à **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **Microsoft** > **RDInfraAgent**. 
3. Prenez note des valeurs de **BrokerURI** et **BrokerURIGlobal**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de BrokerURI et de BrokerURIGlobal](media/broker-uri.png)

 
4. Ouvrez un navigateur et accédez à *\<BrokerURI\>api/health*. 
   - Veillez à utiliser la valeur de **BrokerURI** recueillie à l’étape 3. Dans l’exemple de cette section, il s’agit de <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health>.
5. Ouvrez un autre onglet dans le navigateur, puis accédez à *\<BrokerURIGlobal\>api/health*. 
   - Veillez à utiliser la valeur de **BrokerURIGlobal** recueillie à l’étape 3. Dans l’exemple de cette section, il s’agit de <https://rdbroker.wvd.microsoft.com/api/health>.
6. Si le réseau ne bloque pas la connexion du répartiteur, les deux pages sont chargées avec succès et affichent le message **« RD Broker is Healthy »** comme dans les captures d’écran suivantes. 

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de l’accès réussi à l’URI du répartiteur chargé](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de l’accès réussi à l’URI global du répartiteur chargé](media/broker-global.png)
 

7. Si le réseau bloque la connexion du répartiteur, les pages ne sont pas chargées, comme dans la capture d’écran suivante. 

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de l’échec d’accès à l’URI du répartiteur chargé](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de l’échec d’accès à l’URI global du répartiteur chargé](media/unsuccessful-broker-global.png)

8. Si le réseau bloque ces URL, vous devez débloquer les URL requises. Pour plus d’informations, consultez [Liste des URL requises](safe-url-list.md).
9. Si cela ne résout pas votre problème, assurez-vous que vous n’avez pas de stratégie de groupe avec des chiffrements qui bloquent la connexion de l’agent au répartiteur. Windows Virtual Desktop utilise les mêmes chiffrements TLS 1.2 qu’[Azure Front Door](../frontdoor/front-door-faq.MD#what-are-the-current-cipher-suites-supported-by-azure-front-door). Pour plus d’informations, consultez [Sécurité de la connexion](network-connectivity.md#connection-security).

## <a name="error-3703"></a>Erreur : 3703

Accédez à **Observateur d’événements** > **Journaux Windows** > **Application**. Si vous voyez un événement dont l’ID est 3703 et dont la description contient la chaîne « RD Gateway Url: is not accessible », cela signifie que l’agent ne peut pas atteindre les URL de passerelle. Pour vous connecter correctement à votre hôte de session et autoriser le trafic réseau vers ces points de terminaison à ignorer les restrictions, vous devez débloquer les URL de la [Liste des URL requises](safe-url-list.md). Assurez-vous également que vos paramètres de pare-feu ou de proxy ne bloquent pas ces URL. Le déblocage de ces URL est nécessaire pour utiliser Windows Virtual Desktop.

Pour résoudre ce problème, vérifiez que vos paramètres de pare-feu et/ou de DNS ne bloquent pas ces URL :
1. [Utilisez le Pare-feu Azure pour protéger les déploiements de Windows Virtual Desktop.](../firewall/protect-windows-virtual-desktop.md)
2. Configurez vos [Paramètres DNS de Pare-feu Azure](../firewall/dns-settings.md)

## <a name="error-3019"></a>Erreur: 3019

Accédez à **Observateur d’événements** > **Journaux Windows** > **Application**. Si vous voyez un événement dont l’ID est 3019, cela signifie que l’agent ne peut pas atteindre les URL de transport de WebSocket. Pour vous connecter correctement à votre hôte de session et autoriser le trafic réseau à ignorer les restrictions, vous devez débloquer les URL répertoriées dans la [Liste des URL requises](safe-url-list.md). Collaborez avec l’équipe de mise en réseau Azure pour vous assurer que vos paramètres de pare-feu, de proxy et DNS ne bloquent pas ces URL. Vous pouvez également consulter les journaux de suivi du réseau pour identifier l’emplacement où le service Windows Virtual Desktop est bloqué. Si vous ouvrez une demande de support pour ce problème particulier, veillez à joindre vos journaux de suivi réseau.

## <a name="error-installationhealthcheckfailedexception"></a>Erreur : InstallationHealthCheckFailedException

Accédez à **Observateur d’événements** > **Journaux Windows** > **Application**. Si vous voyez un événement dont l’ID est 3277 et dont la description contient la chaîne « InstallationHealthCheckFailedException », cela signifie que l’écouteur de pile ne fonctionne pas, car le serveur Terminal Server a basculé la clé de Registre pour l’écouteur de pile.

Pour résoudre ce problème :
1. Vérifiez si [l’écouteur de pile fonctionne](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
2. Si l’écouteur de pile ne fonctionne pas, [désinstallez et réinstallez manuellement le composant de la pile](#error-vms-are-stuck-in-unavailable-or-upgrading-state).

## <a name="error-endpoint_not_found"></a>Erreur : ENDPOINT_NOT_FOUND

Accédez à **Observateur d’événements** > **Journaux Windows** > **Application**. Si vous voyez un événement dont l’ID est 3277 et dont la description contient la chaîne « ENDPOINT_NOT_FOUND », cela signifie que le répartiteur n’a pas trouvé de point de terminaison avec lequel établir une connexion. Ce problème de connexion peut se produire pour les raisons suivantes :

- Il n’y a pas de machines virtuelles dans votre pool d’hôtes
- Les machines virtuelles de votre pool d’hôtes ne sont pas actives
- Toutes les machines virtuelles de votre pool d’hôtes ont dépassé la limite de session maximale
- Le service d’agent n’est exécuté sur aucune machine virtuelle de votre pool d’hôtes

Pour résoudre ce problème :

1. Assurez-vous que la machine virtuelle est sous tension et qu’elle n’a pas été supprimée du pool d’hôtes.
2. Assurez-vous que la machine virtuelle n’a pas dépassé la limite de session maximale.
3. Assurez-vous que le [service d’agent est en cours d’exécution](#error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running) et que [l’écouteur de pile fonctionne](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
4. Assurez-vous que [l’agent peut se connecter au répartiteur](#error-agent-cannot-connect-to-broker-with-invalid_form).
5. Assurez-vous que [votre machine virtuelle dispose d’un jeton d’inscription valide](#error-invalid_registration_token).
6. Assurez-vous que [le jeton d’inscription de la machine virtuelle n’a pas expiré](faq.md#how-often-should-i-turn-my-vms-on-to-prevent-registration-issues). 

## <a name="error-installmsiexception"></a>Erreur : InstallMsiException

Accédez à **Observateur d’événements** > **Journaux Windows** > **Application**. Si vous voyez un événement dont l’ID est 3277, et dont la description contient la chaîne **InstallMsiException**, cela signifie que le programme d’installation est déjà en cours d’exécution pour une autre application pendant que vous tentez d’installer l’agent, ou qu’une stratégie empêche l’exécution du programme msiexec.exe.

Pour résoudre ce problème, désactivez la stratégie suivante :
   - Désactivez Windows Installer :  
      - Chemin d’accès de la catégorie : Configuration de l’ordinateur\Modèles d’administration\Composants Windows\Windows Installer
   
>[!NOTE]
>Cette liste ne répertorie pas la totalité des stratégies, mais uniquement celles que nous connaissons actuellement.

Pour désactiver une stratégie :
1. Ouvrez une invite de commandes en tant qu’administrateur.
2. Entrez et exécutez **rsop.msc**.
3. Dans la fenêtre **Jeu de stratégie résultant** qui s’affiche, accédez au chemin d’accès de la catégorie.
4. Sélectionnez la stratégie.
5. Sélectionnez **Désactivé**.
6. Sélectionnez **Appliquer**.   

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de la stratégie de Windows Installer dans Jeu de stratégie résultant](media/gpo-policy.png)

## <a name="error-win32exception"></a>Erreur : Win32Exception

Accédez à **Observateur d’événements** > **Journaux Windows** > **Application**. Si vous voyez un événement dont l’ID est 3277, et dont la description contient la chaîne **InstallMsiException**, cela signifie qu’une stratégie bloque le lancement de cmd.exe. Le blocage de ce programme vous empêche d’exécuter la fenêtre de console que vous devez utiliser pour redémarrer le service à chaque mise à jour de l’agent.

Pour résoudre ce problème, désactivez la stratégie suivante :
   - Empêcher l’accès à l’invite de commandes   
      - Chemin d’accès de la catégorie : Configuration utilisateur\Modèles d’administration\Système
    
>[!NOTE]
>Cette liste ne répertorie pas la totalité des stratégies, mais uniquement celles que nous connaissons actuellement.

Pour désactiver une stratégie :
1. Ouvrez une invite de commandes en tant qu’administrateur.
2. Entrez et exécutez **rsop.msc**.
3. Dans la fenêtre **Jeu de stratégie résultant** qui s’affiche, accédez au chemin d’accès de la catégorie.
4. Sélectionnez la stratégie.
5. Sélectionnez **Désactivé**.
6. Sélectionnez **Appliquer**.   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>Erreur : L’écouteur de pile ne fonctionne pas sur une machine virtuelle Windows 10 2004

Exécutez **qwinsta** à l’invite de commandes et notez le numéro de version qui s’affiche en regard de **rdp-sxs**. Si vous ne voyez pas **Listen** (Écouter ) affiché en regard des composants **rdp-tcp** et **rdp-sxs**, ou si ceux-ci ne s’affichent pas du tout après l’exécution de la commande **qwinsta**, cela signifie qu’il y a un problème de pile. Les mises à jour de pile sont installées en même temps que les mises à jour d’agent et, lorsque cette installation tourne mal, l’écouteur de Windows Virtual Desktop ne fonctionne pas.

Pour résoudre ce problème :
1. Ouvrez l’Éditeur du Registre.
2. Accédez à **HKEY_LOCAL_MACHINE** > **SYSTEM** > **CurrentControlSet** > **Control** > **Terminal Server** > **WinStations**.
3. Sous **WinStations**, il se peut que vous voyiez plusieurs dossiers pour différentes versions de pile, sélectionnez le dossier correspondant aux informations de version que vous avez vues lors de l’exécution de **qwinsta** dans votre invite de commandes.
4. Recherchez **fReverseConnectMode** et assurez-vous que sa valeur de données est **1**. Assurez-vous également que **fEnableWinStation** est défini sur **1**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de fReverseConnectMode](media/fenable-2.png)

5. Si **fReverseConnectMode** n’est pas défini sur **1**, sélectionnez **fReverseConnectMode** et entrez **1** dans son champ valeur. 
6. Si **fEnableWinStation** n’est pas défini sur **1**, sélectionnez **fEnableWinStation** et entrez **1** dans son champ valeur.
7. Redémarrez votre machine virtuelle. 

>[!NOTE]
>Pour modifier le mode **fReverseConnectMode** ou **fEnableWinStation** pour plusieurs machines virtuelles à la fois, vous pouvez effectuer l’une des deux opérations suivantes :
>
>- exporter la clé de Registre de la machine qui fonctionne déjà, et l’importer sur toutes les autres machines nécessitant cette modification ;
>- créer un objet de stratégie de groupe (GPO) qui définit la valeur de clé de Registre pour les machines nécessitant la modification.

7. Accédez à **HKEY_LOCAL_MACHINE** > **SYSTEM** > **CurrentControlSet** > **Control** > **Terminal Server** > **ClusterSettings**.
8. Sous **ClusterSettings**, recherchez **SessionDirectoryListener** et assurez-vous que sa valeur de données est **rdp-sxs...** .
9. Si **SessionDirectoryListener** n’est pas défini sur **rdp-sxs...** , vous devez suivre les étapes décrites dans la section [Désinstaller l’agent et le chargeur de démarrage](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) pour désinstaller l’agent, le chargeur de démarrage et les composants de la pile, puis [Réinstaller l’agent et le chargeur de démarrage](#step-4-reinstall-the-agent-and-boot-loader). Cela a pour effet de réinstaller la pile côte à côte.

## <a name="error-heartbeat-issue-where-users-keep-getting-disconnected-from-session-hosts"></a>Erreur : problème de pulsation avec des utilisateurs constamment déconnectés des hôtes de session

Si votre serveur ne détecte pas de pulsation du service Windows Virtual Desktop, vous devez modifier le seuil de pulsation. Suivez ces instructions si un ou plusieurs des scénarios suivants vous concernent :

- Vous recevez une erreur **CheckSessionHostDomainIsReachableAsync**
- Vous recevez une erreur **ConnectionBrokenMissedHeartbeatThresholdExceeded**
- Vous recevez une erreur **ConnectionEstablished:UnexpectedNetworkDisconnect**
- Les clients utilisateurs sont constamment déconnectés
- Les utilisateurs sont constamment déconnectés des hôtes de session

Pour modifier le seuil de pulsation :
1. Ouvrez une invite de commandes en tant qu’administrateur.
2. Entrez la commande **qwinsta** et exécutez-la.
3. Deux composants de pile doivent s’afficher : **rdp-tcp** et **rdp-sxs**. 
   - Selon la version du système d’exploitation que vous utilisez, **rdp-sxs** peut être suivi du numéro de build. Si c’est le cas, veillez à noter ce numéro pour une utilisation ultérieure.
4. Ouvrez l’Éditeur du Registre.
5. Accédez à **HKEY_LOCAL_MACHINE** > **SYSTEM** > **CurrentControlSet** > **Control** > **Terminal Server** > **WinStations**.
6. Sous **WinStations**, il est possible que plusieurs dossiers s’affichent pour différentes versions de la pile. Sélectionnez le dossier correspondant au numéro de version de l’étape 3.
7. Créez une valeur DWORD de registre en cliquant avec le bouton droit sur l’éditeur du Registre, puis en sélectionnant **Nouvelle** > **Valeur DWORD (32 bits)** . Lorsque vous créez la valeur DWORD, entrez les valeurs suivantes :
   - HeartbeatInterval : 10000
   - HeartbeatWarnCount : 30 
   - HeartbeatDropCount : 60 
8. Redémarrez votre machine virtuelle.

>[!NOTE]
>Si la modification du seuil de pulsation ne résout pas votre problème, vous avez peut-être un problème réseau sous-jacent nécessitant de contacter l’équipe de mise en réseau Azure.

## <a name="error-downloadmsiexception"></a>Erreur : DownloadMsiException

Accédez à **Observateur d’événements** > **Journaux Windows** > **Application**. Si vous voyez un événement dont l’ID est 3277, et dont la description contient la chaîne **DownloadMsiException**, cela signifie qu’il n’y a pas suffisamment d’espace sur le disque pour le RDAgent.

Pour résoudre ce problème, libérez de l’espace sur votre disque en procédant comme suit :
   - Supprimez les fichiers qui ne sont plus utilisés.
   - Augmentez la capacité de stockage de votre machine virtuelle.

## <a name="error-agent-fails-to-update-with-missingmethodexception"></a>Erreur : échec de la mise à jour de l’agent avec MissingMethodException

Accédez à **Observateur d’événements** > **Journaux Windows** > **Application**. Si vous voyez un événement dont l’ID est 3389 et dont la description contient la chaîne « MissingMethodException: Method not found », cela signifie que l’agent Windows Virtual Desktop n’a pas été mis à jour correctement et qu’il est revenu à une version antérieure. Cela peut être dû au fait que le numéro de version de .NET Framework actuellement installé sur vos machines virtuelles est inférieur à 4.7.2. Pour résoudre ce problème, vous devez mettre à niveau .NET vers la version 4.7.2 ou ultérieure en suivant les instructions d’installation de la [documentation .NET Framework](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).


## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>Erreur : Les machines virtuelles sont bloquées dans l’état Indisponible ou Mise à niveau

Ouvrez une fenêtre PowerShell en tant qu’administrateur, puis exécutez la cmdlet suivante :

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

Si l’état indiqué pour l’hôte ou les hôtes de session dans votre pool d’hôtes indique toujours « Indisponible » ou « Mise à niveau », l’agent ou la pile n’a pas été installé correctement.

Pour résoudre ce problème, réinstallez la pile côte à côte :
1. Ouvrez une invite de commandes en tant qu’administrateur.
2. Entrez **net stop RDAgentBootLoader**. 
3. Cliquez sur **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités**.
4. Désinstallez la dernière version de la **Pile réseau SxS des services Bureau à distance** ou la version indiquée dans **HKEY_LOCAL_MACHINE** > **SYSTEM** > **CurrentControlSet** > **Control** > **Terminal Server** > **WinStations** sous **ReverseConnectListener**.
5. Ouvrez une fenêtre de console en tant qu’administrateur et accédez à **Program Files** > **Microsoft RDInfra**.
6. Sélectionnez le composant **SxSStack** ou exécutez la commande **msiexec/i SxsStack-<version>.msi** pour installer le MSI.
8. Redémarrez votre machine virtuelle.
9. Revenez à l’invite de commandes, puis exécutez la commande **qwinsta**.
10. Vérifiez que **Listen** (Écouter) est affiché en regard du composant de la pile installé à l’étape 6.
   - Dans ce cas, entrez **net start RDAgentBootLoader** à l’invite de commandes et redémarrez votre machine virtuelle.
   - Si ce n’est pas le cas, vous devez [ré-inscrire votre machine virtuelle et réinstaller le composant agent](#your-issue-isnt-listed-here-or-wasnt-resolved).

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>Erreur : Connexion introuvable : RDAgent n’a pas de connexion active au répartiteur

Il se peut que vos machines virtuelles aient atteint leur limite de connexion, de sorte que la machine virtuelle ne peut pas accepter de nouvelles connexions.

Pour résoudre ce problème :
   - Abaissez la limite de session maximale. Cela garantit que les ressources sont réparties de façon plus homogène entre les hôtes de session, et empêchent l’épuisement des ressources.
   - Augmentez la capacité des ressources des machines virtuelles.

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>Erreur : Utilisation du système d’exploitation Pro VM ou d’un autre système d’exploitation non pris en charge

La pile côte à côte n’étant prise en charge que par les références (SKU) Windows Enterprise ou Windows Server, cela qui signifie que des systèmes d’exploitation tels que Pro VM ne le sont pas. Si vous ne disposez pas d’une référence (SKU) Windows Enterprise ou Windows Server, la pile sera installée sur votre machine virtuelle, mais ne sera pas activée. Vous ne la verrez donc pas s’afficher lors de l’exécution de la commande **qwinsta** dans votre ligne de commande.

Pour résoudre ce problème, créez une machine virtuelle Windows Enterprise ou Windows Server.
1. Accédez aux [détails de la machine virtuelle](create-host-pools-azure-marketplace.md#virtual-machine-details), puis suivez les étapes 1 à 12 pour configurer l’une des images recommandées suivantes :
   - Windows 10 Entreprise multisession, version 1909
   - Windows 10 Entreprise multisession, version 1909 + Microsoft 365 Apps
   - Windows Server 2019 Datacenter
   - Windows 10 Entreprise multisession, version 2004
   - Windows 10 Entreprise multisession, version 2004 + Microsoft 365 Apps
2. Sélectionnez **Examiner et créer**.

## <a name="error-name_already_registered"></a>Erreur : NAME_ALREADY_REGISTERED

Le nom de votre machine virtuelle a déjà été inscrit et est probablement un doublon.

Pour résoudre ce problème :
1. Suivez les étapes décrites dans la section [Supprimer l’hôte de session du pool d’hôtes](#step-2-remove-the-session-host-from-the-host-pool).
2. [Créez une autre machine virtuelle](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal). Veillez à choisir un nom unique pour cette machine virtuelle.
3. Accédez au [portail Azure](https://portal.azure.com) et ouvrez la page **Vue d’ensemble** du pool d’hôtes dans lequel se trouvait votre machine virtuelle. 
4. Ouvrez l’onglet **Hôtes de la session** et assurez-vous que tous les hôtes de la session se trouvent dans ce pool d’hôtes.
5. Attendez 5 à 10 minutes que l’état de l’hôte de session indique **Disponible**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de l’hôte de la session disponible](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>Votre problème n’est pas répertorié ici ou n’a pas été résolu

Si vous ne trouvez pas votre problème dans cet article ou si les instructions ne vous ont pas été utiles, nous vous recommandons de désinstaller, réinstaller et réinscrire l’agent Windows Virtual Desktop. Les instructions de cette section montrent comment réinscrire votre machine virtuelle auprès du service Windows Virtual Desktop en désinstallant tout agent, chargeur de démarrage et composant de pile, en supprimant l’hôte de la session du pool d’hôtes, en générant une nouvelle clé d’inscription pour la machine virtuelle, et en réinstallant l’agent et le chargeur de démarrage. Si un ou plusieurs des scénarios suivants s’appliquent à vous, suivez ces instructions :
- Votre machine virtuelle est bloquée dans l’état **Mise à niveau** ou **Indisponible**.
- Votre écouteur de pile ne fonctionne pas et vous opérez sur Windows 10 1809, 1903 ou 1904.
- Vous recevez une erreur **EXPIRED_REGISTRATION_TOKEN**.
- Vous ne voyez pas vos machines virtuelles apparaître dans la liste des hôtes de la session.
- Vous ne voyez pas le **chargeur de l’agent Bureau à distance** dans la fenêtre Services.
- Vous ne voyez pas le composant **RdAgentBootLoader** dans le gestionnaire de tâches.
- Vous recevez une erreur **Le répartiteur de connexion n’a pas pu valider les paramètres** sur les machines virtuelles avec image personnalisée
- Les instructions de cet article n’ont pas résolu votre problème.

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>Étape 1 : Désinstaller tout agent, chargeur de démarrage et programme de composant de pile

Avant de réinstaller l’agent, le chargeur de démarrage et la pile, vous devez désinstaller tout programme de composant existant de votre machine virtuelle. Pour désinstaller tout agent, chargeur de démarrage et programme de composant de pile :
1. Connectez-vous à votre machine virtuelle en tant qu’administrateur. 
2. Cliquez sur **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités**.
3. Supprimez les programmes suivants :
   - Chargeur de démarrage de l’agent Bureau à distance
   - Agent d’infrastructure des services Bureau à distance
   - Agent Geneva d’infrastructure des services Bureau à distance
   - Ple réseau SxS des services Bureau à distance
   
>[!NOTE]
>Vous pouvez voir plusieurs instances de ces programmes. Veillez à les supprimer toutes.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de la désinstallation des programmes](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>Étape 2 : Supprimer l’hôte de la session du pool d’hôtes

Lorsque vous supprimez l’hôte de la session du pool d’hôtes, l’hôte de la session n’est plus inscrit auprès de ce pool d’hôtes. Cela a pour effet de réinitialiser l’inscription de l’hôte de la session. Pour supprimer l’hôte de la session du pool d’hôtes :
1. Accédez à la page **Vue d’ensemble** pour le pool d’hôtes dans lequel se trouve votre machine virtuelle, dans le [portail Azure](https://portal.azure.com). 
2. Accédez à l’onglet **Hôtes de la session** pour afficher la liste de tous les hôtes de la session dans ce pool d’hôtes.
3. Examinez la liste des hôtes de la session et sélectionnez la machine virtuelle que vous souhaitez supprimer.
4. Sélectionnez **Supprimer**.  

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de la suppression d’une machine virtuelle du pool d’hôtes](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>Étape 3 : Générer une nouvelle clé d’inscription pour la machine virtuelle

Vous devez générer une nouvelle clé d’inscription à utiliser pour réinscrire votre machine virtuelle auprès du pool d’hôtes et du service. Pour générer une nouvelle clé d’inscription pour la machine virtuelle :
1. Ouvrez le [portail Azure](https://portal.azure.com) et accédez à la page **Vue d’ensemble** pour le pool d’hôtes de la machine virtuelle que vous souhaitez modifier.
2. Sélectionnez **Clé d’inscription**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de la clé d’inscription dans le portail](media/reg-key.png)

3. Ouvrez l’onglet **Clé d’inscription** et sélectionnez **Générer une nouvelle clé**.
4. Sélectionnez la date d’expiration, puis **OK**.  

>[!NOTE]
>La date d’expiration doit être postérieure d’au moins une heure et d’au plus 27 jours à la date et l’heure de sa génération. Nous vous recommandons vivement de définir la date d’expiration sur le maximum de 27 jours.

5. Copiez la clé qui vient d’être générée dans votre Presse-papiers. Vous en aurez besoin plus tard.

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>Étape 4 : Réinstaller l’agent et le chargeur de démarrage

La réinstallation de la version la plus récente de l’agent et du chargeur de démarrage a également pour effet d’installer automatiquement la pile côte à côte et l’agent de surveillance Geneva. Pour réinstaller l’agent et le chargeur de démarrage :
1. Connectez-vous à votre machine virtuelle en tant qu’administrateur et utilisez la version correcte du programme d’installation de l’agent pour votre déploiement en fonction de la version de Windows exécutée par votre machine virtuelle. Si vous avez une machine virtuelle Windows 10, suivez les instructions fournies dans [Inscrire des machines virtuelles](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) pour télécharger **l’Agent Windows Virtual Desktop** et le **Chargeur de démarrage de l’agent Windows Virtual Desktop**. Si vous avez une machine virtuelle Windows 7, suivez les étapes 13 à 14 dans [Inscrire des machines virtuelles](deploy-windows-7-virtual-machine.md#configure-a-windows-7-virtual-machine) pour télécharger **l’Agent Windows Virtual Desktop** et **l’Agent Manager Windows Virtual Desktop**.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de la page de téléchargement de l’agent et du chargeur de démarrage](media/download-agent.png)

2. Cliquez avec le bouton droit sur les programmes d’installation de l’agent et du chargeur de démarrage que vous avez téléchargés.
3. Sélectionner **Propriétés**.
4. Sélectionnez **Débloquer**.
5. Sélectionnez **OK**.
6. Exécutez le programme d'installation de l'agent.
7. Quand le programme d’installation vous demande le jeton d’inscription, collez la clé d’inscription que vous avez copiée dans votre Presse-papiers. 

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du jeton d’inscription collé](media/pasted-agent-token.png)

8. Exécutez le programme d’installation du chargeur de démarrage.
9. Redémarrez votre machine virtuelle. 
10. Accédez au [portail Azure](https://portal.azure.com) et ouvrez la page **Vue d’ensemble** du pool d’hôtes auquel appartient votre machine virtuelle.
11. Accédez à l’onglet **Hôtes de la session** pour afficher la liste de tous les hôtes de la session dans ce pool d’hôtes.
12. Vous devez maintenant voir l’hôte de la session inscrit dans le pool d’hôtes avec l’état **Disponible**. 

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de l’hôte de la session disponible](media/hostpool-portal.png)

## <a name="next-steps"></a>Étapes suivantes

Si le problème persiste, créez un cas de support en incluant des informations détaillées sur le problème rencontré et les actions que vous avez tentées pour le résoudre. La liste suivante comprend d’autres ressources que vous pouvez utiliser pour résoudre des problèmes liés à votre déploiement de Windows Virtual Desktop.

- Pour découvrir une vue d’ensemble de la résolution des problèmes Windows Virtual Desktop et des procédures d’escalade, consultez l’article [Vue d’ensemble du dépannage, commentaires et support](troubleshoot-set-up-overview.md).
- Pour résoudre les problèmes de création d’un pool d’hôtes dans un environnement Windows Virtual Desktop, consultez [Création d’un environnement et d’un pool d’hôtes](troubleshoot-set-up-issues.md).
- Pour résoudre les problèmes de configuration d’une machine virtuelle dans Windows Virtual Desktop, consultez [Configuration d’une machine virtuelle hôte de session](troubleshoot-vm-configuration.md).
- Pour résoudre les problèmes de connexion au client Windows Virtual Desktop, consultez [Connexions au service Windows Virtual Desktop](troubleshoot-service-connection.md).
- Pour résoudre des problèmes liés aux clients Bureau à distance, consultez [Résoudre des problèmes du client Bureau à distance](troubleshoot-client.md).
- Pour résoudre les problèmes d’utilisation de PowerShell avec Windows Virtual Desktop, consultez [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Pour plus d’informations sur le service, consultez [Environnement Windows Virtual Desktop](environment-setup.md).
- Suivez le [Didacticiel : Résoudre les problèmes liés aux déploiements de modèles Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](../azure-resource-manager/templates/deployment-history.md).
