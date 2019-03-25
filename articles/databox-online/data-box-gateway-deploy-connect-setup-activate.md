---
title: Se connecter à Azure Data Box Gateway, le configurer et l’activer dans le Portail Azure | Microsoft Docs
description: Ce troisième didacticiel sur le déploiement de Data Box Gateway vous apprend à connecter, configurer et activer votre appareil virtuel.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: de2ef4908260a62acf28a270dda6ad738a1760b9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402326"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Tutoriel : Se connecter, configurer, activer la passerelle de zone de données Azure

## <a name="introduction"></a>Présentation

Ce didacticiel explique comment se connecter à, configurer et activer votre appareil de passerelle de zone de données à l’aide de l’interface utilisateur web locale. 

Le processus de configuration et d’installation peut durer 10 minutes environ. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Se connecter à un appareil virtuel
> * Configurer et activer l’appareil virtuel

## <a name="prerequisites"></a>Conditions préalables

Avant de configurer votre appareil Data Box Gateway, assurez-vous que :

* Vous avez configuré un appareil virtuel et obtenu une URL connectée à ce dernier comme indiqué dans le [configurer une passerelle de la zone de données dans Hyper-V](data-box-gateway-deploy-provision-hyperv.md) ou [configurer une passerelle de la zone de données dans VMware](data-box-gateway-deploy-provision-vmware.md).
* Vous disposez de la clé d’activation du service de Data Box Gateway que vous avez créée pour gérer les appareils Data Box Gateway. Pour plus d’informations, consultez [Tutorial: Prepare to deploy Azure Data Box Gateway (Preview)](data-box-gateway-deploy-prep.md) (Didacticiel : Préparation du déploiement d’Azure Data Box Gateway (préversion)).


## <a name="connect-to-the-local-web-ui-setup"></a>Se connecter à la configuration de l’interface utilisateur web locale 

1. Ouvrez une fenêtre de navigateur et l’accès de l’appareil à une interface utilisateur web de l’ordinateur local :
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Utilisez l’URL de connexion notée lors du didacticiel précédent. Une erreur ou un avertissement vous indique qu’il existe un problème avec le certificat de sécurité du site web.

