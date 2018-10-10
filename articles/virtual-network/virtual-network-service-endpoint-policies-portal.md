---
title: Créer et associer des stratégies de point de terminaison de service - Portail Azure | Microsoft Docs
description: Dans cet article, découvrez comment configurer et associer des stratégies de point de terminaison de service à l’aide du portail Azure.
services: virtual-network
documentationcenter: virtual-network
author: anithaa
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: anithaa
ms.openlocfilehash: 8c750937cd45e104e754335f7da51d813b8dc7ba
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999645"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Créer, changer ou supprimer une stratégie de point de terminaison de service à l’aide du portail Azure

Les stratégies de point de terminaison de service vous permettent de filtrer le trafic de réseau virtuel sur des ressources Azure spécifiques sur des points de terminaison de service. Si vous n’êtes pas familiarisé avec les stratégies de point de terminaison de service, consultez la [vue d’ensemble des stratégies de point de terminaison de service](virtual-network-service-endpoint-policies-overview.md) pour en savoir plus.

 Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une stratégie de point de terminaison de service
> * Créer une définition de stratégie de point de terminaison de service
> * Créer un réseau virtuel avec un sous-réseau
> * Associer une stratégie de point de terminaison de service à un sous-réseau

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure sur http://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Créer une stratégie de point de terminaison de service

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Dans le volet de recherche, tapez « Stratégie de point de terminaison de service », sélectionnez **Stratégie de point de terminaison de service (préversion)**, puis sélectionnez **Créer**.
3. Entrez ou sélectionnez les informations suivantes dans **Informations de base** : 

   - Abonnement : sélectionnez votre abonnement pour la stratégie.    
   - Groupe de ressources : sélectionnez **Créer**, puis entrez *myResourceGroup*.     
   - Nom : myEndpointPolicy
   - Emplacement : West Central US     
 
   ![Informations de base sur la création d’une stratégie de point de terminaison de service](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. Entrez ou sélectionnez les informations suivantes dans **Définitions de stratégie** :

   - Sous **+ Ajouter une ressource**, entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **Ajouter**.  
   - Étendue : sélectionnez **Compte unique** ou **Tous les comptes de l’abonnement** ou **Tous les comptes du groupe de ressources**.    
   - Abonnement : sélectionnez votre abonnement pour le compte de stockage. La stratégie et les comptes de stockage peuvent se trouver dans des abonnements différents.   
   - Groupe de ressources : sélectionnez votre groupe de ressources. Obligatoire si l’étendue est définie avec la valeur « Tous les comptes du groupe de ressources » ou « Compte unique ».  
   - Ressource : mystorageaccountportal    
   - Cliquez sur **+ Ajouter une ressource** pour continuer à ajouter des ressources supplémentaires.
   
   ![Créer des définitions de stratégie de point de terminaison de service](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. Facultatif : entrez ou sélectionnez les informations suivantes dans **Étiquettes** :
   
   - Clé : sélectionnez votre clé pour la stratégie. Exemple : Département     
   - Valeur : entrez la paire de valeur pour la clé. Exemple : Finances

6. Sélectionnez **Vérifier + créer**. Validez les informations, puis cliquez sur **Créer**. Pour apporter d’autres modifications, cliquez sur **Précédent**. 

   ![Créer des validations finales de stratégie de point de terminaison de service](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>Afficher des stratégies de points de terminaison 

1. Dans la zone *Tous les services* du portail, commencez à taper *stratégies de point de terminaison de service*. Sélectionnez **Stratégies de point de terminaison de service (préversion)**.
2. Sous **Abonnements**, sélectionnez votre abonnement et votre groupe de ressources, comme illustré dans l’image suivante.

   ![Afficher une stratégie](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. Sélectionnez la stratégie, puis cliquez sur **Définitions de stratégie** pour afficher ou ajouter des définitions de stratégie supplémentaires.

   ![Afficher des définitions de stratégie](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. Sélectionnez **Sous-réseaux associés** pour afficher les sous-réseaux auxquels la stratégie est associée. Pour associer une stratégie à un sous-réseau, cliquez sur « Accéder à un réseau virtuel de la même région ».

   ![Afficher des sous-réseaux associés](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>Associer une stratégie à un sous-réseau

>[!WARNING] 
> Vérifiez que toutes les ressources ayant fait l’objet d’un accès à partir du sous-réseau, pour le service sélectionné, sont ajoutées à la stratégie avant d’associer cette dernière. Une fois la stratégie associée, seul l’accès aux ressources répertoriées dans la stratégie est autorisé pour les régions de point de terminaison pour le service. 

Avant de pouvoir associer une stratégie à un sous-réseau, vous devez créer un réseau virtuel et un sous-réseau :

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.
3. Sous **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis sélectionnez **Créer** :
   - Nom : myVirtualNetwork      
   - Espace d’adressage : 10.0.0.0/16      
   - Abonnement : sélectionnez votre abonnement. La stratégie doit se trouver dans le même abonnement que le réseau virtuel     
   - Groupe de ressources : sélectionnez **Utiliser l’existant**, puis *myResourceGroup*     
   - Emplacement : West Central US     
   - Nom du sous-réseau : private     
   - Plage d’adresses : 10.0.0.0/24
     
4. Dans le champ **Rechercher des ressources, services et documents** en haut du portail, commencez à taper *myVirtualNetwork*. Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la.
5. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**, puis **private**.
6. Comme indiqué dans l’image suivante, sélectionnez **Points de terminaison de service**, sélectionnez **Microsoft.Storage**, **Stratégies de point de terminaison de service**,  **myEndpointPolicy**, puis **Enregistrer** :

   ![Associer une stratégie](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>L’accès à des ressources du service dans d’autres régions sera autorisé à partir de ce sous-réseau, en fonction des groupes de sécurité réseau (NSG). Pour retreindre l’accès aux seules les régions de point de terminaison, limitez les groupes de sécurité réseau au seul trafic de service dans les régions de point de terminaison. Pour plus d’informations sur la création de groupes de sécurité réseau avec des balises de service par région, consultez [Balises de service Azure pour les groupes de sécurité réseau](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json).

Dans l’exemple ci-dessous, le groupe de sécurité réseau est soumis à des restrictions pour accéder uniquement aux ressources du Stockage Azure dans WestCentralUS et WestUS2, avec une règle « Refuser tout » comme règle de priorité inférieure.

![Refuser tous les groupes de sécurité réseau](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé une stratégie de point de terminaison de service que vous avez associée à un sous-réseau. Pour en savoir plus sur les stratégies de point de terminaison de service, consultez la [vue d’ensemble des stratégies de point de terminaison de service](virtual-network-service-endpoint-policies-overview.md).

