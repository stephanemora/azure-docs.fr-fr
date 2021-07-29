---
title: Résoudre les problèmes de connectivité réseau | Microsoft Docs
description: Fournit des conseils de dépannage pour les erreurs courantes lors de l’utilisation de Azure Migrate avec des points de terminaison privés.
author: SGSneha
ms.author: v-ssudhir
ms.manager: deseelam
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: 9e987b4db88379e0dfe40b8839b073b893811fb4
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110547605"
---
# <a name="troubleshoot-network-connectivity"></a>Résoudre les problèmes de connectivité réseau
Cet article vous aide à résoudre les problèmes de connectivité réseau à l’aide d’Azure Migrate avec des points de terminaison privés.

## <a name="validate-private-endpoints-configuration"></a>Valider la configuration des points de terminaison privés

Assurez-vous que le point de terminaison privé présente l’état Approuvé.  

1. Accédez à **Azure Migrate** : pages **Découverte et évaluation** et **Migration de serveur**.

2. La page de propriétés contient la liste des points de terminaison privés et des FQDN de liaison privée créés automatiquement par Azure Migrate.  

3. Sélectionnez le point de terminaison privé que vous souhaitez diagnostiquer.  
   a. Vérifiez que l’état de la connexion indique Approuvée.           
   b. Si la connexion présente l’état En attente, vous devez l’approuver.                         
   c. Vous pouvez également accéder à la ressource de point de terminaison privé et vérifier si le réseau virtuel correspond au réseau virtuel de point de terminaison privé du projet Migrate.                                                        

     ![Afficher une connexion de point de terminaison privé](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)


## <a name="validate-the-data-flow-through-the-private-endpoints"></a>Valider le flux de données via les points de terminaison privés
Passez en revue les métriques de flux de données pour vérifier le flux de trafic via les points de terminaison privés. Sélectionnez le point de terminaison privé dans la page Azure Migrate : évaluation du serveur et propriétés Server Migration. Ce faisant, vous êtes redirigé vers la section de présentation des points de terminaison privés dans le centre Azure Private Link. Dans le menu de gauche, sélectionnez **Métriques** pour afficher les informations _Octets de données entrants_ et _Octets de données sortants_ et visualiser le flux de trafic.

## <a name="verify-dns-resolution"></a>Vérifier la résolution de DNS

L’appliance locale (ou le fournisseur de réplication) accède aux ressources Azure Migrate à l’aide de leur nom de domaine de liaison privée complet (FQDN). Vous pouvez avoir besoin de paramètres DNS supplémentaires pour résoudre l’adresse IP privée du point de terminaison privé à partir de l’environnement source. [Consultez cet article](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder) permet de comprendre les scénarios de configuration DNS qui peuvent vous aider à résoudre les problèmes de connectivité réseau.  

