---
title: Résoudre les problèmes de connectivité Azure Private Endpoint
description: Guide pas à pas pour diagnostiquer la connectivité de point de terminaison privé
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: df4ec6ddbba029eb29d2440717697968f8c79302
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191063"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>Résoudre les problèmes de connectivité Private Endpoint

Ce guide fournit des instructions pas à pas pour valider et diagnostiquer votre configuration de connectivité de point de terminaison privé. 

Azure Private Endpoint est une interface réseau qui vous permet de vous connecter de façon privée et sécurisée à un service de liaisons privées. Cette solution vous aide à sécuriser vos charges de travail dans Azure en fournissant une connectivité privée à vos ressources de service Azure à partir de votre réseau virtuel, ce qui revient à placer ces services sur votre réseau virtuel. 

Voici les scénarios de connectivité disponibles avec les points de terminaison privés : 
- Réseau virtuel de la même région 
- Réseaux virtuels avec appairage régional
- Réseaux virtuels avec appairage mondial
- Circuits Express Route ou client local via VPN

## <a name="diagnosing-connectivity-problems"></a>Diagnostic des problèmes de connectivité 
Passez en revue les étapes listées ci-dessous pour vous assurer que toutes les configurations habituelles sont telles que prévu, afin de résoudre les problèmes de connectivité liés à votre configuration de point de terminaison privé.

1. Passez en revue la configuration du point de terminaison privé en parcourant la ressource. 

    a) Accédez au **Centre de liaisons privées**.

      ![Centre de liaisons privées](./media/private-endpoint-tsg/private-link-center.png)

    b) Sélectionnez Points de terminaison privés dans le volet de navigation gauche.
    
      ![Points de terminaison privés](./media/private-endpoint-tsg/private-endpoints.png)

    c) Filtrez et sélectionnez le point de terminaison privé que vous souhaitez diagnostiquer.

    d) Passez en revue les informations relatives au réseau virtuel et au système DNS.
    
     - Vérifiez que l’état de la connexion est **Approuvée**.
     - Vérifiez que la machine virtuelle dispose d’une connectivité au réseau virtuel hébergeant les points de terminaison privés.
     - Informations de nom de domaine complet (copie) et adresse IP privée affectée.
    
       ![Configuration de réseau virtuel et de système DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. Utilisez [**Azure Monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) pour examiner les données transmises.

    a) Sur la ressource Private Endpoint, sélectionnez **Superviser**.
     - Sélectionnez les données entrantes ou sortantes, et vérifiez si les données sont transmises lors de la tentative de connexion au point de terminaison privé. Un délai d’environ 10 minutes est attendu.
    
       ![Vérifier la télémétrie du point de terminaison privé](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. Utilisez la résolution des problèmes de connexion de machine virtuelle à partir de **Network Watcher**.

    a) Sélectionnez la machine virtuelle cliente.

    b) Sélectionnez la section **Résoudre les problèmes de connexion**, onglet **Connexion sortante**.
    
      ![Network Watcher - Tester les connexions sortantes](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c) Sélectionnez **Utiliser Network Watcher pour le suivi détaillé des connexions**.
    
      ![Network Watcher - Résolution des problèmes de connexion](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d) Sélectionnez **Tester par nom de domaine complet**.
     - Collez le nom de domaine complet à partir de la ressource Private Endpoint.
     - Spécifiez un port (*généralement 443 pour Stockage Azure ou COSMOS, 1336 pour SQL...* ).

    e) Cliquez sur **Test** et validez les résultats des tests.
    
      ![Network Watcher - Résultats des tests](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. La résolution DNS des résultats des tests doit avoir la même adresse IP privée assignée au point de terminaison privé.

    a) Si les paramètres DNS sont incorrects, effectuez les étapes suivantes :
     - Avec une zone privée : 
       - Vérifiez que le réseau virtuel de machine virtuelle client est associé à la zone privée.
       - Vérifiez que l’enregistrement de zone DNS privée existe. Sinon, créez-le.
    
     - Avec un système DNS personnalisé :
       - Vérifiez les paramètres DNS de votre client et vérifiez que la configuration DNS est correcte.
       Pour obtenir des conseils, consultez [Vue d’ensemble du point de terminaison privé - Configuration DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration).

    b) Si la connectivité échoue à cause du groupe de sécurité réseau ou des routes définies par l’utilisateur :
     - Passez en revue les règles de trafic sortant du groupe de sécurité réseau et créez des règles de trafic sortant appropriées pour autoriser le trafic.
    
       ![Règles de trafic sortant du groupe de sécurité réseau](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. Si la connexion a des résultats validés, le problème de connectivité est peut-être lié à d’autres aspects tels que les secrets, les jetons et les mots de passe au niveau de la couche application.
   - Dans ce cas, passez en revue la configuration de la ressource Private Link associée au point de terminaison privé. Consultez le [Guide de résolution des problèmes de liaison privée](troubleshoot-private-link-connectivity.md). 

6. Si votre problème n’est toujours pas résolu et que le problème de connectivité persiste, contactez l’[équipe de support Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). 

## <a name="next-steps"></a>Étapes suivantes

 * [Créer un point de terminaison privé sur le sous-réseau mis à jour (portail Azure)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [Guide de résolution des problèmes de liaison privée](troubleshoot-private-link-connectivity.md)
