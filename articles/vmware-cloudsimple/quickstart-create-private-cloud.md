---
title: La VMware Solution Azure par CloudSimple Quickstart - créer un cloud privé
description: Découvrez comment créer et configurer un cloud privé avec Azure VMware Solution par CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e1fc7809ad94d589483b87c638d027a39098164e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209534"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Guide de démarrage rapide - configurer un environnement de cloud privé

Dans cet article, découvrez comment créer un cloud privé CloudSimple et configurer votre environnement de cloud privé.

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Créer un cloud privé

1. Sélectionnez **Tous les services**.
2. Recherchez **CloudSimple Services**.
3. Sélectionnez le service CloudSimple sur lequel vous souhaitez créer votre Cloud privé.
4. Vue d’ensemble, cliquez sur **créer un Cloud privé** pour ouvrir un nouvel onglet de navigateur pour CloudSimple portail.  Si vous y êtes invité, connectez-vous avec votre Azure connectez-vous aux informations d’identification.  

    ![Créer un Cloud privé à partir d’Azure](media/create-private-cloud-from-azure.png)

5. Dans le portail CloudSimple, fournissez un nom pour votre Cloud privé
6. Sélectionnez le **emplacement** de votre nuage privé
7. Sélectionnez le **type de nœud** que vous avez achetées sur Azure.  Vous pouvez choisir le [option CS28 ou CS36](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). Cette dernière option inclut la capacité de calcul et de mémoire maximale.
8. Spécifiez le **nombre de nœuds**.  Les nœuds au minimum trois sont requises pour créer un Cloud privé

    ![Créer le Cloud privé - informations de base](media/create-private-cloud-basic-info.png)

9. Cliquez sur **Suivant : Les options avancées**.
10. Entrez la plage CIDR pour les sous-réseaux de vSphere/vSAN. Assurez-vous que la plage CIDR ne chevauche pas votre réseau local ou d’autres sous-réseaux Azure.

    ![Créer le Cloud privé - Options avancées](media/create-private-cloud-advanced-options.png)

11. Sélectionnez **Suivant : Passez en revue et créer**.
12. Passez en revue les paramètres. Si vous avez besoin modifier des paramètres, cliquez sur **précédent**.
13. Cliquez sur **Créer**.

Cloud privé processus d’approvisionnement sera démarré.  Il peut prendre jusqu'à deux heures pour le Cloud privé à mettre en service.

## <a name="launch-cloudsimple-portal"></a>Lancez CloudSimple portail