Pour valider la connexion de liaison privée, effectuez une résolution DNS des points de terminaison de ressource d’Azure Migrate (FQDN de ressource de liaison privée) à partir du serveur local hébergeant l’appliance Migrate, et assurez-vous qu’elle soit résolue vers une adresse IP privée.
Les informations sur les détails du point de terminaison privé et sur les FQDN de la ressource de liaison privée sont disponibles dans les pages de propriétés Découverte et évaluation et Migration de serveur. Sélectionnez **Télécharger les paramètres DNS** pour afficher la liste.   

 ![Propriétés Azure Migrate : découverte et évaluation](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 [![Propriétés Azure Migrate : migration de serveur](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-expanded.png#lightbox)

Exemple illustrant la résolution DNS du FQDN de la liaison privée du compte de stockage.  

- Entrez _nslookup<nom-compte-stockage>_ .blob.core.windows.net.  Remplacez <nom-compte-stockage> par le nom du compte de stockage utilisé pour Azure Migrate.  

    Vous recevrez un message similaire à celui ci :  

   ![Exemple de résolution DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- L’adresse IP privée 10.1.0.5 est retournée pour le compte de stockage. Cette adresse appartient au sous-réseau du réseau virtuel du point de terminaison privé.   

Vous pouvez vérifier la résolution DNS pour d’autres artefacts Azure Migrate à l’aide d’une approche similaire.   

Si la résolution DNS est incorrecte, procédez comme suit :  

- Si vous utilisez un DNS personnalisé, vérifiez vos paramètres DNS personnalisés et confirmez que la configuration DNS est correcte. Pour obtenir de l’aide, consultez [Vue d’ensemble du point de terminaison privé : configuration DNS](../private-link/private-endpoint-overview.md#dns-configuration).
- Si vous utilisez des serveurs DNS fournis par Azure, reportez-vous à la section ci-dessous pour une résolutions des problèmes plus approfondie.  

> [!Tip]
> Vous pouvez mettre à jour manuellement les enregistrements DNS de votre environnement source en modifiant le fichier hôte DNS sur votre appliance locale avec les FQDN des ressources de liaison privée et leurs adresses IP privées associées. Cette option n’est recommandée qu’à des fins de test. <br/>  


## <a name="validate-the-private-dns-zone"></a>Valider la zone DNS privée   
Si la résolution DNS ne fonctionne pas comme décrit dans la section précédente, il peut y avoir un problème avec votre zone DNS privée.  

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Confirmer l’existence de la ressource de zone DNS privée requise  
Par défaut, Azure Migrate crée également une zone DNS privée correspondant au sous-domaine *privatelink* pour chaque type de ressource. La zone DNS privée est créée dans le même groupe de ressources Azure que le groupe de ressources du point de terminaison privé. Le groupe de ressources Azure doit contenir des ressources de zone DNS privée au format suivant :
- pprivatelink.vaultcore.azure.net pour le coffre de clés
- privatelink.blob.core.windows.net pour le compte de stockage
- privatelink.siterecovery.windowsazure.com pour le coffre recovery services (pour les réplications Hyper-V et basées sur un agent)
- privatelink.prod.migration.windowsazure.com : migrez un projet, un projet d’évaluation et un site de détection.   

Azure Migrate crée automatiquement la zone DNS privée (sauf pour le compte de stockage de cache/de réplication sélectionné par l’utilisateur). Vous pouvez localiser la zone DNS privée liée en accédant à la page du point de terminaison privé et en sélectionnant les configurations DNS. Ici, vous devriez voir la zone DNS privée sous la section intégration DNS privée.

[![Capture d’écran de la configuration DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-expanded.png#lightbox)

Si la zone DNS n’est pas présente (comme indiqué ci-dessous), [créez une nouvelle ressource de zone DNS privée.](../dns/private-dns-getstarted-portal.md)  

[![Créer une zone DNS privée](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-expanded.png#lightbox)

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Vérifier que la zone DNS privée est liée au réseau virtuel  
La zone DNS privée doit être liée au réseau virtuel qui contient le point de terminaison privé pour que la requête DNS résolve l’adresse IP privée du point de terminaison de la ressource. Si la zone DNS privée n’est pas liée au réseau virtuel approprié, toute résolution DNS à partir de ce réseau virtuel ignore la zone DNS privée.   

Accédez à la ressource de zone DNS privée dans le portail Azure et cliquez sur les liens du réseau virtuel à partir du menu. Vous devez voir les réseaux virtuels liés.

[![Voir les liens de réseau virtuel](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-expanded.png#lightbox)

Cette opération affiche une liste de liens, chacun avec le nom d’un réseau virtuel dans votre abonnement. Le réseau virtuel qui contient la ressource de point de terminaison privé doit être répertorié ici. Sinon, [suivez cet article](../dns/private-dns-getstarted-portal.md#link-the-virtual-network) pour lier la zone DNS privée à un réseau virtuel.    

Une fois la zone DNS privée liée au réseau virtuel, les requêtes DNS provenant du réseau virtuel recherchent des enregistrements DNS dans la zone DNS privée. C’est obligatoire pour une résolution d’adresse correcte sur le réseau virtuel sur lequel le point de terminaison privé a été créé.   

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Confirmer que la zone DNS privée contient l’enregistrement A approprié

Accédez à la zone DNS privée que vous souhaitez dépanner. La page de présentation affiche tous les enregistrements DNS pour cette zone DNS privée. Vérifiez qu’un enregistrement A DNS existe pour la ressource. La valeur de l’enregistrement A (l’adresse IP) doit être l’adresse IP privée des ressources. Si vous trouvez l’enregistrement A mais qu’il contient une adresse IP incorrecte, vous devez supprimer l’adresse IP incorrecte et en ajouter une nouvelle. Il est recommandé de supprimer l’intégralité de l’enregistrement A et d’en ajouter un nouveau, puis d’effectuer un vidage DNS sur l’appliance source locale.   

Exemple illustrant l’enregistrement A DNS d’un compte de stockage dans la zone DNS privée :

![Enregistrements DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Exemple illustrant les enregistrements A DNS pour les microservices du coffre Recovery Services dans la zone DNS privée :

[![Enregistrements DNS pour le coffre Recovery Services](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-expanded.png#lightbox)  

>[!Note]
> Lorsque vous supprimez ou modifiez un enregistrement A, la machine peut toujours résoudre l’ancienne adresse IP car la TTL (durée de vie) n’a peut-être pas encore expiré.  

### <a name="items-that-may-affect-private-link-connectivity"></a>Éléments susceptibles d’affecter la connectivité de liaison privée  

Il s’agit d’une liste non exhaustive d’éléments qui peuvent être présents dans des scénarios avancés ou complexes :

- Les paramètres du Pare-feu, qu’il s’agisse du Pare-feu Azure connecté au réseau virtuel ou d’une solution de pare-feu personnalisée déployée dans la machine de l’appliance.  
- Le Peering de réseau, qui peut avoir un impact sur les serveurs DNS utilisés et sur la façon dont le trafic est acheminé.  
- Des solutions de passerelle personnalisée (NAT) peuvent avoir un impact sur la façon dont le trafic est acheminé, notamment le trafic des requêtes DNS.

Pour plus d’informations, consultez le [guide Résoudre les problèmes de connectivité Private Endpoint.](../private-link/troubleshoot-private-endpoint-connectivity.md)  