2. Sélectionnez **Poursuivre sur cette page web**. Ces étapes peuvent varier en fonction du navigateur que vous utilisez.
   
    ![Message erreur de certificat de sécurité du site web](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Connectez-vous à l’interface utilisateur web de votre appareil virtuel. Le mot de passe par défaut est *Password1*. 
   
    ![Connectez-vous à l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. À l’invite, modifiez le mot de passe. Le nouveau mot de passe doit contenir entre 8 et 16 caractères. Il doit contenir 3 des caractères suivants : caractères majuscules, minuscules, numériques et spéciaux.

    ![Modifier le mot de passe d’un appareil](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Vous vous trouvez maintenant au niveau du **tableau de bord** de votre appareil.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurer et activer l’appareil virtuel
 
Votre tableau de bord affiche les différents paramètres qui sont requis pour configurer et inscrire l’appareil virtuel auprès du service de passerelle de zone de données. Les options **Nom de l’appareil**, **Paramètres réseau**, **Paramètres de proxy web** et **Paramètres de l’heure** sont facultatives. Les seuls paramètres obligatoires sont les **Paramètres cloud**.
   
![Local web UI « Tableau de bord » page](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. Dans le volet gauche, sélectionnez **nom de l’appareil**, puis entrez un nom convivial pour votre appareil. Le nom convivial doit comporter entre 1 et 15 caractères et ont des lettres, des chiffres et des traits d’union.

    ![Page « Nom de l’appareil » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Facultatif) Dans le volet gauche, sélectionnez **paramètres réseau** , puis configurez les paramètres. Sur votre appareil virtuel, vous verrez au moins une interface réseau et bien plus encore selon le nombre que vous avez configuré dans la machine virtuelle sous-jacente. La page **Paramètres réseau** d’un appareil virtuel avec une interface réseau activée est présentée ci-dessous.
    
    ![Page « Paramètres réseau » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Lorsque vous configurez les paramètres de réseau, gardez à l’esprit :

    - Si le protocole DHCP est activé dans votre environnement, les interfaces réseau sont configurées automatiquement. Une adresse IP, un sous-réseau, une passerelle et un DNS sont automatiquement attribués.
    - Si le protocole DHCP n’est pas activé, vous pouvez, le cas échéant, attribuer des adresses IP statiques.
    - Vous pouvez configurer votre interface réseau sur IPv4.

     >[!NOTE] 
     > Nous vous recommandons de ne pas replacer Statique par DHCP pour l'adresse IP locale de l'interface réseau, sauf si vous disposez d'une autre adresse IP pour vous connecter à l'appareil. Si vous utilisez une seule interface réseau et que vous passez à DHCP, il n’y a aucun moyen de déterminer l’adresse DHCP. Si vous voulez passer à une adresse DHCP, attendez que l’appareil se soit inscrit auprès du service, puis faites la modification. Vous pouvez ensuite voir les adresses IP de tous les adaptateurs dans les **propriétés de l’appareil** dans le portail Azure pour votre service.

3. (Facultatif) Configurez votre serveur proxy web. Bien que la configuration du proxy web est facultative, si vous utilisez un proxy web, vous pouvez le configurer uniquement sur cette page.
   
   ![Page « Paramètres de proxy Web » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Sur le **proxy Web** page, procédez comme suit :
   
   1. Dans la zone **URL de proxy Web**, entrez l’URL dans ce format : `http://&lt;host-IP address or FQDN&gt;:Port number`. Les URL HTTPS ne sont pas prises en charge.
   2. Sous **Authentification**, sélectionnez **Aucune** ou **NTLM**.
   3. Si vous utilisez l’authentification, entrez un **nom d’utilisateur** et **mot de passe**.
   4. Pour valider et d’appliquer les paramètres de proxy web configurés, sélectionnez **appliquer**.

4. (Facultatif) Dans le volet de gauche, sélectionnez **Paramètres de l’heure**, puis configurez les paramètres de l’heure de votre appareil, notamment le fuseau horaire et les serveurs NTP principal et secondaire. 

    Les serveurs NTP sont requis. En effet, votre appareil doit synchroniser les heures pour pouvoir s’authentifier auprès de vos fournisseurs de services cloud.
    
    ![Page « Paramètres d’heure » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Dans le **paramètres horaires** page, procédez comme suit :
    
    1. Dans le **fuseau horaire** liste déroulante, sélectionnez le fuseau horaire qui correspond à l’emplacement géographique dans lequel l’appareil est déployé.
        Le fuseau horaire par défaut de votre appareil est PST. Votre appareil utilise ce fuseau horaire pour toutes les opérations planifiées.

    2. Spécifiez un **serveur NTP principal** pour votre appareil ou acceptez la valeur par défaut `time.windows.com`.   
        Vérifiez que votre réseau autorise le trafic NTP à passer de votre centre de données à Internet.

    3. Si vous le souhaitez, dans le **serveur NTP secondaire** , entrez un serveur secondaire pour votre appareil.

    4. Cliquez sur **Appliquer** pour valider et appliquer les paramètres d’heure que vous avez configurés.

6. Dans le volet gauche, sélectionnez **paramètres du Cloud**, puis activer votre appareil auprès du service de passerelle de zone de données dans le portail Azure.
    
    1. Dans le **clé d’Activation** , entrez la **clé d’Activation** que vous avez obtenu [obtenir la clé d’activation](data-box-gateway-deploy-prep.md#get-the-activation-key) pour la passerelle de zone de données.

    2. Sélectionnez **Activer**.
       
         ![Page « Paramètres de Cloud » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. L’appareil est activé et les mises à jour critiques, s’il est disponible, sont automatiquement appliquées. Vous voyez une notification à cet effet. Surveiller la progression de la mise à jour via le portail Azure.

        ![Page « Paramètres de Cloud » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **La boîte de dialogue a également une clé de récupération que vous devez copier et enregistrer dans un emplacement sûr. Cette clé est utilisée pour récupérer vos données en cas de l’appareil ne peut pas démarrer.**


    4. Vous devrez peut-être attendre plusieurs minutes pour mener à bien la mise à jour. Après la mise à jour est terminer, connectez-vous à l’appareil. Le **paramètres du Cloud** page mises à jour pour indiquer que l’appareil est activé avec succès.

        ![Page de « Paramètres de Cloud » de l’interface utilisateur web locale mise à jour](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

La configuration de l’appareil est terminée. Vous pouvez maintenant ajouter des partages sur votre appareil.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Se connecter à un appareil virtuel
> * Configurer et activer l’appareil virtuel

Pour savoir comment transférer des données à votre passerelle de la zone de données, consultez :

> [!div class="nextstepaction"]
> [Transférer des données avec Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
