---
title: Se connecter à Azure Data Box Edge, le configurer et l’activer dans le Portail Azure | Microsoft Docs
description: Ce troisième tutoriel sur le déploiement de Data Box Edge vous apprend à connecter, configurer et activer votre appareil physique.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: e5f2ecd2cdff0ae5f3f5f086bde0741f7f6d2dbb
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121615"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge-preview"></a>Tutoriel : Se connecter à Azure Data Box Edge, le configurer et l’activer (préversion) 

Ce tutoriel explique comment vous connecter à votre appareil Azure Data Box Edge, comment le configurer et l’activer avec l’interface utilisateur web locale. 

Le processus de configuration et d’activation peut prendre 20 minutes environ. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Se connecter à un appareil physique
> * Configurer et activer l’appareil physique

> [!IMPORTANT]
> Data Box Edge est en préversion. Avant de commander et déployer cette solution, lisez les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Prérequis

Avant de configurer et d’activer votre appareil Data Box Edge, assurez-vous que :

* Vous avez installé l’appareil physique comme indiqué dans [Installer Data Box Edge](data-box-edge-deploy-install.md).
* Vous disposez de la clé d’activation, du service Data Box Edge que vous avez créé pour gérer l’appareil Data Box Edge. Pour plus d’informations, consultez [Préparer le déploiement d’Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Se connecter à la configuration de l’interface utilisateur web locale 

1. Configurez la carte Ethernet sur l’ordinateur que vous utilisez pour vous connecter à l’appareil Edge avec l’adresse IP statique 192.168.100.5 et le sous-réseau 255.255.255.0.

1. Connectez l’ordinateur au PORT 1 de votre appareil. 

1. Ouvrez une fenêtre de navigateur et accédez à l’interface utilisateur web locale de l’appareil à l’adresse https://192.168.100.10.  
    Cette action peut prendre quelques minutes après la mise sous tension de l’appareil. 

    Une erreur ou un avertissement vous indique qu’il existe un problème avec le certificat de sécurité du site web. 
   
    ![Message erreur de certificat de sécurité du site web](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

1. Sélectionnez **Poursuivre sur cette page web**.  
    Ces étapes peuvent varier en fonction du navigateur que vous utilisez.

1. Connectez-vous à l’interface utilisateur web de votre appareil physique. Le mot de passe par défaut est *Password1*. 
   
    ![Page de connexion de l’appareil Data Box Edge](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

1. À l’invite, modifiez le mot de passe d’administrateur de l’appareil.  
    Le nouveau mot de passe doit comporter entre 8 et 16 caractères. Il doit contenir trois des types de caractères suivants : majuscule, minuscule, chiffre et caractère spécial.

Vous vous trouvez maintenant au niveau du tableau de bord de votre appareil.

## <a name="set-up-and-activate-the-physical-device"></a>Configurer et activer l’appareil physique
 
Le tableau de bord présente les différents paramètres nécessaires à la configuration et à l’inscription de l’appareil physique auprès du service Data Box Edge. Les options **Nom de l’appareil**, **Paramètres réseau**, **Paramètres de proxy web** et **Paramètres de l’heure** sont facultatives. Les seuls paramètres obligatoires sont les **Paramètres cloud**.
   
![Tableau de bord de l’appareil Data Box Edge](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. Dans le volet gauche, sélectionnez **Nom de l’appareil**, puis entrez un nom convivial pour votre appareil.  
    Ce nom convivial doit contenir entre 1 et 15 caractères et doit se composer de lettres, de chiffres et de traits d’union.

    ![Page Nom de l’appareil](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

1. (Facultatif) Dans le volet gauche, sélectionnez **Paramètres réseau**, puis configurez les paramètres.  
    Votre appareil physique comporte six interfaces réseau. Le PORT 1 et le PORT 2 sont des interfaces réseau de 1 Gbit/s. Le PORT 3, le PORT 4, le PORT 5 et le PORT 6 sont tous des interfaces réseau de 25 Gbit/s. Le PORT 1 est automatiquement configuré comme port réservé à la gestion, et les PORTS 2 à 6 sont tous des ports de données. La page **Paramètres réseau** comme illustré ci-dessous.
    
    ![Page Paramètres réseau](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Lorsque vous configurez les paramètres réseau, gardez à l’esprit :

    - Si le protocole DHCP est activé dans votre environnement, les interfaces réseau sont configurées automatiquement. Une adresse IP, un sous-réseau, une passerelle et un DNS sont automatiquement attribués.
    - Si le protocole DHCP n’est pas activé, vous pouvez, le cas échéant, attribuer des adresses IP statiques.
    - Vous pouvez configurer votre interface réseau sur IPv4.

    >[!NOTE] 
    > Nous vous recommandons de ne pas changer de statique en DHCP l’adresse IP locale de l’interface réseau, sauf si vous avez une autre adresse IP pour vous connecter à l’appareil. Si vous utilisez une seule interface réseau et que vous passez à DHCP, il n’y a aucun moyen de déterminer l’adresse DHCP. Si vous voulez passer à une adresse DHCP, attendez que l’appareil se soit inscrit auprès du service, puis faites la modification. Vous pouvez ensuite voir les adresses IP de tous les adaptateurs dans les **propriétés de l’appareil** dans le portail Azure pour votre service.

1. (Facultatif) Dans le volet de gauche, sélectionnez **Paramètres du proxy Web**, puis configurez votre serveur proxy web. Bien que la configuration du proxy web soit facultative, si vous en utilisez un, vous pouvez uniquement le configurer sur cette page.
   
   ![Page Paramètres du proxy web](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Dans la page **Paramètres du proxy web**, procédez comme suit :
   
   a. Dans la zone **URL de proxy Web**, entrez l’URL dans ce format : `http://host-IP address or FDQN:Port number`. Les URL HTTPS ne sont pas prises en charge.

   b. Sous **Authentification**, sélectionnez **Aucune** ou **NTLM**.

   c. Si vous utilisez une authentification, entrez un nom d’utilisateur et un mot de passe.

   d. Cliquez sur **Appliquer les paramètres** pour valider et appliquer les paramètres du proxy web que vous avez configurés.

1. (Facultatif) Dans le volet de gauche, sélectionnez **Paramètres de l’heure**, puis configurez les paramètres de l’heure de votre appareil, notamment le fuseau horaire et les serveurs NTP principal et secondaire.  
    Les serveurs NTP sont requis. En effet, votre appareil doit synchroniser les heures pour pouvoir s’authentifier auprès de vos fournisseurs de services cloud.
    
    ![Page Paramètres de l’heure](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    Dans la page **Paramètres de l’heure**, procédez comme suit :
    
    a. Dans la liste déroulante **Fuseau horaire**, sélectionnez le fuseau horaire qui correspond à l’emplacement géographique de l’appareil déployé.  
        Le fuseau horaire par défaut de votre appareil est PST. Votre appareil utilise ce fuseau horaire pour toutes les opérations planifiées.

    b. Dans la zone **Serveur NTP principal**, entrez le serveur principal de votre appareil ou acceptez la valeur par défaut time.windows.com.  
        Vérifiez que votre réseau autorise le trafic NTP à passer de votre centre de données à Internet.

    c. Si vous le souhaitez, dans la zone **Serveur NTP secondaire**, entrez un serveur secondaire pour votre appareil.

    d. Cliquez sur **Appliquer** pour valider et appliquer les paramètres d’heure que vous avez configurés.

6. Dans le volet de gauche, sélectionnez **Paramètres cloud**, puis activez votre appareil avec le service Data Box Edge sur le portail Azure.
    
    a. Dans la zone **Clé d’activation**, entrez la clé d’activation que vous avez obtenue dans [Obtenir la clé d’activation](data-box-edge-deploy-prep.md#get-the-activation-key) pour Data Box Edge.

    b. Sélectionnez **Appliquer**. 
       
    ![Page Paramètres cloud](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    Après l’activation réussie de l’appareil, des options de mode de connectivité vous sont proposées. Ces paramètres sont configurés si vous avez besoin de travailler avec l’appareil en mode déconnecté ou partiellement déconnecté. 

    ![Confirmation de l’activation Paramètres cloud](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

La configuration de l’appareil est terminée. Vous pouvez maintenant ajouter des partages sur votre appareil.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Se connecter à un appareil physique
> * Configurer et activer l’appareil physique


Pour découvrir comment transférer des données avec votre appareil Data Box Edge, consultez :

> [!div class="nextstepaction"]
> [Transférer des données avec Data Box Edge](./data-box-edge-deploy-add-shares.md).
