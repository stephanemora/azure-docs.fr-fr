---
title: Résoudre les problèmes du runtime d’intégration auto-hébergé dans Azure Data Factory
description: Découvrez comment résoudre les problèmes liés au runtime d’intégration auto-hébergé dans Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f27132eb21d245d0d26de910abba088ba3b8efde
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170970"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Résoudre les problèmes liés au runtime d’intégration auto-hébergé

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés au runtime d'intégration auto-hébergé dans Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Problèmes courants et leur résolution

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Message d’erreur : Le runtime d’intégration auto-hébergé ne peut pas se connecter au service Cloud

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

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Message d’erreur : Le nœud du runtime d’intégration auto-hébergé /le runtime d’intégration auto-hébergé logique présente l’état Inactif/ « En cours d’exécution (limité) »

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


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Page de questions Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guide de performances des flux de données de mappage](concepts-data-flow-performance.md)
