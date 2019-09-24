---
title: Résoudre les problèmes d’intégrité des back-ends dans Azure Application Gateway
description: Cet article vous montre comment résoudre certains problèmes d’intégrité des back-ends dans Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: 1fd4e9156e29133b1db4fe9ab9a0825eb1aa3b55
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097579"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Résoudre les problèmes d’intégrité des back-ends dans Application Gateway
==================================================

<a name="overview"></a>Vue d'ensemble
--------

Par défaut, Application Gateway sonde les serveurs back-end afin de vérifier leur état d’intégrité et leur disponibilité pour traiter les requêtes. De leur côté, les utilisateurs peuvent également créer des sondes personnalisées pour indiquer le nom d’hôte, le chemin à sonder et les codes d’état à considérer comme sains. Dans les deux cas, si le serveur back-end ne répond pas, Application Gateway marque le serveur comme Non sain et arrête le transfert de la requête au serveur. Dès que le serveur répond à nouveau, il reprend le traitement des requêtes.

### <a name="how-to-check-backend-health"></a>Comment vérifier l’intégrité des back-ends

Vous pouvez vérifier l’intégrité de votre pool de back-ends par le biais de la page « Intégrité principale » dans le portail Azure. Vous pouvez aussi connaître son état d’intégrité à l’aide d’[Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), de l’[interface CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health) ou de l’[API REST](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth). Pour plus d’informations, consultez l’article en lien avec chacune de ces méthodes.

L’état récupéré par les méthodes mentionnées ci-dessus peut être de trois types, à savoir :

1.  Healthy

2.  Unhealthy

3.  Unknown

Si l’état d’intégrité d’un serveur back-end est sain, Application Gateway transmet les requêtes à ce serveur. Toutefois, si tous les serveurs back-end d’un pool de back-ends sont dans un état non sain ou inconnu, vous risquez de rencontrer des problèmes lors de l’accès aux applications. Ce document décrit le symptôme, la cause et la solution pour chacune des erreurs qui sont retournées quand les serveurs back-end présentent un état non sain ou inconnu.

<a name="backend-health-status-unhealthy"></a>État d’intégrité des back-ends Non sain
-------------------------------

Quand l’état d’intégrité d’un back-end est signalé comme Non sain, il s’affiche dans le portail comme dans la capture d’écran ci-dessous :

