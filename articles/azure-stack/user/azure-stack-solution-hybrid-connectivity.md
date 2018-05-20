---
title: Configurer l’identité de cloud hybride avec Azure et Azure Stack | Microsoft Docs
description: Découvrez comment configurer l’identité de cloud hybride avec Azure et Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 048e2636aabe406728c8fe1b93ef861f13346256
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Tutoriel : Configurer l’identité de cloud hybride avec Azure et Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez accéder aux ressources en toute sécurité dans Azure global et Azure Stack à l’aide du modèle de connectivité hybride. 

Dans ce tutoriel, vous allez créer un exemple d’environnement pour :

> [!div class="checklist"]
> - Conserver les données localement pour garantir la confidentialité ou le respect des obligations réglementaires, tout en gardant l’accès aux ressources Azure globales.
> - Gérer un système hérité tout en utilisant un déploiement d’application avec une mise à l’échelle dans le cloud et des ressources dans Azure global.

## <a name="prerequisites"></a>Prérequis


Certains composants sont nécessaires pour créer un déploiement de connectivité hybride, et leur préparation peut prendre un certain temps. 

Un partenaire OEM/matériel Azure peut déployer un système Azure Stack de production, et tous les utilisateurs peuvent déployer un kit ASDK. Un opérateur Azure Stack doit également déployer App Souscription, créer des plans et des offres, créer un abonnement client et ajouter l’image Windows Server 2016. 

Si vous disposez déjà de certains de ces composants, vérifiez qu’ils répondent aux conditions requises avant de commencer.

