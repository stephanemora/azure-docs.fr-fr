---
title: Configurer SnapCenter pour acheminer le trafic d’Azure vers des serveurs Oracle BareMetal
description: Découvrez comment configurer NetApp SnapCenter pour acheminer le trafic d’Azure vers des serveurs Oracle BareMetal Infrastructure.
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/05/2021
ms.openlocfilehash: 9d8f83712822dcf556efe1098eadd1cfdb4867e6
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576227"
---
# <a name="set-up-netapp-snapcenter-to-route-traffic-from-azure-to-oracle-baremetal-servers"></a>Configurer NetApp SnapCenter pour acheminer le trafic d’Azure vers des serveurs Oracle BareMetal

Dans cet article, nous allons voir pas à pas comment configurer NetApp SnapCenter pour acheminer le trafic d’Azure vers des serveurs Oracle BareMetal Infrastructure. 

## <a name="prerequisites"></a>Prérequis

> [!div class="checklist"]
> - **Configuration requise pour SnapCenter Server** : Azure Windows 2019 ou ultérieur avec 4 processeurs virtuels, 16 Go de RAM et un minimum de 500 Go de stockage SSD premium managé.
> - **Configuration réseau requise pour ExpressRoute :** Un utilisateur SnapCenter pour Oracle BareMetal doit contacter Microsoft Operations pour créer les comptes réseau et permettre la communication entre votre machine virtuelle de stockage personnel et la machine virtuelle SnapCenter dans Azure.
> - **Java 1.8 sur les instances de BareMetal :** Les plug-ins SnapCenter nécessitent l’installation de Java 1.8 sur les instances de BareMetal.

## <a name="steps-to-integrate-snapcenter"></a>Étapes à suivre pour intégrer SnapCenter

Voici les étapes à effectuer pour configurer NetApp SnapCenter afin d’acheminer le trafic d’Azure vers les serveurs Oracle BareMetal Infrastructure : 

1. Ouvrez une demande de ticket de support pour communiquer la clé publique générée par l’utilisateur à l’équipe Microsoft Ops. L’intervention du support est nécessaire pour configurer l’utilisateur SnapCenter afin qu’il puisse accéder au système de stockage. 

2. Créez une machine virtuelle dans votre réseau virtuel Azure ayant accès à vos instances BareMetal. Cette machine virtuelle est utilisée pour SnapCenter. 

3. Téléchargez et installez SnapCenter. 

4. Opérations de sauvegarde et de récupération 

>[!NOTE]
> Ces étapes supposent que vous avez déjà créé une connexion ExpressRoute entre votre abonnement dans Azure et votre locataire dans Oracle HaaS.

## <a name="create-a-support-ticket-for-user-role-storage-setup"></a>Créer un ticket de support pour la configuration du stockage du rôle d’utilisateur

1. Ouvrez le portail Azure et accédez à la page Abonnements. Sélectionnez votre abonnement BareMetal.
2. Dans la page de votre abonnement BareMetal, sélectionnez **Groupes de ressources**.
3. Sélectionnez un groupe de ressources approprié dans une région.
    
    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/select-resource-group.png" alt-text="Capture d’écran montrant les groupes de ressources dans la page d’abonnement.":::

4. Sélectionnez une référence SKU correspondant au stockage SAP HANA sur Azure. 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/select-sku.png" alt-text="Capture d’écran d’un groupe de ressources avec une référence SKU de type SAP HANA sur Azure mise en évidence.":::

5. Sélectionnez **Nouvelle demande de support**.

6. Sous l’onglet **De base**, fournissez les informations suivantes pour le ticket :
    - **Type de problème** : Technique
    -   **Abonnement** : Votre abonnement
    -   **Service** : BareMetal Infrastructure
    -   **Ressource** : Votre groupe de ressources
    -   **Résumé** : Configuration de l’accès à SnapCenter
    -   **Type de problème** : Configuration et installation
    -   **Sous-type de problème :** Configurer SnapCenter

