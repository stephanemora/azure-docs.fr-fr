---
title: Se connecter à Azure Data Box Gateway, le configurer et l’activer dans le Portail Azure | Microsoft Docs
description: Ce troisième didacticiel sur le déploiement de Data Box Gateway vous apprend à connecter, configurer et activer votre appareil virtuel.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 02780a9e9702a41f677595d74b9861ea6a51b572
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260432"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>Tutoriel : Se connecter à Azure Data Box Gateway, le configurer et l’activer (préversion) 

## <a name="introduction"></a>Introduction

Ce didacticiel explique comment vous connecter à votre appareil Data Box Gateway, le configurer et l’activer à l’aide de l’interface utilisateur web locale. 

Le processus de configuration et d’installation peut durer 10 minutes environ. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * vous connecter à l’appareil virtuel ;
> * configurer et activer l’appareil virtuel.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.


> [!IMPORTANT]
> - Data Box Gateway est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de commander et déployer cette solution. 


## <a name="prerequisites"></a>Prérequis

Avant de configurer votre appareil Data Box Gateway, assurez-vous que :

* Vous avez approvisionné un appareil virtuel et obtenu une URL connectée à celui-ci comme indiqué dans [Tutorial: Provision Azure Data Box Gateway in Hyper-V (Preview)](data-box-gateway-deploy-provision-hyperv.md) (Didacticiel : Approvisionnement d’Azure Data Box Gateway dans Hyper-V (préversion)) ou [Tutorial: Provision Azure Data Box Gateway in VMware (Preview)](data-box-gateway-deploy-provision-vmware.md) (Didacticiel : Approvisionnement d’Azure Data Box Gateway dans VMware (préversion)).
* Vous disposez de la clé d’activation du service de Data Box Gateway que vous avez créée pour gérer les appareils Data Box Gateway. Pour plus d’informations, consultez [Tutorial: Prepare to deploy Azure Data Box Gateway (Preview)](data-box-gateway-deploy-prep.md) (Didacticiel : Préparation du déploiement d’Azure Data Box Gateway (préversion)).

<!--* If this is the second or subsequent virtual device that you are registering with an existing StorSimple Device Manager service, you should have the service data encryption key. This key was generated when the first device was successfully registered with this service. If you have lost this key, see [Get the service data encryption key](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) for your Data Box Gateway.-->

## <a name="connect-to-the-local-web-ui-setup"></a>Se connecter à la configuration de l’interface utilisateur web locale 

1. Ouvrez une fenêtre de navigateur et connectez-vous à l’interface utilisateur web locale. Tapez :
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Utilisez l’URL de connexion notée lors du didacticiel précédent. Une erreur vous indique qu’il existe un problème avec le certificat de sécurité du site web. Cliquez sur **Poursuivre sur cette page web**. (Ces étapes peuvent varier en fonction du navigateur utilisé.)
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. Connectez-vous à l’interface utilisateur web de votre appareil virtuel. Le mot de passe par défaut est *Password1*. 
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. Vous êtes invité à modifier le mot de passe d’administrateur de l’appareil. Saisissez un nouveau mot de passe contenant entre 8 et 16 caractères. Il doit contenir 3 des types de caractères suivants : un caractère en majuscules, un caractère en minuscules, un chiffre et un caractère spécial.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Vous vous trouvez maintenant au niveau du **tableau de bord** de votre appareil.

## <a name="set-up-and-activate-the-virtual-device"></a>Configurer et activer l’appareil virtuel
 
