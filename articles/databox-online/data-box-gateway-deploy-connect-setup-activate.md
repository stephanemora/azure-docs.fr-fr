---
title: Se connecter à Azure Data Box Gateway, le configurer et l’activer dans le Portail Azure | Microsoft Docs
description: Ce troisième didacticiel sur le déploiement de Data Box Gateway vous apprend à connecter, configurer et activer votre appareil virtuel.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 26b560434e6305689781b8c39c7cf814af9bf8aa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112294"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>Tutoriel : Se connecter à Azure Data Box Gateway, le configurer et l’activer (préversion) 

## <a name="introduction"></a>Présentation

Ce didacticiel explique comment vous connecter à votre appareil Data Box Gateway, le configurer et l’activer à l’aide de l’interface utilisateur web locale. 

Le processus de configuration et d’installation peut durer 10 minutes environ. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * vous connecter à l’appareil virtuel ;
> * configurer et activer l’appareil virtuel.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


> [!IMPORTANT]
> - Data Box Gateway est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de commander et déployer cette solution. 


## <a name="prerequisites"></a>Conditions préalables

Avant de configurer votre appareil Data Box Gateway, assurez-vous que :

* Vous avez approvisionné un appareil virtuel et obtenu une URL connectée à celui-ci comme indiqué dans [Tutorial: Provision Azure Data Box Gateway in Hyper-V (Preview)](data-box-gateway-deploy-provision-hyperv.md) (Didacticiel : Approvisionnement d’Azure Data Box Gateway dans Hyper-V (préversion)) ou [Tutorial: Provision Azure Data Box Gateway in VMware (Preview)](data-box-gateway-deploy-provision-vmware.md) (Didacticiel : Approvisionnement d’Azure Data Box Gateway dans VMware (préversion)).
* Vous disposez de la clé d’activation du service de Data Box Gateway que vous avez créée pour gérer les appareils Data Box Gateway. Pour plus d’informations, consultez [Tutorial: Prepare to deploy Azure Data Box Gateway (Preview)](data-box-gateway-deploy-prep.md) (Didacticiel : Préparation du déploiement d’Azure Data Box Gateway (préversion)).


## <a name="connect-to-the-local-web-ui-setup"></a>Se connecter à la configuration de l’interface utilisateur web locale 