7. Dans la partie  **Description** du ticket de support, sous l’onglet **Détails** , collez le contenu d’un fichier *.pem dans la zone de texte pour plus d’informations. Vous pouvez également compresser un fichier *.pem et le charger. Votre clé publique pour l’utilisateur SnapCenter est snapcenter.pem. Utilisez l’exemple suivant pour créer un fichier *.pem à partir de l’une de vos instances BareMetal. 

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/pem.png" alt-text="Capture d’écran montrant l’exemple de contenu d’un fichier .pem.":::

    >[!NOTE]
    >Le nom du fichier « snapcenter » est le nom d’utilisateur requis pour effectuer des appels d’API REST. Il est donc conseillé de donner au fichier un nom descriptif.

8.  Sélectionnez **Vérifier + créer** pour passer en revue votre ticket de support.

9.  Une fois le certificat de clé publique envoyé, Microsoft configure le nom d’utilisateur SnapCenter pour votre locataire ainsi que l’adresse IP de la machine virtuelle de stockage. Microsoft vous donne l’adresse IP de la machine virtuelle de stockage.

10. Après avoir reçu cette adresse IP, définissez un mot de passe pour accéder à la machine virtuelle de stockage, que vous contrôlez.

    Voici un exemple d’appel REST d’une grande instance HANA ou d’une machine virtuelle dans le réseau virtuel qui a accès à l’environnement de grande instance HANA et servira à définir le mot de passe.
    
    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/sample-rest-call.png" alt-text="Capture d’écran montrant un exemple d’appel REST.":::

11. Vérifiez qu’il n’y a aucune variable proxy active sur l’instance BareMetal utilisée pour créer le fichier *.pem.

     :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/ensure-no-proxy.png" alt-text="Capture d’écran montrant le proxy http non défini (unset) pour s’assurer qu’aucune variable proxy n’est active sur l’instance BareMetal lors de la création d’un fichier *.pem.":::

12. À partir de l’ordinateur client, il est désormais possible d’exécuter des commandes sans nom d’utilisateur/mot de passe pour les commandes REST activées. Testez la connexion : 

    Aucun proxy :

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/connection-test.png" alt-text="Capture d’écran montrant le test de la connexion et les commandes REST activées sans nom d’utilisateur/mot de passe.":::


       >[!NOTE]
       > Vous pouvez ajouter la commande « --verbose » à l’une des commandes curl pour fournir des détails supplémentaires sur la raison de l’échec d’une commande.

## <a name="install-snapcenter"></a>Installer SnapCenter

1. Sur la machine virtuelle Windows provisionnée, accédez au [site web de NetApp](https://mysupport.netapp.com/site/products/all/details/snapcenter/downloads-tab).

2. Connectez-vous et téléchargez SnapCenter. Download > SnapCenter > **Version 4.4**.

3. Installez SnapCenter 4.4. Sélectionnez **Suivant**.

    Le programme d’installation vérifie les prérequis de la machine virtuelle. Notez la taille de la machine virtuelle, en particulier dans les grands environnements. Vous pouvez tout à fait continuer l’installation même si un redémarrage est en attente.

4. Configurez les informations d’identification de l’utilisateur pour SnapCenter. Les informations d’identification de l’utilisateur Windows qui a lancé l’installation de l’application sont renseignées par défaut. Sauf en cas de conflit de port, nous vous recommandons d’utiliser les ports par défaut.

    L’exécution de l’Assistant Installation peut prendre du temps. Vous pouvez suivre la progression de l’installation.
 
5. Une fois l’installation terminée, sélectionnez **Terminer**.  Notez l’adresse web du portail web SnapCenter.  Vous pouvez également y accéder en double-cliquant sur l’icône de SnapCenter disponible sur le Bureau au terme de l’installation.
 
## <a name="disable-enhanced-messaging-service-ems-messages-to-netapp-auto-support"></a>Désactiver les messages EMS (Enhanced Messaging Service) vers NetApp AutoSupport

La collecte de données EMS est activée par défaut et s’exécute tous les sept jours après la date d’installation. Vous pouvez désactiver la collecte de données à tout moment.

1. À partir d’une ligne de commande PowerShell, établissez une session avec SnapCenter :

   ```powershell-interactive
   Open-SmConnection
   ```

2. Connectez-vous avec vos informations d’identification.

3. Désactivez la collecte de données EMS : 

   ```powershell-interactive
   Disable-SmDataCollectionEms
   ```
   
## <a name="next-steps"></a>Étapes suivantes

Découvrez comment configurer SnapCenter :

> [!div class="nextstepaction"]
> [Configurer SnapCenter](configure-snapcenter-oracle-baremetal.md)