1. Depuis le tableau de bord, vous pouvez accéder aux différents paramètres requis pour configurer et inscrire l’appareil virtuel auprès du service Data Box Gateway. Les **paramètres réseau**, les **paramètres de proxy web** et les **paramètres de l’heure** sont facultatifs. Les seuls paramètres requis sont **Nom de l’appareil** et **Paramètres du cloud**.
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. Sur la page **Nom de l’appareil**, configurez un nom convivial pour votre appareil. Ce nom peut contenir 1 à 15 caractères, ainsi que des lettres, des chiffres et des traits d’union.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. (Facultatif) Configurez vos **Paramètres réseau**. Vous devez voir une interface réseau minimum en fonction du nombre que vous avez configuré dans la machine virtuelle sous-jacente. La page **Paramètres réseau** d’un appareil virtuel avec une interface réseau activée est présentée ci-dessous.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Lorsque vous configurez les paramètres réseau, gardez les points suivants à l’esprit :

    - Si le protocole DHCP est activé dans votre environnement, les interfaces réseau sont configurées automatiquement. Par conséquent, une adresse IP, un sous-réseau, une passerelle et un DNS sont automatiquement attribués.
    - Si le protocole DHCP n’est pas activé, vous pouvez attribuer des adresses IP statiques si nécessaire.
    - Vous pouvez configurer votre interface réseau sur IPv4.

    >[!NOTE] 
    > Nous vous recommandons de ne pas replacer Statique par DHCP pour l'adresse IP locale de l'interface réseau, sauf si vous disposez d'une autre adresse IP pour vous connecter à l'appareil. Si vous utilisez une seule interface réseau et que vous passez à DHCP, il n’y a aucun moyen de déterminer l’adresse DHCP. Si vous voulez passer à une adresse DHCP, attendez que l’appareil se soit inscrit auprès du service, puis faites la modification. Vous pouvez ensuite voir les adresses IP de tous les adaptateurs dans les **propriétés de l’appareil** dans le portail Azure pour votre service.

4. (Facultatif) Configurez votre serveur proxy web. Bien que la configuration du proxy web soit facultative, si vous en utilisez un, vous pouvez uniquement le configurer ici.
   
   ![](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Sur la page **Proxy Web** :
   
   1. Indiquez l'**URL du proxy web** au format suivant : *http://&lt;adresse IP ou nom de domaine complet de l'hôte&gt;:numéro de port*. Notez que les URL HTTPS ne sont pas prises en charge.
   2. Définissez **Authentification** sur la valeur **De base** ou **Aucune**.
   3. Si vous utilisez une authentification, vous devrez également fournir un **nom d’utilisateur** et un **mot de passe**.
   4. Cliquez sur **Appliquer**. Cette opération validera et appliquera les paramètres de proxy web configurés.

5. (Facultatif) Configurez les paramètres d'heure de votre appareil, notamment le fuseau horaire et les serveurs NTP principal et secondaire. Les serveurs NTP sont requis. En effet, votre appareil doit synchroniser les heures pour pouvoir s’authentifier auprès de vos fournisseurs de services cloud.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Sur la page **Paramètres d’heure** :
    
    1. Dans la liste déroulante, définissez le **fuseau horaire** en fonction de l’emplacement géographique de l’appareil déployé. Le fuseau horaire par défaut de votre appareil est PST. Votre appareil utilise ce fuseau horaire pour toutes les opérations planifiées.
    2. Spécifiez un **serveur NTP principal** pour votre appareil ou acceptez la valeur par défaut time.windows.com. Vérifiez que votre réseau autorise le trafic NTP à passer de votre centre de données à Internet.
    3. (Facultatif) Spécifiez un **serveur NTP secondaire** pour votre appareil.
    4. Cliquez sur **Apply**. Cette opération validera et appliquera les paramètres d’heure configurés.

6. Sur la page **Paramètres du cloud**, activez votre appareil avec le service Data Box Gateway dans le Portail Azure.
    
    1. Entrez la **clé d’activation** que vous avez obtenue dans [Get the activation key](data-box-gateway-deploy-prep.md#get-the-activation-key) (Obtenir la clé d’activation) pour Data Box Gateway.

    2. Cliquez sur **Activer**. 
       
         ![](./media/data-box-gateway-deploy-connect-setup-activate/image10.png)
    
    3. Vous devrez peut-être attendre une minute avant que l’appareil ne soit activé. Suite à l’activation, la page est actualisée pour indiquer que l’appareil est activé.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez approfondi vos connaissances sur Data Box Gateway et avez notamment appris à :

> [!div class="checklist"]
> * vous connecter à l’appareil virtuel ;
> * configurer et activer l’appareil virtuel.


Passez au didacticiel suivant pour découvrir comment transférer des données avec Data Box Gateway.

> [!div class="nextstepaction"]
> [Transférer des données avec Data Box Gateway](./data-box-gateway-deploy-add-shares.md).
