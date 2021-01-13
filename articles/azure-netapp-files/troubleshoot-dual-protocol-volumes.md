---
title: Résoudre les problèmes liés aux volumes à deux protocoles pour Azure NetApp Files | Microsoft Docs
description: Décrit les messages d’erreur et les résolutions qui peuvent vous aider à résoudre les problèmes de double protocole pour Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 0ae7e8f745a91e080d12a47271057ed90f9bc835
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134328"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>Résoudre les problèmes des volumes à deux protocoles

Cet article décrit les résolutions des conditions d’erreur que vous pouvez rencontrer lors de la création ou de la gestion de volumes à deux protocoles.

## <a name="error-conditions-and-resolutions"></a>Conditions d’erreur et résolutions

|     Conditions d’erreur    |     Résolution    |
|-|-|
| La création d’un volume à deux protocoles échoue avec l’erreur `This Active Directory has no Server root CA Certificate`.    |     Si cette erreur se produit lorsque vous créez un volume à deux protocoles, assurez-vous que le certificat racine de l’autorité de certification est chargé dans votre compte NetApp.    |
| La création d’un volume à deux protocoles échoue avec l’erreur `Failed to validate LDAP configuration, try again after correcting LDAP configuration`.    |  L’enregistrement du pointeur (PTR) de l’ordinateur hôte AD est peut-être manquant sur le serveur DNS. Vous devez créer une zone de recherche inversée sur le serveur DNS, puis ajouter un enregistrement PTR de l’ordinateur hôte AD dans cette zone de recherche inversée. <br> Par exemple, supposons que l’adresse IP de l’ordinateur AD est `1.1.1.1`, le nom d’hôte de l’ordinateur AD (trouvé à l’aide de la commande `hostname`) est `AD1` et le nom de domaine est `contoso.com`.  L’enregistrement PTR ajouté à la zone de recherche inversée doit être `1.1.1.1` -> `contoso.com`.   |
| La création d’un volume à deux protocoles échoue avec l’erreur `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE`. |  Cette erreur indique que le mot de passe AD est incorrect lorsqu’Active Directory est joint au compte NetApp. Mettez à jour la connexion AD avec le mot de passe correct, puis réessayez. |
| La création d’un volume à deux protocoles échoue avec l’erreur `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available`. |   Cette erreur indique que DNS n’est pas accessible. Cela peut être dû au fait que l’adresse IP DNS est incorrecte ou qu’il y a un problème réseau. Vérifiez l’adresse IP DNS entrée dans la connexion AD et assurez-vous que l’adresse IP est correcte. <br> En outre, assurez-vous que l’instance AD et le volume se trouvent dans la même région et dans le même réseau virtuel. S’ils se trouvent dans des réseaux virtuels différents, assurez-vous que l’appairage de réseaux virtuels est établi entre les deux réseaux virtuels.|
| Erreur « Autorisation refusée » lors du montage d’un volume à deux protocoles. | Un volume à double protocoles prend en charge les protocoles NFS et SMB.  Lorsque vous tentez d’accéder au volume monté sur le système UNIX, celui-ci tente de mapper l’utilisateur UNIX que vous utilisez à un utilisateur Windows. Si aucun mappage n’est trouvé, l’erreur « Autorisation refusée » se produit. <br> Cette situation s’applique également lorsque vous utilisez l’utilisateur « racine » pour l’accès. <br> Pour éviter l’erreur « Autorisation refusée », assurez-vous que Windows Active Directory inclut `pcuser` avant d’accéder au point de montage. Si vous ajoutez `pcuser` après avoir rencontré l’erreur « Autorisation refusée », patientez 24 heures jusqu’à ce que l’entrée du cache s’efface avant de réessayer d’accéder. |

## <a name="next-steps"></a>Étapes suivantes  

* [Créer un volume à deux protocoles](create-volumes-dual-protocol.md)
* [Configurer un client NFS pour Azure NetApp Files](configure-nfs-clients.md)
