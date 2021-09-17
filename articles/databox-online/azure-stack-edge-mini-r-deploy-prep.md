---
title: Tutoriel pour préparer le portail Azure et l’environnement du centre de données au déploiement d’un appareil Azure Stack Edge Mini R | Microsoft Docs
description: Le premier tutoriel sur le déploiement d’un appareil Azure Stack Edge Mini R implique la préparation du portail Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/09/2021
ms.author: alkohli
ms.openlocfilehash: b99247bf0aefb8052d4f6f04446a5f66dff7b88a
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324440"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-mini-r"></a>Tutoriel : Préparer le déploiement d’Azure Stack Edge Mini R

Ce tutoriel est le premier d’une série de tutoriels permettant de déployer entièrement un appareil Azure Stack Edge Mini R. Il explique comment préparer le portail Azure pour déployer une ressource Azure Stack Edge.

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. La préparation du portail prend moins de 10 minutes.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une nouvelle ressource
> * Obtenir la clé d'activation

### <a name="get-started"></a>Bien démarrer

Pour déployer Azure Stack Edge Mini R, consultez les tutoriels suivants dans l’ordre indiqué.

| Étape | Description |
| --- | --- |
| **Préparation** |Ces étapes doivent être effectuées en préparation du déploiement à venir. |
| **[Liste de contrôle de la configuration du déploiement](#deployment-configuration-checklist)** |Utilisez cette liste de contrôle pour rassembler et enregistrer des informations avant et pendant le déploiement. |
| **[Conditions préalables au déploiement](#prerequisites)** |Ces prérequis attestent que l’environnement est prêt pour le déploiement. |
|  | |
|**Didacticiels de déploiement** |Ces tutoriels sont nécessaires pour déployer votre appareil Azure Stack Edge Mini R en production. |
|**[1. Préparer le portail Azure pour l’appareil](azure-stack-edge-mini-r-deploy-prep.md)** |Créez et configurez votre ressource Azure Stack Edge avant d’installer l’appareil physique. |
|**[2. Installer l’appareil](azure-stack-edge-mini-r-deploy-install.md)**|Inspectez, puis branchez votre appareil physique.  |
|**[3. Se connecter à l’appareil](azure-stack-edge-mini-r-deploy-connect.md)** |Une fois l’appareil installé, connectez-vous à l’interface utilisateur web locale de l’appareil.  |
|**[4. Configurer les paramètres réseau](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)** |Configurez le réseau, y compris les paramètres de réseau de calcul et de proxy web pour votre appareil.   |
|**[5. Configurer les paramètres de l’appareil](azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)** |Attribuez un nom d’appareil et un domaine DNS, configurez le serveur de mise à jour et l’heure de l’appareil. |
|**[6. Configurer les paramètres de sécurité](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)** |Configurez des certificats en utilisant les vôtres, configurez un VPN et configurez un chiffrement au repos pour votre appareil.   |
|**[7. Activer l’appareil](azure-stack-edge-mini-r-deploy-activate.md)** |Utilisez la clé d’activation du service pour activer l’appareil. L’appareil est prêt à configurer des partages SMB ou NFS ou à se connecter via REST. |
|**[8. Configurer le calcul](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configurez le rôle de calcul sur votre appareil. Un cluster Kubernetes est également créé. |

Vous pouvez maintenant commencer à configurer le portail Azure.

## <a name="deployment-configuration-checklist"></a>Liste de contrôle de la configuration du déploiement

Avant de déployer votre appareil, vous devez collecter des informations pour configurer le logiciel sur votre appareil Azure Stack Edge Mini R. L’étude préalable d’une partie de ces informations simplifie le déploiement de l’appareil Azure Stack Edge dans votre environnement. Utilisez la [check-list de la configuration du déploiement d’Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-checklist.md) pour noter les détails de configuration lors du déploiement de votre appareil.

## <a name="prerequisites"></a>Prérequis

Voici les prérequis relatifs à la configuration de votre ressource Azure Stack Edge, de votre appareil Azure Stack Edge et du réseau du centre de données.

### <a name="for-the-azure-stack-edge-resource"></a>Pour la ressource Azure Stack Edge

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Pour votre appareil Azure Stack Edge

Avant de déployer un appareil physique, assurez-vous que :

- Vous avez consulté les informations de sécurité données pour cet appareil dans la page [Consignes de sécurité pour votre appareil Azure Stack Edge](azure-stack-edge-mini-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>Pour le réseau du centre de données

Avant de commencer, assurez-vous que :

- Le réseau de votre centre de données est configuré conformément à la configuration réseau nécessaire pour votre appareil Azure Stack Edge. Pour plus d’informations, consultez [Configuration système nécessaire pour Azure Stack Edge Mini R](azure-stack-edge-mini-r-system-requirements.md).

- Pour utiliser votre appareil Azure Stack Edge dans des conditions normales, vous avez :

    - Une bande passante de chargement de minimum 10 Mbits/s pour s’assurer que l’appareil reste à jour.
    - Une bande passante de chargement et de téléchargement de minimum 20 Mbits/s dédiée pour transférer des fichiers.

## <a name="create-a-new-resource"></a>Créer une nouvelle ressource

Si vous disposez d’une ressource Azure Stack Edge existante pour gérer votre appareil physique, ignorez cette étape et passez à la section [Obtenir la clé d’activation](#get-the-activation-key).

---

### <a name="azure-edge-hardware-center-preview"></a>[Azure Edge Hardware Center (préversion)](#tab/azure-edge-hardware-center)

Azure Edge Hardware Center (préversion) est un nouveau service qui vous permet d’explorer et de commander une variété de matériel à partir du portefeuille hybride Azure, notamment les appareils Azure Stack Edge Pro.

Lorsque vous passez une commande par le biais d’Azure Edge Hardware Center, vous pouvez commander plusieurs appareils, à envoyer à plusieurs adresses et vous pouvez réutiliser les adresses d’expédition à partir d’autres commandes.

Toute commande par le biais d’Azure Edge Hardware Center crée une ressource Azure qui contient toutes les informations relatives à votre commande. Une seule ressource est créée pour chaque unité commandée. Vous devrez créer une ressource Azure Stack Edge après avoir reçu l’appareil pour l’activer et le gérer.

[!INCLUDE [Create order in Azure Edge Hardware Center](../../includes/azure-edge-hardware-center-new-order.md)]

#### <a name="create-a-management-resource-for-each-device"></a>Créer une ressource de gestion pour chaque appareil

Pour gérer les appareils qui sont commandés via Azure Edge Hardware Center, vous allez créer une ressource de gestion pour chaque appareil dans Azure Stack Edge. Lorsqu’un appareil est activé, la ressource de gestion est associée à un élément de la commande. Vous pourrez ouvrir l’élément de la commande à partir de la ressource de gestion et la ressource de gestion à partir de l’élément de la commande. 

Une fois l’appareil expédié, un lien **Configurer le matériel** est ajouté aux informations sur l’élément de la commande, ce qui vous permet d’ouvrir directement un Assistant pour créer une ressource de gestion. Vous pouvez également utiliser l’option **Créer une ressource de gestion** dans Azure Stack Edge.

[!INCLUDE [Create management resource](../../includes/azure-edge-hardware-center-create-management-resource.md)]

### <a name="portal-classic"></a>[Portail (classique)](#tab/azure-portal)

Pour créer une ressource Azure Stack Edge, suivez ces étapes dans le portail Azure.

1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter au portail Azure en suivant cette URL : [https://portal.azure.com](https://portal.azure.com).

2. Dans **Services Azure**, recherchez et sélectionnez **Azure Stack Edge**. Sélectionnez ensuite **+ Créer**. 

3. Dans **Gérer les appareils Azure Stack Edge**, sélectionnez le lien **Essayer Azure Hardware Center**.

    ![Capture de l’écran « Gérer les appareils Azure Stack Edge », ouvert avec le bouton Plus Créer. Le lien « Essayer Azure Edge Hardware Center » est mis en surbrillance.](media/azure-stack-edge-mini-r-deploy-prep/classic-order-experience-1.png)

    L’écran **Démarrage** s’ouvre pour la création d’une commande dans Azure Edge Hardware Center. 

4. Si vous ne souhaitez pas commander via Hardware Center, sur l’écran **Démarrage**, sélectionnez **Commander via l’expérience de classement classique**.

   ![Capture de l’écran Démarrage dans Azure Stack Edge. Le lien « Commander via l’expérience de commande classique » est mis en surbrillance.](media/azure-stack-edge-mini-r-deploy-prep/classic-order-experience-2.png)

5. Sélectionnez l'abonnement que vous souhaitez utiliser pour l'appareil Azure Stack Edge Pro. Sélectionnez le pays où vous souhaitez expédier cet appareil physique. Sélectionnez **Afficher les appareils**.

    ![Capture de l’écran « Sélectionner le type d’appareil » pour sélectionner un abonnement et une région d’expédition pour une ressource Azure Stack Edge. Le bouton Afficher les appareils est mis en surbrillance.](media/azure-stack-edge-mini-r-deploy-prep/create-resource-1.png)


6. Sélectionnez le type d'appareil. Sous **Azure Stack Edge**, choisissez **Azure Stack Edge Mini R**, puis **Sélectionner**. Si vous rencontrez des problèmes ou si vous ne parvenez pas à sélectionner le type d'appareil, accédez à [Résoudre les problèmes de commandes](azure-stack-edge-troubleshoot-ordering.md).

    [![Capture d’écran de l’écran « Sélectionner le type d’appareil » pour sélectionner un type d’appareil pour une ressource Azure Stack Edge. Le bouton Sélectionner d’un type d’appareil est mis en surbrillance.](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)


7. Sous l’onglet **Bases**, entrez ou sélectionnez les **détails du projet** suivants.
    
    |Paramètre  |Valeur  |
    |---------|---------|
    |Abonnement    |L’abonnement est automatiquement renseigné en fonction de la sélection antérieure. L’abonnement est lié à votre compte de facturation. |
    |Resource group  |Sélectionnez un groupe existant ou créez-en un.<br>Obtenez plus d’informations sur les [groupes de ressources Azure](../azure-resource-manager/management/overview.md).     |


8. Entrez ou sélectionnez les **détails de l’instance** suivants.

    |Paramètre  |Valeur  |
    |---------|---------|
    |Nom   | Entrez un nom reconnaissable pour identifier la ressource.<br>Le nom peut comporter entre 2 et 50 caractères, dont des lettres, des chiffres et des traits d’union.<br> Le nom doit commencer et se terminer par une lettre ou un chiffre.        |
    |Région     |Pour obtenir la liste complète des régions où la ressource Azure Stack Edge est disponible, consultez [Disponibilité des produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Si vous utilisez Azure Government, toutes les régions administratives sont disponibles, comme indiqué dans [Régions Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Choisissez l’emplacement le plus proche de la région géographique dans laquelle vous souhaitez déployer votre appareil.|

    ![Capture d’écran de l’onglet Informations de base de l’Assistant Créer une ressource et commander un appareil pour Azure Stack Edge. L’onglet Informations de base et le bouton Suivant : adresse d’expédition sont mis en surbrillance.](media/azure-stack-edge-mini-r-deploy-prep/create-resource-4.png)


9. Sélectionnez **Suivant : Adresse de livraison**.

   - Si vous disposez déjà d’un appareil, cochez la case **J’ai déjà un appareil**.

     ![Capture d’écran de l’onglet « Adresse d’expédition » avec l’option « J’ai déjà une adresse d’expédition pour l’appareil » sélectionnée dans l’Assistant Créer une ressource pour Azure Stack Edge.](media/azure-stack-edge-mini-r-deploy-prep/create-resource-5.png)

   - Si vous commandez un nouvel appareil, entrez le nom du contact, la société, l’adresse de livraison de l’appareil et les informations de contact.

     ![Capture d’écran de l’onglet « Adresse d’expédition » dans l’Assistant Créer une ressource lors de la création d’une nouvelle ressource Azure Stack Edge.](media/azure-stack-edge-mini-r-deploy-prep/create-resource-6.png)

10. Sélectionnez **Suivant : Balises**. Si vous le souhaitez, vous pouvez fournir des balises pour catégoriser les ressources et centraliser la facturation. Sélectionnez **Suivant : Vérifier + créer**.

11. Sous l’onglet **Vérifier + créer**, passez en revue les **Détails de la tarification**, les **Conditions d’utilisation** et les détails de votre ressource. Cochez la case **J’ai pris connaissance des conditions de confidentialité**.

    ![Capture d’écran de l’onglet Vérifier plus créer pour une commande Azure Stack Edge.](media/azure-stack-edge-mini-r-deploy-prep/create-resource-7.png)

    Vous êtes également informé qu’une identité MSI (Managed Service Identity) est activée pendant la création de la ressource qui vous permet de vous authentifier auprès des services cloud. Cette identité existe aussi longtemps que la ressource existe.

12. Sélectionnez **Create** (Créer).

    La création de la ressource prend quelques minutes. Une identité MSI est également créée pour permettre à l’appareil Azure Stack Edge de communiquer avec le fournisseur de ressources dans Azure.
    
    Un message vous informe que la ressource a été créée et déployée. Sélectionnez **Accéder à la ressource**.
    
    ![Capture d’écran du volet Vue d’ensemble pour une commande Azure Stack Edge qui a été créée avec succès.](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-1.png)
    
Une fois la commande passée, Microsoft l’examine et vous communique (par e-mail) les détails de l’expédition.

> [!NOTE]
> Si vous souhaitez créer plusieurs commandes à la fois ou cloner une commande existante, vous pouvez utiliser les [scripts dans les exemples Azure](https://github.com/Azure-Samples/azure-stack-edge-order). Pour plus d’informations, consultez le fichier README.

Si vous rencontrez des problèmes pendant le processus de commande, consultez [Résoudre les problèmes de commande](azure-stack-edge-troubleshoot-ordering.md).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Si nécessaire, préparez votre environnement pour Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Pour créer une ressource Azure Stack Edge, exécutez les commandes suivantes dans Azure CLI.

1. Créez un groupe de ressources à l’aide de la commande [az group create](/cli/azure/group#az_group_create) ou utilisez un groupe de ressources existant :

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Pour créer un appareil, utilisez la commande [az databoxedge device create](/cli/azure/databoxedge/device#az_databoxedge_device_create) :

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgeMR_Mini
   ```

   Choisissez l’emplacement le plus proche de la région géographique dans laquelle vous souhaitez déployer votre appareil. La région stocke les métadonnées uniquement pour la gestion des appareils. Les données réelles peuvent être stockées dans n’importe quel compte de stockage.

   Pour obtenir la liste complète des régions où la ressource Azure Stack Edge est disponible, consultez [Disponibilité des produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Si vous utilisez Azure Government, toutes les régions administratives sont disponibles, comme indiqué dans [Régions Azure](https://azure.microsoft.com/global-infrastructure/regions/).

1. Pour créer une commande, exécutez la commande [az databoxedge order create](/cli/azure/databoxedge/order#az_databoxedge_order_create) :

   ```azurecli
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

La création de la ressource prend quelques minutes. Pour voir votre commande, exécutez la commande [az databoxedge order show](/cli/azure/databoxedge/order#az_databoxedge_order_show) :

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

Une fois la commande passée, Microsoft l’examine et vous communique les détails de l’expédition par e-mail.

---

## <a name="get-the-activation-key"></a>Obtenir la clé d'activation

Une fois que la ressource Azure Stack Edge est active et en cours d’exécution, vous devez obtenir la clé d’activation. Cette clé sert à activer votre appareil Azure Stack Edge Mini R et à le connecter à la ressource. Vous pouvez obtenir cette clé maintenant, lorsque vous vous trouvez dans le Portail Azure.

1. Sélectionnez la ressource que vous avez créée, puis **Vue d’ensemble**.

   ![Capture d’écran du volet Vue d’ensemble pour une ressource Azure Stack Edge.](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-2.png)

2. Sur la vignette **Activer**, attribuez un nom au coffre de clés Azure ou acceptez le nom par défaut. Le nom du coffre de clés peut contenir entre 3 et 24 caractères. 

    Un coffre de clés est créé pour chaque ressource Azure Stack Edge activée avec votre appareil. Le coffre de clés vous permet de stocker des secrets et d’y accéder. Par exemple, la clé d’intégrité de canal (CIK) pour le service est stockée dans le coffre de clés.

    Une fois que vous avez spécifié un nom de coffre de clés, sélectionnez **Générer la clé d’activation** pour créer une clé d’activation.

    [![Capture d’écran du volet Vue d’ensemble pour la ressource Azure Stack Edge nouvellement créée, avec une entrée de nom de coffre de clés. L’entrée et le bouton Générer une clé d’activation sont mis en surbrillance.](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png)](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png#lightbox)

    Attendez quelques minutes le temps que le coffre de clés et la clé d’activation soient créés. Sélectionnez l’icône de copie pour copier la clé et l’enregistrer pour une utilisation ultérieure.

> [!IMPORTANT]
> - La clé d’activation expire 3 jours après sa création.
> - Si la clé expire, générez une nouvelle clé. L’ancienne clé n'est plus valide.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez approfondi vos connaissances sur Azure Stack Edge et avez notamment appris à :

> [!div class="checklist"]
> * Créer une nouvelle ressource
> * Obtenir la clé d'activation

Passez au tutoriel suivant pour apprendre à installer Azure Stack Edge.

> [!div class="nextstepaction"]
> [Installer Azure Stack Edge](./azure-stack-edge-mini-r-deploy-install.md)