![Intégrité des back-ends dans Application Gateway - Non sain](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Si vous effectuez une requête à l’aide d’Azure PowerShell, de l’interface CLI ou de l’API REST Azure, vous obtenez une réponse semblable à celle-ci :
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Si vous voyez un état d’intégrité non sain pour tous les serveurs back-end dans un pool de back-ends, les requêtes ne sont pas transmises à ces serveurs, et Application Gateway retourne l’erreur 502 de passerelle incorrecte au client à l’origine des requêtes. Pour résoudre le problème, examinez la colonne Détails de l’onglet d’intégrité des back-ends.

Le message affiché dans la colonne Détails de l’onglet d’intégrité des back-ends fournit des insights plus détaillés sur le problème, qui vous aident à commencer à résoudre le problème.

> [!NOTE]
> La requête de la sonde par défaut est envoyée au format \<protocol\>://127.0.0.1:\<port\>/ (par exemple, <http://127.0.0.1/> pour une sonde http sur le port 80), et considère comme saines uniquement les réponses avec les codes d’état HTTP entre 200 et 399. Le protocole et le port de destination sont hérités des paramètres HTTP. Si vous souhaitez qu’Application Gateway sonde sur un autre protocole, nom d’hôte ou chemin et accepte un autre code d’état comme sain, configurez une sonde personnalisée et associez-la aux paramètres HTTP.

<a name="error-messages"></a>Messages d'erreur
------------------------
#### <a name="backend-server-timeout"></a>Délai d’attente dépassé pour le serveur back-end

**Message :** Le temps pris par le serveur back-end pour répondre à la sonde d’intégrité d’Application Gateway est supérieur au seuil de délai d’attente défini dans les paramètres de la sonde.

**Cause :** Après avoir envoyé une requête de sonde HTTP(S) au serveur back-end, Application Gateway attend la réponse de ce serveur pendant une durée définie. Si le serveur back-end ne répond pas dans ce délai, il est marqué comme non sain jusqu’à ce qu’il puisse répondre dans le délai imparti.

**Résolution :** Examinez le serveur back-end ou l’application pour comprendre pourquoi il ou elle ne répond pas dans le délai défini. Vérifiez également les dépendances de l’application, notamment pour détecter si la base de données rencontre des problèmes susceptibles d’entraîner un retard dans la réponse. Si l’application a le comportement attendu et qu’elle doit répondre seulement après le délai d’attente défini, augmentez la valeur du délai d’attente dans les paramètres de la sonde personnalisée.
Vous devez utiliser une sonde personnalisée pour modifier la valeur du délai d’attente. Pour savoir comment configurer une sonde personnalisée, [consultez cette page de documentation](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Pour augmenter le délai d’attente, effectuez les étapes suivantes :

1.  Accédez directement au serveur back-end et regardez le temps que le serveur a pris pour répondre sur cette page. Pour cela, utilisez n’importe quel outil de votre choix, y compris un navigateur avec les outils de développement.

2.  Une fois que vous avez déterminé le temps de réponse de l’application, cliquez sur l’onglet Sondes d’intégrité et sélectionnez la sonde qui est associée à vos paramètres HTTP.

3.  Entrez une valeur de délai d’attente supérieure au délai de réponse de l’application, en secondes.

4.  Enregistrez les paramètres de la sonde personnalisée et vérifiez si l’intégrité du back-end est saine maintenant.

#### <a name="dns-resolution-error"></a>Erreur de résolution du DNS

**Message :** Application Gateway n’a pas pu créer de sonde pour ce back-end. Cela se produit généralement quand le nom de domaine complet du back-end n’a pas été entré correctement. 

**Cause :** Si le pool de back-ends est de type Adresse IP/Nom de domaine complet ou App Service, Application Gateway utilise l’adresse IP du nom de domaine complet entré à l’aide du serveur DNS (personnalisé ou Azure par défaut) et tente de se connecter au serveur sur le port TCP indiqué dans les paramètres HTTP. Toutefois, si ce message s’affiche, cela peut indiquer qu’Application Gateway n’a pas pu résoudre correctement l’adresse IP du nom de domaine complet entré.

**Résolution :**

1.  Vérifiez que le nom de domaine complet entré dans le pool de back-ends est correct et, s’il s’agit d’un domaine public, essayez de le résoudre à partir de votre machine locale.

2.  Si vous réussissez à résoudre l’adresse IP, il y a peut-être un problème avec la configuration DNS dans le réseau virtuel.

3.  Vérifiez si le réseau virtuel est configuré avec un serveur DNS personnalisé. Si c’est le cas, examinez le serveur DNS pour comprendre pourquoi il ne réussit pas à résoudre l’adresse IP du nom de domaine complet entré.

4.  S’il s’agit du DNS par défaut d’Azure, vérifiez dans votre registre d’inscription des noms de domaine que le mappage approprié avec un enregistrement A ou un enregistrement CNAM a été effectué.

5.  Si le domaine est privé/interne, essayez de le résoudre à partir d’une machine virtuelle située dans le même réseau virtuel. Si cette opération échoue, redémarrez Application Gateway et vérifiez à nouveau. Pour redémarrer Application Gateway, vous devez utiliser les commandes PowerShell [Stop](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) et [Start](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) décrites dans les liens de documentation respectifs.

#### <a name="tcp-connect-error"></a>Erreur de connexion TCP

**Message :** Application Gateway n’a pas pu se connecter au serveur back-end.
Vérifiez que le serveur back-end répond sur le port utilisé pour la sonde.
Vérifiez également qu’aucun NSG, UDR ou pare-feu ne bloque l’accès à l’adresse IP et au port de ce serveur back-end

**Cause :** Après la phase de résolution du DNS, Application Gateway tente de se connecter au serveur back-end sur le port TCP qui est configuré dans les paramètres HTTP. Si Application Gateway ne parvient pas à établir une session TCP sur le port spécifié, la sonde est marquée comme non saine, avec ce message.

**Solution :** Si vous voyez cette erreur, vérifiez les points suivants :

1.  Essayez de vous connecter au serveur back-end sur le port indiqué dans les paramètres HTTP à l’aide d’un navigateur ou de PowerShell. Par exemple, utilisez cette commande : Test-NetConnection -NomOrdinateur www.bing.com -Port 443

2.  Si le port indiqué n’est pas le port souhaité, entrez le numéro de port approprié à utiliser par Application Gateway pour se connecter au serveur back-end.

3.  Si vous ne réussissez pas non plus à vous connecter sur le port à partir de votre machine locale :

    a.  Vérifiez les paramètres NSG de la carte réseau et du sous-réseau du serveur back-end pour savoir si les connexions entrantes sur le port configuré sont autorisées. Si ces connexions ne sont pas autorisées, créez une règle pour les autoriser. Pour savoir comment créer des règles NSG, [consultez cette page de documentation](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Vérifiez les paramètres NSG du sous-réseau d’Application Gateway pour savoir si le trafic public et privé sortant est autorisé, afin que la connexion puisse être établie. Pour en savoir plus sur la création de règles NSG, consultez le document lié dans la section (a).
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Vérifiez qu’il n’y a pas d’erreurs de routage dans les paramètres UDR du sous-réseau du serveur back-end et d’Application Gateway. Assurez-vous que l’UDR ne détourne pas le trafic du sous-réseau du back-end. Vérifiez, par exemple, les routes vers les appliances virtuelles du réseau ou les routes par défaut annoncées sur le sous-réseau d’Application Gateway via ExpressRoute/VPN.

    d.  Vous pouvez vérifier les règles et routes effectives d’une carte réseau à l’aide des commandes PowerShell suivantes.
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  Si vous ne constatez pas d’erreur dans les paramètres NSG ou UDR, examinez votre serveur back-end pour détecter d’éventuels problèmes avec des applications qui empêchent les clients d’établir une session TCP sur le ou les ports configurés. Voici quelques points à vérifier :

    e.  Ouvrez une invite de commandes (Win+R -\> cmd) et entrez netstat, puis appuyez sur Entrée.

    f.  Vérifiez que le serveur écoute sur le port configuré.
        Par exemple :
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  Si ce n’est pas le cas, vérifiez les paramètres de votre serveur web, notamment les liaisons de site dans IIS, le bloc serveur dans NGINX et l’hôte virtuel dans Apache.

    h.  Vérifiez les paramètres de votre pare-feu du système d’exploitation pour vous assurer que le trafic entrant vers le port est autorisé.

#### <a name="http-status-code-mismatch"></a>Non-correspondance du code d’état HTTP

**Message :** Le code d’état de la réponse HTTP du back-end ne correspond pas au paramètre de la sonde. Attendu :{HTTPStatusCode0} Reçu :{HTTPStatusCode1}.

**Cause :** Une fois que la connexion TCP a été établie et que la négociation SSL est terminée (si SSL est activé), Application Gateway envoie la sonde sous forme de requête HTTP GET au serveur back-end. Comme indiqué plus haut, la sonde par défaut est envoyée sur \<protocole\>://127.0.0.1:\<port\>/, et considère comme sains les codes d’état de réponse compris entre 200 et 399. Si le serveur retourne un autre code d’état, il est marqué comme non sain, avec ce message.

**Solution :** Selon le code de réponse du serveur back-end, effectuez les étapes appropriées parmi les suivantes. Quelques codes d’état courants sont décrits ici :

| **Error** | **Actions** |
| --- | --- |
| Non-correspondance du code d’état de la sonde : Réponse 401 reçue | Vérifiez si le serveur back-end requiert une authentification. Les sondes d’Application Gateway ne peuvent pas passer ici les informations d’identification pour l’authentification. Autorisez la réponse \"HTTP 401\" dans une correspondance de code d’état de la sonde ou configurez la sonde sur un chemin où le serveur ne requiert pas d’authentification. | |
| Non-correspondance du code d’état de la sonde : Réponse 403 reçue | Accès interdit. Vérifiez que l’accès au chemin est autorisé sur le serveur back-end. | |
| Non-correspondance du code d’état de la sonde : Réponse 404 reçue | Page introuvable. Vérifiez que le chemin du nom d’hôte est accessible sur le serveur back-end. Changez le paramètre du chemin ou du nom d’hôte en une valeur accessible. | |
| Non-correspondance du code d’état de la sonde : Réponse 405 reçue | Les requêtes de sonde d’Application Gateway utilisent la méthode HTTP GET. Vérifiez que votre serveur accepte cette méthode. | |
| Non-correspondance du code d’état de la sonde : Réponse 500 reçue | Erreur interne du serveur. Vérifiez l’intégrité du serveur back-end et que les services sont en cours d’exécution. | |
| Non-correspondance du code d’état de la sonde : Réponse 503 reçue | Service indisponible. Vérifiez l’intégrité du serveur back-end et que les services sont en cours d’exécution. | |

Ou bien, si vous pensez que la réponse est légitime et que vous souhaitez qu’Application Gateway considère d’autres codes d’état comme sains, vous pouvez créer une sonde personnalisée. Cette solution est utile dans les situations où le site web back-end nécessite une authentification, car du fait que les requêtes de sonde ne renferment pas d’informations d’identification de l’utilisateur, elles échouent, et un code d’état HTTP 401 est retourné par le serveur back-end.

Pour créer une sonde personnalisée, effectuez les étapes décrites [ici](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

#### <a name="http-response-body-mismatch"></a>Non-correspondance du corps de la réponse HTTP

**Message :** Le corps de la réponse HTTP du back-end ne correspond pas au paramètre de la sonde. Le corps de la réponse reçue ne contient pas l’élément {string}.

**Cause :** Quand vous créez une sonde personnalisée, vous avez l’option de marquer un serveur back-end comme sain en mettant en correspondance une chaîne du corps de la réponse. Par exemple, configurez Application Gateway pour accepter la mise en correspondance de la chaîne « non autorisé ». Si la réponse du serveur back-end à la requête de la sonde contient la chaîne « non autorisé », elle est marquée comme étant saine.
Sinon, elle est marquée comme étant non saine, avec ce message.

**Solution :** Vous pouvez résoudre ce problème en effectuant les étapes ci-dessous :

1.  Accédez au serveur back-end localement ou à partir d’une machine cliente sur le chemin de la sonde et examinez le corps de la réponse.

2.  Examinez la configuration de la sonde personnalisée d’Application Gateway pour vérifier si le corps de la réponse correspond à la chaîne configurée.

3.  Si les deux ne correspondent pas, modifiez la configuration de la sonde en entrant la valeur de chaîne correcte à accepter.

Pour en savoir plus sur la correspondance des sondes d’Application Gateway, [consultez cet article](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching).

#### <a name="backend-server-certificate-invalid-ca"></a>Autorité de certification non valide pour le certificat du serveur back-end

**Message :** Le certificat de serveur utilisé par le serveur back-end n’est pas signé par une autorité de certification reconnue. Ajoutez le serveur back-end à la liste approuvée d’Application Gateway en chargeant le certificat racine du certificat de serveur qui est utilisé par le serveur back-end.

**Cause :** Le chiffrement SSL de bout en bout avec Application Gateway v2 nécessite la vérification du certificat du serveur back-end afin de garantir que le serveur est sain.
Pour qu’un certificat SSL soit approuvé, ce certificat utilisé par le serveur back-end doit avoir été émis par l’une des autorités de certification figurant dans le magasin de certificats approuvés d’Application Gateway. Si le certificat n’a pas été émis par une autorité de certification approuvée, ce qui est le cas des certificats auto-signés, par exemple, les utilisateurs doivent charger le certificat de l’émetteur dans Application Gateway.

**Solution :** Effectuez les étapes ci-dessous pour exporter et charger le certificat racine approuvé dans Application Gateway (la procédure décrite s’applique aux clients Windows)

1.  Connectez-vous à la machine qui héberge votre application

2.  Ouvrez une fenêtre d’exécution en appuyant sur Win+R ou en cliquant avec le bouton droit sur le bouton Démarrer et en sélectionnant Exécuter

3.  Entrez certmgr.msc et appuyez sur Entrée. Vous pouvez également ouvrir le gestionnaire de certificats à partir du menu Démarrer.

4.  Recherchez le certificat, qui se trouve généralement dans \'Certificates - Current User\\Personal\\Certificates\', puis ouvrez-le

5.  Dans les propriétés du certificat, sélectionnez l’onglet Chemin d’accès de certification

6.  Sélectionnez le certificat racine, puis sélectionnez l’option « Afficher le certificat »

7.  Dans les propriétés du certificat, accédez à l’onglet Détails

8.  Dans l’onglet Détails, sélectionnez l’option « Copier dans un fichier », puis enregistrez le fichier au format X.509 encodé en base 64 (.CER)

9.  Après avoir enregistré le fichier, ouvrez la page des paramètres HTTP d’Application Gateway dans le portail Azure

10. Ouvrez Paramètres HTTP, cliquez sur « Ajouter un certificat » et recherchez le fichier de certificat que vous venez d’enregistrer

11. Cliquez sur Enregistrer pour enregistrer les paramètres HTTP

Vous pouvez aussi exporter le certificat racine à partir d’une machine cliente en accédant directement au serveur (sans passer par Application Gateway) à l’aide d’un navigateur et en exportant le certificat racine à partir du navigateur.

Pour connaître les étapes détaillées d’extraction et de chargement des certificats racines approuvés dans Application Gateway, [consultez cet article](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Non-correspondance du certificat racine approuvé

**Message :** Le certificat racine du certificat de serveur utilisé par le serveur back-end ne correspond pas au certificat racine approuvé qui a été ajouté dans Application Gateway. Assurez-vous d’ajouter le certificat racine approprié pour faire figurer le serveur back-end dans la liste approuvée

**Cause :** Le chiffrement SSL de bout en bout avec Application Gateway v2 nécessite la vérification du certificat du serveur back-end afin de garantir que le serveur est sain.
Pour qu’un certificat SSL soit approuvé, ce certificat utilisé par le serveur back-end doit avoir été émis par l’une des autorités de certification figurant dans le magasin de certificats approuvés d’Application Gateway. Si le certificat n’a pas été émis par une autorité de certification approuvée, ce qui est le cas des certificats auto-signés, par exemple, les utilisateurs doivent charger le certificat de l’émetteur dans Application Gateway.

Le certificat qui a été chargé dans les paramètres HTTP d’Application Gateway doit correspondre au certificat racine du certificat utilisé sur le serveur back-end.

**Solution :** Si vous voyez le message d’erreur mentionné ci-dessus, cela signifie que le certificat ayant été chargé dans Application Gateway ne correspond pas à celui qui a été chargé sur le serveur back-end.

Effectuez les étapes 1 à 11 décrites plus haut pour charger le certificat racine approuvé approprié dans Application Gateway.

Pour connaître les étapes détaillées d’extraction et de chargement des certificats racines approuvés dans Application Gateway, [consultez cet article](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> L’erreur mentionnée peut également se produire si le serveur back-end ne passe pas la chaîne complète du certificat, y compris les éléments Root -> Intermediate (le cas échéant) -> Leaf pendant la négociation TLS. Pour vérifier ce point, utilisez les commandes OpenSSL à partir de n’importe quel client pour vous connecter au serveur back-end avec les paramètres configurés dans la sonde d’Application Gateway.

Par exemple,
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Si la sortie n’affiche pas la chaîne complète du certificat retourné, réexportez le certificat avec la chaîne complète incluant le certificat racine, puis configurez-le sur votre serveur back-end. 

CONNECTED(00000188)\
depth=0 OU = Domain Control Validated, CN = \*.example.com\
verify error:num=20:unable to get local issuer certificate\
verify return:1\
depth=0 OU = Domain Control Validated, CN = \*.example.com\
verify error:num=21:unable to verify the first certificate\
verify return:1\
\-\-\-\
Certificate chain\
 0 s:/OU=Domain Control Validated/CN=*.example.com\
   i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
\-----BEGIN CERTIFICATE-----\
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
\-----END CERTIFICATE-----

#### <a name="backend-certificate-invalid-common-name-cn"></a>Nom commun (CN) non valide pour le certificat du back-end

**Message :** Le nom commun (CN) du certificat du back-end ne correspond pas à l’en-tête d’hôte de la sonde.

**Cause :** Application Gateway vérifie si le nom d’hôte spécifié dans le paramètre HTTP du back-end correspond au nom commun (CN) présenté par le certificat SSL du serveur back-end. Ce comportement s’applique uniquement aux références SKU Standard_v2 et WAF_v2. Le SNI des références SKU Standard et WAF est défini comme nom de domaine complet dans l’adresse du pool de back-ends.

Dans la référence SKU v2, si une sonde par défaut est utilisée (pas de sonde personnalisée configurée/associée), le SNI est défini d’après le nom d’hôte indiqué dans les paramètres HTTP ou, si l’option « Choisir le nom d’hôte à partir de l’adresse de back-end » est cochée dans les paramètres HTTP, là où le pool d’adresses de back-end contient un nom de domaine complet valide.

Si une sonde personnalisée est associée aux paramètres HTTP, le SNI est défini d’après le nom d’hôte qui est indiqué dans la configuration de la sonde personnalisée ou, si l’option « Choisir le nom d’hôte dans les paramètres HTTP de back-end » est cochée dans la sonde personnalisée, le SNI est défini sur la base du nom d’hôte indiqué dans les paramètres HTTP.

Dans le cas où l’option « Choisir le nom d’hôte à partir de l’adresse de back-end » est cochée dans les paramètres HTTP, le pool d’adresses de back-end doit contenir un nom de domaine complet valide.

Si vous voyez le message d’erreur mentionné ci-dessus, cela signifie que le nom commun (CN) du certificat du back-end ne correspond pas au nom d’hôte qui est configuré dans la sonde personnalisée ou dans les paramètres HTTP (quand l’option « Choisir le nom d’hôte dans les paramètres HTTP de back-end » est cochée). Si vous utilisez une sonde par défaut, le nom d’hôte est « 127.0.0.1 ». Si vous souhaitez changer cette valeur, vous devez créer une sonde personnalisée et l’associer aux paramètres HTTP.

**Solution :**

Pour résoudre ce problème, effectuez les étapes suivantes :

Pour Windows :

1.  Connectez-vous à la machine qui héberge votre application

2.  Ouvrez une fenêtre d’exécution en appuyant sur Win+R ou en cliquant avec le bouton droit sur le bouton Démarrer et en sélectionnant Exécuter

3.  Entrez certmgr.msc et appuyez sur Entrée. Vous pouvez également ouvrir le gestionnaire de certificats à partir du menu Démarrer.

4.  Recherchez le certificat, qui se trouve généralement dans \'Certificates - Current User\\Personal\\Certificates\', puis ouvrez-le

5.  Dans l’onglet Détails, vérifiez l’objet du certificat

6.  Vérifiez le nom commun du certificat indiqué dans les détails et entrez le même nom dans le champ du nom d’hôte de la sonde personnalisée ou des paramètres HTTP (quand l’option « Choisir le nom d’hôte dans les paramètres HTTP de back-end » est cochée). Si ce n’est pas le nom d’hôte souhaité pour votre site web, vous devez obtenir un certificat pour ce domaine, ou entrer le nom d’hôte approprié dans la configuration de la sonde personnalisée ou des paramètres HTTP.

Pour Linux avec OpenSSL

1.  Exécutez cette commande dans OpenSSL 
```
openssl x509 -in certificate.crt -text -noout
```

2.  Dans les propriétés affichées, recherchez le nom commun du certificat et entrez le même nom dans le champ du nom d’hôte des paramètres HTTP. Si ce n’est pas le nom d’hôte souhaité pour votre site web, vous devez obtenir un certificat pour ce domaine, ou entrer le nom d’hôte approprié dans la configuration de la sonde personnalisée ou des paramètres HTTP.

#### <a name="backend-certificate-is-invalid"></a>Le certificat du back-end n’est pas valide

**Message :** Le certificat du back-end n’est pas valide. La date actuelle ne s’inscrit pas dans la plage de dates définie par les options \"Valide à partir du\" et \"Valide jusqu’au\" pour le certificat.

**Cause :** Chaque certificat a une durée de validité, et la connexion HTTPS est sécurisée uniquement si le certificat SSL du serveur est valide.
La date actuelle doit être comprise dans la plage de dates délimitée par « Valide à partir du » et « Valide jusqu’au ». Si ce n’est pas le cas, le certificat est considéré comme non valide et constitue un problème de sécurité. Application Gateway marque alors le serveur back-end comme étant non sain.

**Solution :** Si votre certificat SSL est arrivé à expiration, renouvelez le certificat auprès de votre fournisseur et mettez à jour les paramètres du serveur avec le nouveau certificat. S’il s’agit d’un certificat auto-signé, générez un certificat valide et chargez le certificat racine dans les paramètres HTTP d’Application Gateway. Pour ce faire, effectuez les étapes ci-dessous :

1.  Ouvrez les paramètres HTTP d’Application Gateway dans le portail

2.  Sélectionnez les paramètres HTTP associés au certificat expiré, sélectionnez Ajouter un certificat et ouvrez le nouveau fichier de certificat

3.  Supprimez l’ancien certificat en cliquant sur l’icône Supprimer à côté du certificat, puis cliquez sur Enregistrer

#### <a name="certificate-verification-failed"></a>Échec de la vérification du certificat

**Message :** La validité du certificat du back-end n’a pas pu être vérifiée. Pour en déterminer la raison, examinez le message associé au code d’erreur {errorCode} dans les diagnostics Open SSL

**Cause :** Cette erreur se produit quand Application Gateway n’est pas en mesure de vérifier la validité du certificat.

**Solution :** Pour résoudre ce problème, vérifiez que le certificat sur votre serveur a été créé correctement. Par exemple, utilisez [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) pour vérifier le certificat et ses propriétés, puis réessayez de charger le certificat dans les paramètres HTTP d’Application Gateway.

<a name="backend-health-status-unknown"></a>État d’intégrité des back-ends Inconnu
-------------------------------
Quand l’état d’intégrité d’un back-end est signalé comme Inconnu, il s’affiche dans le portail comme dans la capture d’écran ci-dessous :

![Intégrité des back-ends dans Application Gateway - Inconnu](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Un back-end peut présenter un état d’intégrité inconnu pour une ou plusieurs des raisons suivantes :

1.  Un NSG sur le sous-réseau d’Application Gateway bloque l’accès entrant sur les ports 65503-65534 (SKU v1) ou 65200-65535 (SKU v2) à partir d’« Internet »
2.  Un UDR sur le sous-réseau d’Application Gateway a une route par défaut (0.0.0.0/0) avec le tronçon suivant non défini sur Internet
3.  La route par défaut est annoncée par une connexion ExpressRoute/VPN au réseau virtuel via le protocole BGP
4.  Le serveur DNS personnalisé est configuré dans le réseau virtuel qui n’est pas en mesure de résoudre les noms de domaines publics
5.  Application Gateway se trouve dans un état non sain

**Solution :**

1.  Vérifiez que votre NSG ne bloque pas l’accès aux ports 65503-65534 (SKU v1) ou 65200-65535 (SKU v2) à partir d’« Internet »

    a.  Dans Application Gateway, sous l’onglet « Vue d’ensemble », sélectionnez le lien Réseau/sous-réseau virtuel

    b.  Dans l’onglet Sous-réseaux de votre réseau virtuel, sélectionnez le sous-réseau où Application Gateway a été déployé

    c.  Vérifiez si un NSG est configuré

    d.  S’il y en a un, recherchez cette ressource NSG dans l’onglet de recherche ou sous Toutes les ressources

    e.  Dans la section Règles de trafic entrant, ajoutez une règle de trafic entrant pour autoriser la plage de ports de destination 65503-65534 pour la référence SKU v1 ou 65200-65535 pour la référence SKU v2, avec la source définie sur N’importe laquelle ou Internet

    f.  Cliquez sur Enregistrer et vérifiez si le serveur back-end est affiché comme sain maintenant

    g.  Vous pouvez également faire cette vérification à l’aide de [PowerShell/CLI](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

2.  Vérifiez si votre UDR a une route par défaut (0.0.0.0/0) avec le tronçon suivant non défini sur Internet
    
    a.  Effectuez les étapes 1.a et 1.b pour identifier votre sous-réseau

    b.  Vérifiez s’il y a un UDR configuré. S’il y en a un, recherchez cette ressource dans la barre de recherche ou sous Toutes les ressources

    c.  Vérifiez s’il y a une route par défaut (0.0.0.0/0) avec le tronçon suivant non défini sur Internet. S’il s’agit d’une appliance virtuelle ou d’une passerelle de réseau virtuel, assurez-vous que l’appliance virtuelle ou l’appareil local est en mesure de rediriger correctement le paquet vers la destination Internet sans modifier le paquet

    d.  Dans le cas contraire, définissez le tronçon suivant sur Internet, cliquez sur Enregistrer, puis vérifiez l’intégrité du serveur back-end

3.  La route par défaut est annoncée par une connexion ExpressRoute/VPN au réseau virtuel via le protocole BGP

    a.  Si vous avez une connexion ExpressRoute/VPN au réseau virtuel via le protocole BGP et que vous annoncez la route par défaut, assurez-vous que le paquet est redirigé correctement vers la destination Internet sans être modifié

    b.  Vous pouvez vérifier ce point à l’aide de l’option « Résoudre les problèmes de connexion » disponible dans le portail Application Gateway

    c.  Choisissez la destination manuellement au format de n’importe quelle adresse IP routable sur Internet (par exemple, « 1.1.1.1 ») ainsi que le port de destination de votre choix, puis vérifiez la connectivité.

    d.  Si le tronçon suivant est une passerelle de réseau virtuel, il y a peut-être une route par défaut annoncée via ExpressRoute ou VPN qui est configurée

4.  Si un serveur DNS personnalisé est configuré dans le réseau virtuel, vérifiez que le ou les serveurs peuvent résoudre les domaines publics. La résolution des noms de domaines publics peut être nécessaire dans les scénarios où Application Gateway doit accéder à des domaines externes tels que des serveurs OCSP ou vérifier l’état de révocation du certificat, par exemple.

5.  Pour vérifier si Application Gateway est sain et en cours d’exécution, accédez à l’option Intégrité des ressources dans le portail et assurez-vous que cette ressource est affichée comme « Saine ». Si vous constatez un état Non sain ou Dégradé, [contactez le support](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Étapes suivantes
----------

Pour en savoir plus sur les diagnostics et la journalisation dans Application Gateway, [consultez cet article](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
