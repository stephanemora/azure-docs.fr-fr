---
title: Se connecter à Azure Data Box Edge, le configurer et l’activer dans le Portail Azure | Microsoft Docs
description: Ce troisième tutoriel sur le déploiement de Data Box Edge vous apprend à connecter, configurer et activer votre appareil physique.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3d6742bec54e612b5dca6d9ef6c4f67a33929448
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166574"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-edge-preview"></a>Tutoriel : Se connecter à Azure Data Box Edge, le configurer et l’activer (préversion) 

Ce tutoriel explique comment vous connecter à votre appareil Data Box Edge, comment le configurer et l’activer avec l’interface utilisateur web locale. 

Le processus de configuration et d’activation peut prendre 20 minutes environ. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Se connecter à l’appareil physique
> * Configurer et activer l’appareil physique

> [!IMPORTANT]
> Data Box Edge est en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de commander et déployer cette solution. 


## <a name="prerequisites"></a>Prérequis

Avant de configurer et d’activer votre appareil Data Box Edge, assurez-vous que :

* Vous avez installé l’appareil physique comme indiqué dans [Installer Data Box Edge](data-box-edge-deploy-install.md).
* Vous disposez de la clé d’activation, du service Data Box Edge que vous avez créé pour gérer l’appareil Data Box Edge. Pour plus d’informations, consultez [Préparer le déploiement d’Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Se connecter à la configuration de l’interface utilisateur web locale 

1. Configurez la carte Ethernet sur l’ordinateur que vous utilisez pour vous connecter à l’appareil Edge avec l’adresse IP statique 192.168.100.5 et le sous-réseau 255.255.255.0.
2. Connectez l’ordinateur au PORT 1 de votre appareil. 
3. Ouvrez une fenêtre de navigateur et accédez à l’interface utilisateur web locale de l’appareil à l’adresse https://192.168.100.10. Cette action peut prendre quelques minutes après la mise sous tension de l’appareil. 
4. Une erreur ou un avertissement vous indique qu’il existe un problème avec le certificat de sécurité du site web. Cliquez sur **Poursuivre sur cette page web**. (Ces étapes peuvent varier en fonction du navigateur utilisé.)
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

2. Connectez-vous à l’interface utilisateur web de votre appareil physique. Le mot de passe par défaut est *Password1*. 
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

3. Vous êtes invité à modifier le mot de passe d’administrateur de l’appareil. Saisissez un nouveau mot de passe contenant entre 8 et 16 caractères. Il doit contenir 3 types parmi les types de caractères suivants : majuscule, minuscule, chiffre et caractère spécial.

Vous vous trouvez maintenant au niveau du **tableau de bord** de votre appareil.

## <a name="set-up-and-activate-the-physical-device"></a>Configurer et activer l’appareil physique
 
1. Depuis le tableau de bord, vous pouvez accéder aux différents paramètres nécessaires à la configuration et à l’inscription de l’appareil physique auprès du service Data Box Edge. Les options **Nom de l’appareil**, **Paramètres réseau**, **Paramètres de proxy web** et **Paramètres de l’heure** sont facultatives. Les seuls paramètres obligatoires sont les **Paramètres cloud**.
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

2. Sur la page **Nom de l’appareil**, configurez un nom convivial pour votre appareil. Ce nom peut contenir 1 à 15 caractères, ainsi que des lettres, des chiffres et des traits d’union.

    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

3. (Facultatif) Configurez vos **Paramètres réseau**. Sur votre appareil physique, vous voyez six interfaces réseau. Le PORT 1 et le PORT 2 sont des interfaces réseau de 1 Gbit/s. Le PORT 3, le PORT 4, le PORT 5 et le PORT 6 sont tous des interfaces réseau de 25 Gbit/s. Le PORT 1 est automatiquement configuré comme port réservé à la gestion, et les PORTS 2 à 6 sont tous des ports de données. La page **Paramètres réseau** comme illustré ci-dessous.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Lorsque vous configurez les paramètres réseau, gardez à l’esprit :

    - Si le protocole DHCP est activé dans votre environnement, les interfaces réseau sont configurées automatiquement. Une adresse IP, un sous-réseau, une passerelle et un DNS sont automatiquement attribués.
    - Si le protocole DHCP n’est pas activé, vous pouvez, le cas échéant, attribuer des adresses IP statiques.
    - Vous pouvez configurer votre interface réseau sur IPv4.
   
4. (Facultatif) Configurez votre serveur proxy web. Bien que la configuration du proxy web soit facultative, si vous en utilisez un, vous pouvez uniquement le configurer ici.
   
   ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Sur la page **Proxy Web** :
   
   1. Fournissez l’**URL de proxy Web** dans ce format : `http://host-IP address or FDQN:Port number`. Les URL HTTPS ne sont pas prises en charge.
   2. Définissez **Authentification** sur la valeur **De base** ou **Aucune**.
   3. Si vous utilisez une authentification, vous devez également fournir un **Nom d’utilisateur** et un **Mot de passe**.
   4. Cliquez sur **Appliquer** pour valider et appliquer les paramètres du proxy web configurés.

5. (Facultatif) Configurez les paramètres d'heure de votre appareil, notamment le fuseau horaire et les serveurs NTP principal et secondaire. Les serveurs NTP sont requis. En effet, votre appareil doit synchroniser les heures pour pouvoir s’authentifier auprès de vos fournisseurs de services cloud.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    Sur la page **Paramètres d’heure** :
    
    1. Dans la liste déroulante, définissez le **fuseau horaire** en fonction de l’emplacement géographique de l’appareil déployé. Le fuseau horaire par défaut de votre appareil est PST. Votre appareil utilise ce fuseau horaire pour toutes les opérations planifiées.
    2. Spécifiez un **serveur NTP principal** pour votre appareil ou acceptez la valeur par défaut time.windows.com. Vérifiez que votre réseau autorise le trafic NTP à passer de votre centre de données à Internet.
    3. (Facultatif) Spécifiez un **serveur NTP secondaire** pour votre appareil.
    4. Cliquez sur **Appliquer** pour valider et appliquer les paramètres d’heure configurés.

6. Dans la page **Paramètres cloud**, activez votre appareil avec le service Data Box Edge sur le portail Azure.
    
    1. Entrez la **Clé d’activation** que vous avez obtenue dans [Obtenir la clé d’activation](data-box-edge-deploy-prep.md#get-the-activation-key) pour Data Box Edge.

    2. Cliquez sur **Appliquer**. 
       
         ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    3. Après l’activation réussie de l’appareil, des options de mode de connectivité vous sont proposées. Ces paramètres sont configurés si vous avez besoin de travailler avec l’appareil en mode déconnecté ou partiellement déconnecté. 

        ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

La configuration de l’appareil est terminée. Vous pouvez maintenant ajouter des partages sur votre appareil.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez approfondi vos connaissances sur Data Box Edge et avez notamment appris à :

> [!div class="checklist"]
> * Se connecter à l’appareil physique
> * Configurer et activer l’appareil physique


Passez au tutoriel suivant pour découvrir comment transférer des données avec Data Box Edge.

> [!div class="nextstepaction"]
> [Transférer des données avec Data Box Edge](./data-box-edge-deploy-add-shares.md).