Cette rubrique suppose également que vous connaissez déjà Azure et Azure Stack. Si vous voulez en savoir plus avant de continuer, veillez à commencer par les rubriques suivantes :
 - [Présentation de Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Concepts clés d’Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure
 - Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
 - Créez une [application web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) dans Azure. Notez la nouvelle URL de l’application web, car nous l’utiliserons ultérieurement.

### <a name="azure-stack"></a>Azure Stack
 - Utilisez votre système Azure Stack de production ou déployez le Kit de développement Azure Stack à l’aide du lien ci-dessous :
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - Comme l’installation prend généralement quelques heures, adaptez votre planification en conséquence.
 - Déployez les services PaaS [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) sur Azure Stack. 
 - [Créez un plan/des offres](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) dans l’environnement Azure Stack. 
 - [Créez un abonnement client](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dans l’environnement Azure Stack. 


### <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration :

 - Vérifiez que vous disposez d’une adresse IPv4 publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
 - Assurez-vous que toutes les ressources sont déployées dans la même région/le même emplacement.

#### <a name="example-values"></a>Valeurs utilisées dans l’exemple

Nous utilisons les valeurs suivantes dans les exemples de cet article. Vous pouvez utiliser ces valeurs pour créer un environnement de test ou vous y référer pour mieux comprendre les exemples de cet article. Pour plus d’informations sur les paramètres de la passerelle VPN, voir [À propos des paramètres de la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Spécifications de la connexion :
 - **Type de VPN** : Basé sur itinéraires
 - **Type de connexion :** Site-to-site (IPsec)
 - **Type de passerelle** : VPN
 - **Nom de la connexion Azure** : Azure-Gateway-AzureStack-S2SGateway (le portail fournira automatique cette valeur)
 - **Nom de la connexion Azure Stack** : AzureStack-Gateway-Azure-S2SGateway (le portail fournira automatique cette valeur)
 - **Clé partagée** : toute clé compatible avec le matériel VPN, avec des valeurs correspondantes des deux côtés de la connexion
 - **Abonnement** : un abonnement de votre choix
 - **Groupe de ressources** : Test-Infra

| Connexion Azure/Azure Stack | NOM | Sous-réseau | Adresse IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Réseau virtuel Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | Sous-réseau de passerelle<br>10.100.103.0/24 |  |
| Réseau virtuel Azure Stack | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | Sous-réseau de passerelle <br>10.100101.0/24 |  |
| Passerelle de réseau virtuel Azure | Azure-Gateway |  |  |
| Passerelle de réseau virtuel Azure Stack | AzureStack-Gateway |  |  |
| Adresse IP publique | Azure-GatewayPublicIP |  | Déterminée à la création |
| Adresse IP publique Azure Stack | AzureStack-GatewayPublicIP |  | Déterminée à la création |
| Passerelle du réseau local Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Valeur d’adresse IP publique Azure Stack |
| Passerelle de réseau local Azure Stack | Azure-S2SGateway<br>10.100.102.0/23 |  | Adresse IP publique Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Créer un réseau virtuel dans Azure global et Azure Stack

> [!Note]  
> Vous devez vous assurer que les adresses IP ne se chevauchent pas dans les espaces d’adressage du réseau virtuel Azure ou Azure Stack. 

Pour créer un réseau virtuel dans le modèle de déploiement Resource Manager à l’aide du portail Azure : Utilisez les [valeurs d’exemple](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) si vous utilisez ces étapes sous forme de didacticiel. Si vous n’effectuez pas ces étapes sous forme d’un didacticiel, veillez à remplacer les valeurs par les vôtres. 

1. Dans un navigateur, accédez au [portail Azure](http://portal.azure.com/) et connectez-vous avec votre compte Azure.
2. Cliquez sur **Créer une ressource**. Dans le champ **Rechercher dans le marketplace**, entrez `virtual network`. Localisez **Réseau virtuel** dans la liste renvoyée et ouvrez la page **Réseau virtuel**.
3. En bas de la page Réseau virtuel, à partir de la liste **Sélectionner un modèle de déploiement**, choisissez **Gestionnaire des ressources** puis sélectionnez **Créer**. La page Créer un réseau virtuel s’ouvre.
4. Sur la page **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Lorsque vous renseignez les champs, le point d’exclamation rouge se transforme en coche verte si les caractères saisis dans le champ sont valides.
5. Répétez ces étapes à partir du **portail locataire** d’Azure Stack.

## <a name="add-a-gateway-subnet"></a>Ajouter un sous-réseau de passerelle

Avant de connecter votre réseau virtuel à une passerelle, vous devez créer le sous-réseau de passerelle pour le réseau virtuel auquel vous souhaitez vous connecter. Les adresses de passerelle utilisent les adresses spécifiées dans le sous-réseau de passerelle.

Dans le [portail](http://portal.azure.com/), accédez au réseau virtuel Resource Manager pour lequel vous souhaitez créer une passerelle de réseau virtuel.

1. Dans la section **Paramètres** de la page de votre réseau virtuel, sélectionnez **Sous-réseaux** pour développer la page **Sous-réseaux**.
2. Sur la page **Sous-réseaux**, sélectionnez **+Sous-réseau de passerelle** pour ouvrir la page **Ajouter un sous-réseau**.

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. Le **Nom** de votre sous-réseau est automatiquement rempli avec la valeur « GatewaySubnet ». Cette valeur est nécessaire pour qu’Azure puisse reconnaître le sous-réseau en tant que sous-réseau de passerelle. Ajustez le remplissage automatique des valeurs de la **plage d’adresses** pour correspondre à votre configuration requise, puis sélectionnez **OK** au bas de la page pour créer le sous-réseau.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Créer une passerelle de réseau virtuel dans Azure et Azure Stack

1. Sur le côté gauche de la page du portail, sélectionnez le signe + puis tapez « Passerelle de réseau virtuel » dans la barre de recherche. Dans **Résultats**, recherchez et sélectionnez **Passerelle de réseau virtuel**.
2. En bas de la page **Passerelle de réseau virtuel**, sélectionnez **Créer**. La page **Créer une passerelle de réseau virtuel** s’ouvre.
3. Sur la page **Créer une passerelle de réseau virtuel**, spécifiez les valeurs de votre passerelle de réseau virtuel, comme indiqué dans les exemples de valeurs et dans les valeurs supplémentaires détaillées ci-dessous.
    - **Référence (SKU)** : De base
    - **Réseau virtuel** : sélectionnez le réseau virtuel que vous avez créé précédemment. Cette option sélectionne automatiquement le sous-réseau de passerelle que vous avez créé précédemment. 
    - **Première configuration IP** : adresse IP publique de votre passerelle. 
        - Cliquez sur **Créer une configuration d’IP de passerelle** pour ouvrir la page **Choisir une adresse IP publique**.
        - Cliquez sur **+Créer nouveau** pour ouvrir la page **Créer une adresse IP publique**.
        - Donnez un **nom** à votre adresse IP publique. Laissez la référence (SKU) sur **De base**, puis sélectionnez **OK** au bas de cette page pour enregistrer vos modifications.

    > [!Note]  
    > Actuellement, la passerelle VPN prend uniquement en charge l’allocation d’adresses IP publiques dynamiques. Toutefois, cela ne signifie pas que l’adresse IP change après son affectation à votre passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Elle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN.

4. Vérifiez les paramètres. 
5. Cliquez sur **Créer** pour créer la passerelle VPN. Les paramètres sont validés et la vignette Déploiement d’une passerelle de réseau virtuel s’affiche sur le tableau de bord. La création d’une passerelle peut prendre jusqu’à 45 minutes Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.

    Une fois la passerelle créée, examinez le réseau virtuel dans le portail pour obtenir l’adresse IP affectée à la passerelle. Cette dernière apparaît sous la forme d’un appareil connecté. Vous pouvez sélectionner l’appareil connecté (votre passerelle de réseau virtuel) pour afficher davantage d’informations.
6. Répétez ces étapes sur votre déploiement Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Créer une passerelle de réseau local dans Azure et Azure Stack

La passerelle de réseau local fait généralement référence à votre emplacement local. Donnez au site un nom auquel Azure ou Azure Stack pourra se référer, puis spécifiez l’adresse IP du périphérique VPN local vers lequel vous allez créer une connexion. Spécifiez également les préfixes d’adresses IP qui seront acheminés via la passerelle VPN vers le périphérique VPN. Les préfixes d’adresses que vous spécifiez sont les préfixes situés sur votre réseau local. En cas de modification du réseau ou si vous devez modifier l’adresse IP publique de l’appareil VPN, il est simple de mettre à jour les valeurs ultérieurement.

1. Dans le portail Azure, sélectionnez **+Créer une ressource**.
2. Dans la zone de recherche, tapez **passerelle de réseau local**, puis appuyez sur **Entrée** pour lancer la recherche. Une liste de résultats apparaîtra. Cliquez sur **Passerelle de réseau local**, puis sélectionnez le bouton **Créer** pour ouvrir la page **Créer une passerelle de réseau local**.
3. Sur la page **Créer une passerelle de réseau local**, spécifiez les valeurs de votre passerelle de réseau local, comme indiqué dans nos exemples de valeurs et dans les valeurs supplémentaires détaillées ci-dessous.
    - **Adresse IP :** il s’agit de l’adresse IP publique du périphérique VPN auquel vous souhaitez qu’Azure ou Azure Stack se connecte. Spécifiez une adresse IP publique valide. L’adresse IP ne peut pas se trouver derrière NAT et doit être accessible par Azure. Si vous ne disposez pas de l’adresse IP actuellement, vous pouvez utiliser les valeurs indiquées dans l’exemple. Toutefois, vous devrez revenir en arrière et remplacer votre adresse IP d’espace réservé par l’adresse IP publique de votre périphérique VPN. Dans le cas contraire, Azure ne sera pas en mesure de se connecter.
    - **Espace d’adressage** fait référence aux plages d’adresses du réseau qui représente ce réseau local. Vous pouvez ajouter plusieurs plages d’espaces d’adressage. Assurez-vous que les plages que vous spécifiez ici ne se chevauchent pas avec les plages d’autres réseaux auxquels vous souhaitez vous connecter. Azure achemine la plage d’adresses que vous spécifiez vers l’adresse IP du périphérique VPN local. Utilisez ici vos propres valeurs (et non les valeurs indiquées dans l’exemple) si vous voulez vous connecter à votre site local.
    - **Configurer les paramètres de BGP** : sélectionnez cette option uniquement lors de la configuration BGP. pour la configuration du protocole BGP.
    - **Abonnement** : vérifiez que l’abonnement approprié s’affiche.
    - **Groupe de ressources :** sélectionnez le groupe de ressources que vous souhaitez utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un déjà créé.
    - **Emplacement** : sélectionnez l’emplacement dans lequel cet objet sera créé. Vous pouvez sélectionner l’emplacement dans lequel se trouve votre réseau virtuel, mais vous n’êtes pas obligé de le faire.
4. Quand vous avez terminé de spécifier les valeurs, sélectionnez le bouton **Créer** en bas de la page pour créer la passerelle de réseau local.
5. Répétez ces étapes sur votre déploiement Azure Stack.

## <a name="configure-your-connection"></a>Configurer votre connexion

Les connexions site à site vers un réseau local nécessitent un périphérique VPN. Dans cette étape, vous configurez votre périphérique VPN (une connexion). Pour configurer votre connexion, vous avez besoin des éléments suivants :
    - Une clé partagée. Il s’agit de la clé partagée spécifiée lors de la création de la connexion VPN de site à site. Dans nos exemples, nous utilisons une clé partagée basique. Nous vous conseillons de générer une clé plus complexe.
    - L’adresse IP publique de votre passerelle de réseau virtuel. Vous pouvez afficher l’adresse IP publique à l’aide du portail Azure, de PowerShell ou de l’interface de ligne de commande. Pour rechercher l’adresse IP publique de votre passerelle VPN à l’aide du portail Azure, accédez à Passerelles de réseau virtuel, puis sélectionnez le nom de votre passerelle.
Créez la connexion VPN de site à site entre votre passerelle de réseau virtuel et votre périphérique VPN local.
1. Dans le portail Azure, sélectionnez **+Créer une ressource**.
2. Dans la zone de recherche, tapez **Connexions**, puis appuyez sur **Entrée** pour lancer la recherche. Une liste de résultats apparaîtra. Cliquez sur **Connexions**, puis sélectionnez le bouton Créer pour ouvrir la page **Créer des connexions**.
3. Sur la page **Créer des connexions**, configurez les valeurs de votre connexion.
     - Paramètres de base :
        1. **Type de connexion** : sélectionnez Site à site (IPSec).
        2. **Groupe de ressources** : (sélectionnez votre groupe de ressources de test)
     - Paramètres :
        1. **Passerelle de réseau virtuel** : sélectionnez la passerelle de réseau virtuel que vous avez créée précédemment.
        2. **Passerelle de réseau local** : sélectionnez la passerelle de réseau local que vous avez créée précédemment. 
        3. **Nom de la connexion** : ce champ affichera automatiquement les valeurs provenant des deux passerelles. 
        4. **Clé partagée** : la valeur doit correspondre à celle que vous utilisez pour votre périphérique VPN local. Dans cet exemple, nous avons utilisé « abc123 », mais vous pouvez (et devriez) utiliser une valeur plus complexe. L’important, c’est que la valeur que vous spécifiez ici doit être identique à celle spécifiée lors de la configuration de votre périphérique VPN.
    - Les autres valeurs pour **abonnement**, **groupe de ressources**, et **emplacement** sont fixes.
4. Cliquez sur **OK** pour créer votre connexion. Le message Création de la connexion clignote à l'écran.
5. Vous pouvez afficher la connexion dans la page **Connexions de la passerelle de réseau virtuel. L’état passe de **Inconnu** à **Connexion**, puis à **Réussi**.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles cloud Azure, voir [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