1. Ouvrez une fenêtre de navigateur et connectez-vous à l’interface utilisateur web locale. Tapez :
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Utilisez l’URL de connexion notée lors du didacticiel précédent. Une erreur vous indique qu’il existe un problème avec le certificat de sécurité du site web. Cliquez sur **Poursuivre sur cette page web**. (Ces étapes peuvent varier en fonction du navigateur utilisé.)
   
    ![Erreur lors de la connexion](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. Connectez-vous à l’interface utilisateur web de votre appareil virtuel. Le mot de passe par défaut est *Password1*. 
   
    ![Connectez-vous à l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. Vous êtes invité à modifier le mot de passe d’administrateur de l’appareil. Saisissez un nouveau mot de passe contenant entre 8 et 16 caractères. Il doit contenir 3 des types de caractères suivants : un caractère en majuscules, un caractère en minuscules, un chiffre et un caractère spécial.

    ![Mot de passe administrateur de périphérique modification](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Vous vous trouvez maintenant au niveau du **tableau de bord** de votre appareil.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurer et activer l’appareil virtuel
 
1. Depuis le tableau de bord, vous pouvez accéder aux différents paramètres requis pour configurer et inscrire l’appareil virtuel auprès du service Data Box Gateway. Les **paramètres réseau**, les **paramètres de proxy web** et les **paramètres de l’heure** sont facultatifs. Les seuls paramètres requis sont **Nom de l’appareil** et **Paramètres du cloud**.
   
    ![Local web UI « Tableau de bord » page](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. Sur la page **Nom de l’appareil**, configurez un nom convivial pour votre appareil. Ce nom peut contenir 1 à 15 caractères, ainsi que des lettres, des chiffres et des traits d’union.

    ![Page « Nom de l’appareil » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. (Facultatif) Configurez vos **Paramètres réseau**. Vous devez voir une interface réseau minimum en fonction du nombre que vous avez configuré dans la machine virtuelle sous-jacente. La page **Paramètres réseau** d’un appareil virtuel avec une interface réseau activée est présentée ci-dessous.
    
    ![Page « Paramètres réseau » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Lorsque vous configurez les paramètres réseau, gardez les points suivants à l’esprit :

   - Si le protocole DHCP est activé dans votre environnement, les interfaces réseau sont configurées automatiquement. Par conséquent, une adresse IP, un sous-réseau, une passerelle et un DNS sont automatiquement attribués.
   - Si le protocole DHCP n’est pas activé, vous pouvez attribuer des adresses IP statiques si nécessaire.
   - Vous pouvez configurer votre interface réseau sur IPv4.

     >[!NOTE] 
     > Nous vous recommandons de ne pas replacer Statique par DHCP pour l'adresse IP locale de l'interface réseau, sauf si vous disposez d'une autre adresse IP pour vous connecter à l'appareil. Si vous utilisez une seule interface réseau et que vous passez à DHCP, il n’y a aucun moyen de déterminer l’adresse DHCP. Si vous voulez passer à une adresse DHCP, attendez que l’appareil se soit inscrit auprès du service, puis faites la modification. Vous pouvez ensuite voir les adresses IP de tous les adaptateurs dans les **propriétés de l’appareil** dans le portail Azure pour votre service.

4. (Facultatif) Configurez votre serveur proxy web. Bien que la configuration du proxy web soit facultative, si vous en utilisez un, vous pouvez uniquement le configurer ici.
   
   ![Page « Paramètres de proxy Web » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Sur la page **Proxy Web** :
   
   1. Indiquez l'**URL du proxy web** au format suivant : *http://&lt;adresse IP ou nom de domaine complet de l'hôte&gt;:numéro de port*. Notez que les URL HTTPS ne sont pas prises en charge.
   2. Définissez **Authentification** sur la valeur **De base** ou **Aucune**.
   3. Si vous utilisez une authentification, vous devrez également fournir un **nom d’utilisateur** et un **mot de passe**.
   4. Cliquez sur **Appliquer**. Cette opération validera et appliquera les paramètres de proxy web configurés.

5. (Facultatif) Configurez les paramètres d'heure de votre appareil, notamment le fuseau horaire et les serveurs NTP principal et secondaire. Les serveurs NTP sont requis. En effet, votre appareil doit synchroniser les heures pour pouvoir s’authentifier auprès de vos fournisseurs de services cloud.
    
    ![Page « Paramètres d’heure » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Sur la page **Paramètres d’heure** :
    
    1. Dans la liste déroulante, définissez le **fuseau horaire** en fonction de l’emplacement géographique de l’appareil déployé. Le fuseau horaire par défaut de votre appareil est PST. Votre appareil utilise ce fuseau horaire pour toutes les opérations planifiées.
    2. Spécifiez un **serveur NTP principal** pour votre appareil ou acceptez la valeur par défaut time.windows.com. Vérifiez que votre réseau autorise le trafic NTP à passer de votre centre de données à Internet.
    3. (Facultatif) Spécifiez un **serveur NTP secondaire** pour votre appareil.
    4. Cliquez sur **Apply**. Cette opération validera et appliquera les paramètres d’heure configurés.

6. Sur la page **Paramètres du cloud**, activez votre appareil avec le service Data Box Gateway dans le Portail Azure.
    
    1. Entrez la **clé d’activation** que vous avez obtenue dans [Get the activation key](data-box-gateway-deploy-prep.md#get-the-activation-key) (Obtenir la clé d’activation) pour Data Box Gateway.

    2. Cliquez sur **Activer**. 
       
         ![Page « Paramètres de Cloud » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Tout d’abord l’appareil sera activé. L’appareil est ensuite analysée pour les mises à jour critiques et s’il est disponible, les mises à jour sont automatiquement appliquées. Vous voyez une notification à cet effet. 

        La boîte de dialogue a également une clé de récupération que vous devez copier et enregistrez-le dans un emplacement sûr. Cette clé est utilisée pour récupérer vos données en cas de l’appareil ne peut pas démarrer.

        ![Page « Paramètres de Cloud » de l’interface utilisateur web locale](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)    

    4. Vous devrez peut-être attendre plusieurs minutes après que la mise à jour est effectuée avec succès. Les mises à jour de page pour indiquer que l’appareil est activé avec succès.

        ![Page de « Paramètres de Cloud » de l’interface utilisateur web locale mise à jour](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez approfondi vos connaissances sur Data Box Gateway et avez notamment appris à :

> [!div class="checklist"]
> * vous connecter à l’appareil virtuel ;
> * configurer et activer l’appareil virtuel.


Passez au didacticiel suivant pour découvrir comment transférer des données avec Data Box Gateway.

> [!div class="nextstepaction"]
> [Transférer des données avec Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
