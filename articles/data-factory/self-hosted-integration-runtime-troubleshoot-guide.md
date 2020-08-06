---
title: Résoudre les problèmes du runtime d’intégration auto-hébergé dans Azure Data Factory
description: Découvrez comment résoudre les problèmes liés au runtime d’intégration auto-hébergé dans Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 07/19/2020
ms.author: abnarain
ms.openlocfilehash: 521756081db938e749849e6f3630dbd60700d24f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023824"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Résoudre les problèmes liés au runtime d’intégration auto-hébergé

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés au runtime d'intégration auto-hébergé dans Azure Data Factory.

## <a name="gather-self-hosted-integration-runtime-logs-from-azure-data-factory"></a>Collecter les journaux du runtime d’intégration auto-hébergé auprès d’Azure Data Factory

Azure Data Factory prend en charge l’affichage et le chargement des journaux d’erreurs pour les activités qui n’ont pas été exécutées sur l’IR auto-hébergé ou partagé. Vous pouvez suivre les étapes ci-dessous pour obtenir l’ID du rapport d’erreur, puis saisir l’ID du rapport pour localiser les problèmes connexes connus.

1. Accédez à la page **Exécutions d’activités**.

1. Sous la colonne **ERREUR**, cliquez sur le bouton ci-dessous.

    ![Page Exécutions d’activités](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Vous verrez les journaux relatifs à l’exécution de l’activité qui a échoué. Cliquez sur le bouton **Envoyer les journaux** pour obtenir de l’aide.

    ![Envoyer les journaux](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Vous pouvez choisir les journaux que vous souhaitez envoyer. Pour *IR auto-hébergé*, vous pouvez charger les journaux relatifs à l’activité ayant échoué ou tous les journaux sur le nœud de l’IR auto-hébergé. Pour *IR partagé*, vous pouvez uniquement charger les journaux relatifs à l’activité ayant échoué.

    ![Choisir les journaux](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Lorsque les journaux sont chargés, conservez une trace de l’ID du rapport au cas où vous auriez besoin d’une aide supplémentaire pour résoudre le problème.

    ![Charger les journaux](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Les requêtes de consultation et de chargement des journaux seront exécutées sur toutes les instances de l’IR auto-hébergé en ligne. Assurez-vous que toutes les instances de l’IR auto-hébergé sont en ligne en cas de journal manquant. 


## <a name="common-errors-and-resolutions"></a>Problèmes courants et leur résolution

### <a name="error-message"></a>Message d’erreur : 

`Self-hosted integration runtime can't connect to cloud service`

![Problème de connexion de l’IR auto-hébergé](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Cause 

Le runtime d’intégration auto-hébergé ne peut pas se connecter au service Data Factory (backend). Ce problème est généralement dû à des paramètres réseau dans le pare-feu.

#### <a name="resolution"></a>Résolution

1. Vérifiez si le service du runtime d’intégration est en cours d’exécution.
    
   ![État d’exécution du service d’IR auto-hébergé](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Si le service est en cours d'exécution, passez à l'étape 3.

1. Si aucun proxy n’est configuré sur le runtime d’intégration auto-hébergé (qui est le paramètre par défaut), exécutez la commande PowerShell suivante sur l’ordinateur sur lequel est installé le runtime d’intégration auto-hébergé :

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > L’URL du service peut varier en fonction de votre emplacement Data Factory. L’URL du service se trouve sous **UI ADF** > **Connexions** > **Runtimes d’intégration** >  **Modifier l’IR auto-hébergé** > **Nœuds** > **Afficher les URL du service**.
            
    Voici la réponse attendue :
            
    ![Réponse de la commande PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Si vous ne recevez pas la réponse attendue, utilisez l’une des méthodes suivantes en fonction de votre situation :
            
    * Si vous recevez le message « le nom distant n’a pas pu être résolu », il existe un problème DNS (Domain Name System). Contactez votre équipe réseau pour résoudre ce problème.
    * Si vous recevez un message « certificat SSL/TLS non approuvé », vérifiez si le certificat de https://wu2.frontend.clouddatahub.net/ est approuvé sur l’ordinateur, puis installez le certificat public à l’aide du gestionnaire de certificats. Cette action doit atténuer le problème.
    * Accédez à **Windows** > **Visionneur d’événements (journaux)**  > **Journaux des applications et des services** > **Runtime d’intégration** et recherchez les défaillances provoquées par DNS, une règle de pare-feu ou des paramètres réseau d’entreprise. (Si vous trouvez une de ces défaillances, forcez la fermeture de la connexion.) Étant donné que chaque entreprise a personnalisé des paramètres réseau, contactez votre équipe réseau pour résoudre ces problèmes.

1. Si « proxy » a été configuré sur le runtime d’intégration auto-hébergé, vérifiez que votre serveur proxy est en mesure d’accéder à notre point de terminaison de service. Pour obtenir un exemple de commande, consultez [PowerShell, les requêtes Web et les proxys](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Voici la réponse attendue :
            
![Réponse de la commande PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considérations liées au proxy :
> *    Vérifiez si le serveur proxy doit être placé dans la liste des destinataires approuvés. Dans ce cas, assurez-vous que [ces domaines](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) figurent dans la liste des destinataires approuvés.
> *    Vérifiez si le certificat TLS/SSL « wu2.frontend.clouddatahub.net/ » est approuvé sur le serveur proxy.
> *    Si vous utilisez l’authentification Active Directory sur le proxy, remplacez le compte de service par le compte d’utilisateur qui peut accéder au proxy en tant que « Service Integration Runtime ».

### <a name="error-message"></a>Message d’erreur : 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

#### <a name="cause"></a>Cause 

Le nœud Runtime intégré auto-hébergé peut avoir un État **inactif**, comme indiqué dans la capture d’écran suivante :

![Nœud du runtime d’intégration auto-hébergé inactif](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Ce comportement se produit lorsque les nœuds ne peuvent pas communiquer entre eux.

#### <a name="resolution"></a>Résolution

1. Connectez-vous à la machine virtuelle hébergée sur un nœud. Sous **Journaux des applications et des services** > **Runtime d’intégration**, ouvrez Observateur d’événements, puis filtrez tous les journaux d’erreurs.

1. Vérifiez si un journal des erreurs contient l’erreur suivante : 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Si vous rencontrez cette erreur, exécutez la commande suivante sur une ligne de commande : 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Si vous recevez l’erreur suivante, contactez votre service informatique pour obtenir de l’aide pour résoudre ce problème. Lorsque vous parvenez à utiliser Telnet, contactez le support Microsoft si vous rencontrez toujours des problèmes pour l’état du nœud IR d’intégration.
        
   ![Erreur de ligne de commande](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Vérifiez si le journal des erreurs contient ce qui suit :

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Pour résoudre le problème, essayez l’une des méthodes suivantes, voire les deux :
    - Placez tous les nœuds dans le même domaine.
    - Ajoutez l’adresse IP au mappage de l’hôte dans tous les fichiers hôtes de la machine virtuelle hébergée.


## <a name="troubleshoot-connectivity-issue"></a>Résoudre un problème de connectivité

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Résoudre un problème de connectivité entre le runtime d’intégration auto-hébergé et Data Factory ou le runtime d’intégration auto-hébergé et la source de données/le récepteur

Pour résoudre le problème de connectivité réseau, vous devez savoir comment [collecter la trace réseau](#how-to-collect-netmon-trace), comprendre comment l’utiliser et [analyser la trace netmon](#how-to-analyze-netmon-trace) avant d’appliquer les outils Netmon dans des cas réels à partir du runtime d’intégration auto-hébergé.

Parfois, lorsque nous dépannons des problèmes de connectivité comme le suivant entre le runtime d’intégration auto-hébergé et Data Factory : 

![Échec de la requête HTTP](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Ou un problème entre le runtime d’intégration auto-hébergé et la source de données/le récepteur, nous rencontrons les erreurs suivantes :

**Message d’erreur :** 
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**Message d’erreur :** 
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**Résolution :** Lorsque vous rencontrez les problèmes ci-dessus, reportez-vous aux instructions de résolution suivantes :

Prenez la trace netmon et analysez-la plus en détail.
- Tout d’abord, vous pouvez définir le filtre pour voir toute réinitialisation à partir du serveur vers le côté client. Dans l’exemple ci-dessous, vous pouvez voir que le côté serveur correspond au serveur Data Factory.

    ![Serveur Data Factory](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Lorsque vous obtenez le package de réinitialisation, vous pouvez trouver la conversation en suivant le protocole TCP.

    ![Rechercher la conversation](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Vous pouvez ensuite obtenir la conversation entre le client et le serveur Data Factory ci-dessous en supprimant le filtre.

    ![Obtenir la conversation](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- En fonction de la trace netmon collectée, nous pouvons dire que la durée de vie (TimeToLive) totale est 64. D’après les **valeurs de durée de vie et de limite de tronçon par défaut** mentionnées dans [cet article](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (comme extrait ci-dessous), nous pouvons voir que le système Linux réinitialise le package et provoque la déconnexion.

    Les valeurs de durée de vie et de limite de tronçon par défaut varient entre différents systèmes d’exploitation. Voici les valeurs par défaut pour certains d’entre-eux :
    - Noyau Linux 2.4 (circa 2001) : 255 pour TCP, UDP et ICMP
    - Noyau Linux 4.10 (2015) : 64 pour TCP, UDP et ICMP
    - Windows XP (2001) : 128 pour TCP, UDP et ICMP
    - Windows 10 (2015) : 128 pour TCP, UDP et ICMP
    - Windows Server 2008 : 128 pour TCP, UDP et ICMP
    - Windows Server 2019 (2018) : 128 pour TCP, UDP et ICMP
    - macOS (2001) : 64 pour TCP, UDP et ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    Toutefois, la durée de vie affichée est de 61 au lieu de 64 dans l’exemple ci-dessus, car lorsque le package réseau atteint la destination, il doit passer par différents tronçons tels que des routeurs/périphériques réseau. Le nombre de routeurs/périphériques réseau sera déduit dans la durée de vie (TTL) finale.
    Dans ce cas, nous pouvons voir que la réinitialisation (Reset) peut être envoyée à partir du système Linux avec TTL 64.

- Nous devons vérifier le quatrième tronçon du runtime d’intégration auto-hébergé pour confirmer l’origine de l’appareil de réinitialisation.
 
    *Package réseau provenant du système Linux A avec TTL 64 -> B TTL 64 moins 1 = 63 -> C TTL 63 moins 1 = 62 -> TTL 62 moins 1 = 61 Runtime d’intégration auto-hébergé*

- Dans le cas idéal, la durée de vie (TTL) sera de 128, ce qui signifie que le système Windows exécute notre Data Factory. Comme indiqué dans l’exemple ci-dessous, *128 – 107 = 21 tronçons*, ce qui signifie que 21 tronçons pour le package ont été envoyés depuis Data Factory vers le runtime d’intégration auto-hébergé au cours de la négociation TCP 3.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Par conséquent, vous devez faire appel à l’équipe réseau pour vérifier à quoi correspond le quatrième tronçon provenant du runtime d'intégration auto-hébergé. S’il s’agit du pare-feu en tant que système Linux, consultez tous les journaux pour déterminer la raison pour laquelle ce périphérique réinitialise le package après la négociation TCP 3. Toutefois, si vous n’êtes pas sûr de l’emplacement où effectuer vos investigations, essayez d’obtenir la trace netmon à partir du runtime d'intégration auto-hébergé et du pare-feu pendant la période problématique afin de déterminer quel périphérique peut réinitialiser ce package et provoquer la déconnexion. Dans ce cas, vous devez également encourager votre équipe réseau à avancer.

### <a name="how-to-collect-netmon-trace"></a>Procédure de collecte de la trace netmon

1.  Téléchargez les outils Netmon à partir de [ce site web](https://www.microsoft.com/en-sg/download/details.aspx?id=4865) et installez-les sur votre ordinateur serveur (quel que soit le serveur qui présente le problème) et le client (par exemple, le runtime d'intégration auto-hébergé).

2.  Créez un dossier, par exemple, avec le chemin suivant : *D:\netmon*. Assurez-vous qu’il dispose de suffisamment d’espace pour enregistrer le journal.

3.  Capturez les informations d’adresse IP et de port. 
    1. Démarrez une invite de commandes.
    2. Sélectionnez Exécuter en tant qu'administrateur et exécutez la commande suivante :
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Capturez la trace netmon (package réseau).
    1. Démarrez une invite de commandes.
    2. Sélectionnez Exécuter en tant qu'administrateur et exécutez la commande suivante :
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. Vous pouvez utiliser trois commandes différentes pour capturer la page réseau :
        - Option A : Commande de fichier RoundRobin (capture un seul fichier et remplace les anciens journaux).

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - Option B : Commande de fichier chaînée (crée un fichier si 200 Mo sont atteints).
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - Option C : Commande de fichier planifiée.

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Appuyez sur **Ctrl+C** pour arrêter la capture de la trace netmon.
 
> [!NOTE]
> Si vous pouvez uniquement collecter la trace netmon sur l’ordinateur client, obtenez l’adresse IP du serveur pour mieux analyser la trace.

### <a name="how-to-analyze-netmon-trace"></a>Procédure d’analyse de la trace netmon

Lorsque vous essayez d’établir une connexion Telnet à **8.8.8.8 888** avec la trace netmon ci-dessus collectée, vous êtes censé voir la trace ci-dessous :

![trace netmon 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![trace netmon 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Cela signifie que vous n’avez pas pu établir la connexion TCP côté serveur **8.8.8.8** sur la base du port **888**, de sorte que vous y voyez deux packages supplémentaires **SynReTransmit**. Étant donné que la source **SELF-HOST2** n’a pas pu établir de connexion à **8.8.8.8** au niveau du premier package, elle continue à établir la connexion.

> [!TIP]
> - Vous pouvez cliquer sur **Charger le filtre** -> **Filtre standard** -> **Adresses** -> **Adresses IPv4**.
> - Entrez **IPv4.Address == 8.8.8.8** comme filtre, puis cliquez sur **Appliquer**. Après cela, vous verrez uniquement la communication de l’ordinateur local à la destination **8.8.8.8**.

![filtre d’adresses 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![filtre d’adresses 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

L’exemple ci-dessous montre à quoi ressemblerait un scénario approprié. 

- Si Telnet **8.8.8.8 53** fonctionne correctement sans aucun problème, vous pouvez voir la négociation TCP 3, puis la session se termine avec la négociation TCP 4.

    ![exemple de scénario correct 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![exemple de scénario correct 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Sur la base de la négociation TCP 3 ci-dessus, vous pouvez voir le flux de travail ci-dessous :

    ![Flux de travail de négociation TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- La négociation TCP 4 destinée à terminer la session et son flux de travail s’affichent comme suit :

    ![Négociation TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Flux de travail de négociation TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Page de questions Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guide de performances des flux de données de mappage](concepts-data-flow-performance.md)
