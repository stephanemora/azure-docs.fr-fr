---
title: Tutoriel pour préparer le portail Azure et l’environnement du centre de données au déploiement d’Azure Stack Edge Pro R
description: Le premier tutoriel sur le déploiement d’Azure Stack Edge Pro R implique la préparation du portail Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: 5e220759a46ad9098f81a9534fa64145adade2b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104613122"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-r"></a>Tutoriel : Préparer le déploiement d’Azure Stack Edge Pro R

Voici le premier tutoriel de la série sur le déploiement complet d’Azure Stack Edge Pro R. Ce tutoriel explique comment préparer le portail Azure pour déployer une ressource Azure Stack Edge. Ce tutoriel utilise un appareil Azure Stack Edge Pro R à 1 nœud fourni avec un onduleur.

Vous avez besoin de privilèges d’administrateur pour terminer le processus d’installation et de configuration. La préparation du portail prend moins de 10 minutes.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> * Créer une nouvelle ressource
> * Obtenir la clé d'activation

### <a name="get-started"></a>Bien démarrer

Pour déployer Azure Stack Edge Pro R, consultez les tutoriels suivants dans l’ordre indiqué.

| Pour effectuer cette étape | Utilisez ces documents |
| --- | --- |
| **Préparation** |Ces étapes doivent être effectuées en préparation du déploiement à venir. |
| **[Liste de contrôle de la configuration du déploiement](#deployment-configuration-checklist)** |Utilisez cette liste de contrôle pour rassembler et enregistrer des informations avant et pendant le déploiement. |
| **[Conditions préalables au déploiement](#prerequisites)** |Ces prérequis attestent que l’environnement est prêt pour le déploiement. |
|  | |
|**Didacticiels de déploiement** |Ces tutoriels sont nécessaires pour déployer votre appareil Azure Stack Edge Pro R en production. |
|**[1. Préparer le portail Azure pour l’appareil](azure-stack-edge-pro-r-deploy-prep.md)** |Créez et configurez votre ressource Azure Stack Edge avant d’installer un appareil physique Azure Stack Edge. |
|**[2. Installer l’appareil](azure-stack-edge-pro-r-deploy-install.md)**|Inspectez, puis branchez votre appareil physique.  |
|**[3. Se connecter à l’appareil](azure-stack-edge-pro-r-deploy-connect.md)** |Une fois l’appareil installé, connectez-vous à l’interface utilisateur web locale de l’appareil.  |
|**[4. Configurer les paramètres réseau](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)** |Configurez le réseau, y compris les paramètres de réseau de calcul et de proxy web pour votre appareil.   |
|**[5. Configurer les paramètres de l’appareil](azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)** |Attribuez un nom d’appareil et un domaine DNS, configurez le serveur de mise à jour et l’heure de l’appareil. |
|**[6. Configurer les paramètres de sécurité](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)** |Configurez les certificats, le VPN, le chiffrement au repos pour votre appareil. Utilisez des certificats générés par l’appareil ou apportez vos propres certificats.   |
|**[7. Activer l’appareil](azure-stack-edge-pro-r-deploy-activate.md)** |Utilisez la clé d’activation du service pour activer l’appareil. L’appareil est prêt à configurer des partages SMB ou NFS ou à se connecter via REST. |
|**[8. Configurer le calcul](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configurez le rôle de calcul sur votre appareil. Un cluster Kubernetes est également créé. |

Vous pouvez maintenant commencer à configurer le portail Azure.

## <a name="deployment-configuration-checklist"></a>Liste de contrôle de la configuration du déploiement

Avant de déployer votre appareil, vous devez collecter des informations pour configurer le logiciel sur votre appareil Azure Stack Edge Pro. L’étude préalable d’une partie de ces informations simplifie le déploiement de l’appareil Azure Stack Edge dans votre environnement. Utilisez la [check-list de la configuration du déploiement d’Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-checklist.md) pour noter les détails de configuration lors du déploiement de votre appareil.

## <a name="prerequisites"></a>Prérequis

Voici les prérequis relatifs à la configuration de votre ressource Azure Stack Edge, de votre appareil Azure Stack Edge et du réseau du centre de données.

### <a name="for-the-azure-stack-edge-resource"></a>Pour la ressource Azure Stack Edge

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Pour votre appareil Azure Stack Edge

Avant de déployer un appareil physique, assurez-vous que :

- Vous avez consulté les informations de sécurité de cet appareil sur : [Consignes de sécurité pour votre appareil Azure Stack Edge](azure-stack-edge-pro-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 



### <a name="for-the-datacenter-network"></a>Pour le réseau du centre de données

Avant de commencer, assurez-vous que :

- Le réseau de votre centre de données est configuré conformément à la configuration réseau nécessaire pour votre appareil Azure Stack Edge. Pour plus d’informations, consultez [Configuration système nécessaire pour Azure Stack Edge Pro R](azure-stack-edge-pro-r-system-requirements.md).

- Pour utiliser votre appareil dans des conditions normales, vous avez :

    - Une bande passante de chargement de minimum 10 Mbits/s pour s’assurer que l’appareil reste à jour.
    - Une bande passante de chargement et de téléchargement de minimum 20 Mbits/s dédiée pour transférer des fichiers.

## <a name="create-a-new-resource"></a>Créer une nouvelle ressource

Si vous disposez d’une ressource Azure Stack Edge existante pour gérer votre appareil physique, ignorez cette étape et passez à la section [Obtenir la clé d’activation](#get-the-activation-key).

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour créer une ressource Azure Stack Edge, suivez ces étapes dans le portail Azure.

1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter au portail Azure en suivant cette URL : [https://portal.azure.com](https://portal.azure.com).

2. Dans le volet de gauche, sélectionnez **+ Créer une ressource**. Recherchez et sélectionnez **Azure Stack Edge / Data Box Gateway**. Sélectionnez **Create** (Créer). 

3. Sélectionnez l'abonnement que vous souhaitez utiliser pour l'appareil Azure Stack Edge Pro. Sélectionnez le pays où vous souhaitez expédier cet appareil physique. Sélectionnez **Afficher les appareils**.

    ![Créer une ressource 1](media/azure-stack-edge-pro-r-deploy-prep/create-resource-1.png)

4. Sélectionnez le type d'appareil. Sous **Azure Stack Edge**, choisissez **Azure Stack Edge Pro R**, puis **Sélectionner**. Si vous rencontrez des problèmes ou si vous ne parvenez pas à sélectionner le type d'appareil, accédez à [Résoudre les problèmes de commandes](azure-stack-edge-troubleshoot-ordering.md).

    ![Créer une ressource 2](media/azure-stack-edge-pro-r-deploy-prep/create-resource-2.png)

5. En fonction des besoins de votre entreprise, vous pouvez sélectionner **Nœud unique Azure Stack Edge Pro R** ou **Nœud unique Azure Stack Edge Pro R avec onduleur**.  

    ![Créer une ressource 3](media/azure-stack-edge-pro-r-deploy-prep/create-resource-3.png)

6. Sous l’onglet **Bases**, entrez ou sélectionnez les **détails du projet** suivants.
    
    |Paramètre  |Valeur  |
    |---------|---------|
    |Abonnement    |L’abonnement est automatiquement renseigné en fonction de la sélection antérieure. L’abonnement est lié à votre compte de facturation. |
    |Resource group  |Sélectionnez un groupe existant ou créez-en un.<br>Obtenez plus d’informations sur les [groupes de ressources Azure](../azure-resource-manager/management/overview.md).     |

7. Entrez ou sélectionnez les **détails de l’instance** suivants.

    |Paramètre  |Valeur  |
    |---------|---------|
    |Nom   | Entrez un nom reconnaissable pour identifier la ressource.<br>Le nom doit être compris entre 2 et 50 caractères, et se composer uniquement de lettres, de chiffres et de traits d’union.<br> Le nom doit commencer et se terminer par une lettre ou un chiffre.        |
    |Région     |Pour obtenir la liste complète des régions où la ressource Azure Stack Edge est disponible, consultez [Disponibilité des produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Si vous utilisez Azure Government, toutes les régions administratives sont disponibles, comme indiqué dans [Régions Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Choisissez l’emplacement le plus proche de la région géographique dans laquelle vous souhaitez déployer votre appareil.|

    ![Créer une ressource 4](media/azure-stack-edge-pro-r-deploy-prep/create-resource-4.png)


8. Sélectionnez **Suivant : Adresse de livraison**.

    - Si vous avez déjà un appareil, sélectionnez **J’ai un appareil Azure Stack Edge Pro R** dans la zone de liste modifiable.

        ![Créer une ressource 5](media/azure-stack-edge-pro-r-deploy-prep/create-resource-5.png)

    - Si vous commandez un nouvel appareil, entrez le nom du contact, la société, l’adresse de livraison de l’appareil et les coordonnées.

        ![Créer une ressource 6](media/azure-stack-edge-pro-r-deploy-prep/create-resource-6.png)

9. Sélectionnez **Suivant : Balises**. Si vous le souhaitez, vous pouvez fournir des balises pour catégoriser les ressources et centraliser la facturation. Sélectionnez **Suivant : Vérifier + créer**.

10. Sous l’onglet **Vérifier + créer**, passez en revue les **Détails de la tarification**, les **Conditions d’utilisation** et les détails de votre ressource. Cochez la case **J’ai pris connaissance des conditions de confidentialité**.

    ![Créer une ressource 7](media/azure-stack-edge-pro-r-deploy-prep/create-resource-7.png) 

    Vous êtes également informé qu’une identité MSI (Managed Service Identity) est activée pendant la création de la ressource qui vous permet de vous authentifier auprès des services cloud. Cette identité existe aussi longtemps que la ressource existe.

11. Sélectionnez **Create** (Créer).

    La création de la ressource prend quelques minutes. Une identité MSI est également créée pour permettre à l’appareil Azure Stack Edge de communiquer avec le fournisseur de ressources dans Azure.

    Un message vous informe que la ressource a été créée et déployée. Sélectionnez **Accéder à la ressource**.

    ![Accéder à la ressource Azure Stack Edge Pro](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-1.png)

Une fois la commande passée, Microsoft l’examine et vous communique (par e-mail) les détails de l’expédition.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png) - If this is restored, it must go above "After the resource is successfully created." The azure-stack-edge-resource-1.png would seem superfluous in that case.--> 

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
      --device-name myasegpu1 --location eastus --sku EdgePR_Base
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

Une fois que la ressource Azure Stack Edge est active et en cours d’exécution, vous devez obtenir la clé d’activation. Cette clé sert à activer votre appareil Azure Stack Edge Pro et à le connecter à la ressource. Vous pouvez obtenir cette clé maintenant, lorsque vous vous trouvez dans le Portail Azure.

1. Sélectionnez la ressource que vous avez créée, puis **Vue d’ensemble**.

2. Dans le volet droit, fournissez un nom pour le coffre de clés Azure ou acceptez le nom par défaut. Le nom du coffre de clés peut contenir entre 3 et 24 caractères.

   Un coffre de clés est créé pour chaque ressource Azure Stack Edge activée avec votre appareil. Le coffre de clés vous permet de stocker des secrets et d’y accéder. Par exemple, la clé d’intégrité de canal (CIK) du service est stockée dans le coffre de clés.

   Une fois que vous avez spécifié un nom de coffre de clés, sélectionnez **Générer la clé d’activation** pour créer une clé d’activation.

   ![Obtenir une clé d’activation](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-3.png)

   Attendez quelques minutes le temps que le coffre de clés et la clé d’activation soient créés. Sélectionnez l’icône de copie pour copier la clé et l’enregistrer pour une utilisation ultérieure.<!--Verify that the new screen has a copy icon.-->

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
> [Installer Azure Stack Edge](./azure-stack-edge-pro-r-deploy-install.md)
