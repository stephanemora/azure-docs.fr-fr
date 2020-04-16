---
title: Résoudre les problèmes du runtime d’intégration auto-hébergé dans Azure Data Factory
description: Découvrez comment résoudre les problèmes liés au runtime d’intégration auto-hébergé dans Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: f298b331d53eb8bab67a6f99194065dc5f889236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414893"
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
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.    Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
