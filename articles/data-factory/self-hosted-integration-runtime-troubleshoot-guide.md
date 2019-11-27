---
title: Résoudre les problèmes du runtime d’intégration auto-hébergé dans Azure Data Factory
description: Découvrez comment résoudre les problèmes liés au runtime d’intégration auto-hébergé dans Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907221"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Résoudre les problèmes liés au runtime d’intégration auto-hébergé

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés au runtime d'intégration auto-hébergé dans Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Problèmes courants et leur résolution

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>Message d’erreur : Le runtime d’intégration auto-hébergé n’est pas en mesure de se connecter au service cloud.

- **Symptôme** : 

    ![Problème de connexion du runtime d’intégration auto-hébergé](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **Cause** : Le runtime d’intégration auto-hébergé n’est pas en mesure de se connecter au service de fabrique de données (back-end). Le plus souvent, cela est dû à des paramètres réseau dans le pare-feu.

- **Résolution** : 

    1. Vérifiez si le service Windows « Service runtime d’intégration » est en cours d’exécution.
    
        ![État d’exécution du service runtime d’intégration auto-hébergé](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. Si le service Windows comme indiqué dans [1] est en cours d’exécution, suivez les instructions ci-dessous, selon le cas :

        1. Si « proxy » n’est pas configuré sur le runtime d’intégration auto-hébergé (les paramètres par défaut indiquent aucune configuration de proxy), exécutez la commande PowerShell ci-dessous sur l’ordinateur sur lequel le runtime d’intégration auto-hébergé est installé : 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > L’URL du service peut varier en fonction de l’emplacement de votre fabrique de données. L’URL du service se trouve sous IU ADF -> Connexions -> Runtimes d’intégration -> Modifier le runtime d’intégration auto-hébergé -> Noeuds -> Afficher les URL du service.
            
            Voici la réponse attendue :
            
            ![Réponse de la commande PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            Si la réponse est différente, suivez les instructions ci-dessous en fonction de vos besoins :
            
            * Si vous recevez l’erreur « Le nom distant n’a pas pu être résolu », il y a un problème avec le DNS. Veuillez contacter l’équipe réseau pour résoudre le problème de résolution DNS. 
            * Si vous recevez l’erreur « Le certificat SSL/TLS n’est pas approuvé », vérifiez si le certificat pour « https://wu2.frontend.clouddatahub.net/  » est approuvé sur l’ordinateur, installez le certificat public à l’aide de Cert Manager, ce qui devrait atténuer ce problème.
            * Vérifiez dans Windows -> Observateur d’événements (journaux)-> Journaux des applications et des services -> Integration Runtime la présence d’échecs, principalement causé par DNS, la règle de pare-feu et les paramètres réseau de l’entreprise (forcez la fermeture de la connexion). Pour ce problème, contactez votre équipe réseau pour un dépannage supplémentaire, car chaque entreprise présente des paramètres réseau personnalisés.

        2. Si « proxy » a été configuré sur le runtime d’intégration auto-hébergé, vérifiez que votre serveur proxy est en mesure d’accéder à notre point de terminaison de service. Pour obtenir un exemple de commande, consultez [ceci](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
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
            > * Vérifiez si le serveur proxy requiert une liste verte. Le cas échéant, placez [ces domaines](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) en liste verte.
            > * Vérifiez que le certificat TLS/SSL pour « wu2.frontend.clouddatahub.net/ » est approuvé sur le serveur proxy.
            > * Si vous utilisez l’authentification Active Directory dans le proxy, remplacez le compte de service par le compte d’utilisateur qui peut accéder au proxy en tant que « Service Integration Runtime ».

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Message d’erreur : Le nœud du runtime d’intégration auto-hébergé /le runtime d’intégration auto-hébergé logique présente l’état Inactif/ « En cours d’exécution (limité) »

- **Cause** : Vous pouvez voir le nœud du runtime d’intégration auto-hébergé à l’état Inactif, comme indiqué dans la capture d’écran ci-dessous :

    ![Nœud du runtime d’intégration auto-hébergé inactif](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    Cela arrive lorsque les nœuds ne sont pas en mesure de communiquer entre eux. 

- **Résolution** : 

    Connectez-vous à la machine virtuelle hébergée dans le nœud, puis ouvrez l’Observateur d’événements dans les journaux des applications et des services-> Integration Runtime, filtrez tous les journaux d’erreurs. 

     1. Si le journal des erreurs contient : 
    
        **Journal des erreurs** : System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060.  ---> System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it 10.2.4.10:8060
    
           at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
           
           at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
           
           at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    
        **Solution :** lancez la ligne de commande : telnet 10.2.4.10 8060
        
        Si vous le message d’erreur ci-dessous s’affiche, contactez vos informaticiens pour obtenir de l’aide pour résoudre ce problème. Une fois que vous avez réussi à utiliser Telnet, contactez le support technique de Microsoft si vous rencontrez toujours des problèmes pour l’état du nœud IR.
        
        ![Erreur de ligne de commande](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. Si le journal des erreurs contient :
     
        **Journal des erreurs :** Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) 
    
        **Solution :** L’une des deux actions suivantes peut aider à résoudre le problème :
         1. Placez tous les nœuds dans le même domaine.
         2. Ajoutez l’adresse IP au mappage de l’hôte dans tous les fichiers hôtes de la machine virtuelle hébergée.


## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guide de performances du mappage de flux de données ADF](concepts-data-flow-performance.md)