Vous pouvez accéder CloudSimple portail à partir du portail Azure.  Portail de CloudSimple sera lancé avec votre Azure connectez-vous aux informations d’identification à l’aide de l’authentification unique (SSO).  Accès au portail CloudSimple, vous devez autoriser le **autorisation du Service CloudSimple** application.  Pour plus d’informations sur l’octroi d’autorisations, consultez [consentement pour une application de l’autorisation du Service CloudSimple](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. Sélectionnez **Tous les services**.
2. Recherchez **CloudSimple Services**.
3. Sélectionnez le service CloudSimple sur lequel vous souhaitez créer votre Cloud privé.
4. Vue d’ensemble, cliquez sur **accéder au portail CloudSimple** pour ouvrir un nouvel onglet de navigateur pour CloudSimple portail.  Si vous y êtes invité, connectez-vous avec votre Azure connectez-vous aux informations d’identification.  

    ![Lancez CloudSimple portail](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Créer le point-to-site VPN

Une connexion VPN point à site est le plus simple pour vous connecter à votre Cloud privé à partir de votre ordinateur. Utilisez la connexion VPN point à site si vous vous connectez à distance dans le Cloud privé.  Pour accéder rapidement à votre Cloud privé, suivez les étapes ci-dessous.  Accès à la région CloudSimple à partir de votre réseau local peut être effectuée à l’aide [VPN de Site à Site](https://docs.azure.cloudsimple.com/vpn-gateway/) ou [Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/).

### <a name="create-gateway"></a>Créer une passerelle

1. Lancez CloudSimple portail et sélectionnez **réseau**.
2. Sélectionnez **passerelle VPN**.
3. Cliquez sur **nouvelle passerelle VPN**.

    ![Créer la passerelle VPN](media/create-vpn-gateway.png)

4. Pour **configuration de la passerelle**, spécifiez les paramètres suivants et cliquez sur **suivant**.

    * Sélectionnez **Point-to-Site VPN** comme type de passerelle.
    * Entrez un nom pour identifier la passerelle.
    * Sélectionnez l’emplacement Azure où votre service CloudSimple est déployé.
    * Spécifiez le sous-réseau du client pour la passerelle de point-to-site.  Adresses DHCP seront accordées à partir de ce sous-réseau lorsque vous vous connectez.

5. Pour **connexion/utilisateur**, spécifiez les paramètres suivants et cliquez sur **suivant**.

    * Pour autoriser automatiquement tous les utilisateurs actuels et futurs pour accéder au nuage privé via cette passerelle point à site, sélectionnez **ajouter automatiquement tous les utilisateurs**. Lorsque vous sélectionnez cette option, tous les utilisateurs dans la liste des utilisateurs sont automatiquement sélectionnés. Vous pouvez remplacer l’option automatique en désélectionnant les utilisateurs individuels dans la liste.
    * Pour sélectionner uniquement les utilisateurs individuels, cliquez sur les cases à cocher dans la liste des utilisateurs.

6. La section de réseaux locaux virtuels/sous-réseaux vous permet de spécifier utilisateur réseaux locaux virtuels/sous-réseaux pour la passerelle et les connexions et gestion.

    * Le **automatiquement ajouter** options définir la stratégie globale pour cette passerelle. Les paramètres s’appliquent à la passerelle en cours. Les paramètres peuvent être remplacées dans le **sélectionnez** zone.
    * Sélectionnez **ajouter la gestion des réseaux locaux virtuels/sous-réseaux de Clouds privés**. 
    * Pour ajouter des réseaux locaux virtuels/sous-réseaux tout défini par l’utilisateur, cliquez sur **ajouter des réseaux locaux virtuels/sous-réseaux définis par l’utilisateur**. 
    * Le **sélectionnez** paramètres remplacent les paramètres généraux sous **automatiquement ajouter**. 

7. Cliquez sur **suivant** pour passer en revue les paramètres. Cliquez sur les icônes de la modifier pour apporter des modifications.
8. Cliquez sur **créer** pour créer la passerelle VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Se connecter à CloudSimple à l’aide de point-to-site VPN

Client VPN est nécessaire pour la connexion à CloudSimple à partir de votre ordinateur.  Télécharger [OpenVPN client](https://openvpn.net/community-downloads/) pour Windows ou [viscosité](https://www.sparklabs.com/viscosity/download/) pour Mac OS et OS X.

1. Lancez CloudSimple portail et sélectionnez **réseau**.
2. Sélectionnez **passerelle VPN**.
3. Dans la liste des passerelles VPN, cliquez sur la passerelle VPN de point-to-site.
4. Sélectionnez **Utilisateurs**.
5. Cliquez sur **télécharger ma configuration de VPN**

    ![Télécharger la configuration VPN](media/download-p2s-vpn-configuration.png)

6. Importer la configuration sur votre client VPN

    * Instructions pour [importation de la configuration sur le client de Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instructions pour [importation de la configuration sur Mac OS ou OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Se connecter à CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>Créer un réseau local virtuel pour votre charge de travail des machines virtuelles

Après avoir créé un cloud privé, créez un réseau local virtuel où vous déploierez votre charge de travail/application machines virtuelles.

1. Dans le portail CloudSimple, sélectionnez **réseau**.
2. Cliquez sur **VLAN/sous-réseaux**.
3. Cliquez sur **créer un réseau local virtuel/sous-réseau**

    ![Créer un réseau local virtuel/sous-réseau](media/create-new-vlan-subnet.png)

4. Sélectionnez le **Cloud privé** pour le nouveau réseau local virtuel/sous-réseau.
5. Sélectionnez un ID de réseau local virtuel dans la liste.  
6. Entrez un nom de sous-réseau pour identifier le sous-réseau.
7. Spécifiez la plage CIDR de sous-réseau et le masque.  Cette plage ne doit pas chevaucher les sous-réseaux existants.
8. Cliquez sur **Envoyer**.

    ![Créer des détails du réseau local virtuel/sous-réseau](media/create-new-vlan-subnet-details.png)

Le réseau local virtuel/sous-réseau sera créé.  Vous pouvez désormais utiliser cet ID de réseau local virtuel pour créer un groupe de ports distribué sur votre vCenter de Cloud privé. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Connecter votre environnement à un réseau virtuel Azure

CloudSimple vous offre un circuit ExpressRoute pour votre cloud privé. Vous pouvez vous connecter à votre réseau virtuel sur Azure au circuit ExpressRoute. Pour plus d’informations sur la configuration de la connexion, suivez les étapes de [connexion réseau virtuel Azure à l’aide d’ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)

## <a name="sign-in-to-vcenter"></a>Se connecter à vCenter

Vous pouvez maintenant signer à vCenter configurer des stratégies et des machines virtuelles.

1. Pour accéder à vCenter, démarrez à partir du portail CloudSimple. Sur la page d’accueil, sous **tâches courantes**, cliquez sur **lancer le Client vSphere**.  Sélectionnez le Cloud privé, puis cliquez **lancer le Client vSphere** sur le Cloud privé.

    ![Lancer le Client vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Sélectionnez votre client vSphere préféré pour accéder à vCenter et connectez-vous avec votre nom d’utilisateur et le mot de passe.  Les valeurs par défaut sont :
    * Nom d’utilisateur : **CloudOwner@cloudsimple.local**
    * Mot de passe : **CloudSimple123!**  

Les écrans de vCenter dans les procédures suivantes sont à partir du client vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Modifier votre mot de passe vCenter

CloudSimple recommande de modifier votre mot de passe la première fois que vous vous connectez au serveur vCenter.  
Le mot de passe que vous définissez doit remplir les conditions suivantes :

* Durée de vie maximale : Mot de passe doit être modifié chaque 365 jours
* Limiter la réutilisation : Les utilisateurs ne peut pas réutiliser un mot de cinq passe précédente
* Durée : 8 - 20 caractères
* Caractère spécial : Au moins un caractère spécial
* Caractères alphabétiques : Au moins un caractère en majuscule, A-Z et au moins un caractère minuscule, d’a à z
* Nombres : Au moins un caractère numérique, 0-9
* Nombre maximal de caractères adjacent identiques : Trois

    Exemple : CC ou CCC est acceptable dans le cadre du mot de passe, mais CCCC n’est pas.

Si vous définissez un mot de passe ne répond pas à la configuration requise :

* Si vous utilisez le Client Flash vSphere, il signale une erreur
* Si vous utilisez le client HTML5, il ne signale une erreur. Le client n’accepte pas la modification et l’ancien mot de passe continue de fonctionner.

## <a name="change-nsx-administrator-password"></a>Mot de passe administrateur modification NSX

Le gestionnaire NSX est déployé avec un mot de passe par défaut.  Nous vous recommandons de que modifier le mot de passe après avoir créé votre Cloud privé.

   * Nom d’utilisateur : **admin**
   * Mot de passe : **CloudSimple123!**

Vous pouvez trouver le nom de domaine complet (FQDN) et l’adresse IP du Gestionnaire de NSX sur CloudSimple portail.

1. Lancez CloudSimple portail et sélectionnez **ressources**.
2. Cliquez sur le Cloud privé, auquel vous souhaitez utiliser.
3. Sélectionnez **réseau de gestion vSphere**
4. Utiliser le nom de domaine complet ou adresse IP de **NSX Manager** et connectez-vous en utilisant un navigateur web. 

    ![Rechercher le nom de domaine complet NSX Manager](media/private-cloud-nsx-manager-fqdn.png)

Pour modifier le mot de passe, suivez les instructions de [Installation NSX Manager](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html).

## <a name="create-a-port-group"></a>Créer un groupe de ports

Pour créer un groupe de ports distribué dans vSphere :

1. Suivez les instructions de « Ajouter un groupe de ports distribué, » dans le [vSphere Guide de mise en réseau](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Lorsque vous configurez le groupe de ports distribué, fournissez l’ID de réseau local virtuel créé dans [créer un réseau local virtuel pour vos machines virtuelles de charge de travail](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser des machines virtuelles VMware sur Azure](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [Utiliser des machines virtuelles VMware sur Azure](quickstart-create-vmware-virtual-machine.md)
* [Se connecter au réseau local à l’aide d’Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Le programme d’installation de VPN de Site à Site en local](https://docs.azure.cloudsimple.com/vpn-gateway/)
