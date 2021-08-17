---
title: Tutoriel de configuration des paramètres réseau pour l'appareil Azure Stack Edge Pro avec GPU sur le portail Azure | Microsoft Docs
description: Le tutoriel de déploiement d'Azure Stack Edge Pro avec GPU vous explique comment configurer les paramètres du réseau, du réseau de calcul et du proxy web pour votre appareil physique.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 07/07/2021
ms.author: alkohli
ms.openlocfilehash: 3c3b2bc20481da45bd9345f7668382521642a074
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114221279"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-with-gpu"></a>Tutoriel : Configurer le réseau pour Azure Stack Edge Pro avec GPU

Ce tutoriel explique comment configurer le réseau pour votre appareil Azure Stack Edge Pro avec GPU intégré à l'aide de l'interface utilisateur web locale.

Le processus de connexion peut prendre environ 20 minutes.

Ce tutoriel vous fournira des informations sur :

> [!div class="checklist"]
>
> * Prérequis
> * Configurer le réseau
> * Activer le réseau de calcul
> * Configurer le proxy web


## <a name="prerequisites"></a>Prérequis

Avant de configurer et d'activer votre appareil Azure Stack Edge Pro avec GPU, vérifiez que :

* Vous avez installé l'appareil physique, comme indiqué dans [Installer Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).
* Vous vous êtes connecté à l'interface utilisateur web locale de l'appareil, comme indiqué dans [Se connecter à Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md).


## <a name="configure-network"></a>Configurer le réseau

La page **Bien démarrer** comprend les différents paramètres nécessaires à la configuration et à l’inscription de l’appareil physique auprès du service Azure Stack Edge. 

Procédez comme suit pour configurer le réseau de votre appareil.

1. Accédez à la page **Bien démarrer** de l’interface utilisateur web locale de votre appareil. 

2. Dans la vignette **Réseau**, sélectionnez **Configurer**.  
    
    ![Vignette « Paramètres réseau » de l’interface utilisateur web locale](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    Votre appareil physique comporte six interfaces réseau. Le PORT 1 et le PORT 2 sont des interfaces réseau de 1 Gbit/s. Les PORTS 3, 4, 5 et 6 sont tous des interfaces réseau de 25 Gbits/s qui peuvent également servir d’interfaces réseau de 10 Gbits/s. Le PORT 1 est automatiquement configuré comme port réservé à la gestion, et les PORTS 2 à 6 sont tous des ports de données. Pour un nouvel appareil, la page **Paramètres réseau** est illustrée ci-dessous.
    
    ![Page « Paramètres réseau » de l’interface utilisateur web locale](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)

3. Pour modifier les paramètres réseau, sélectionnez un port et, dans le volet droit qui s’affiche, modifiez l’adresse IP, le sous-réseau, la passerelle, le DNS principal et le DNS secondaire. 

    - Si vous sélectionnez le Port 1, vous pouvez voir qu’il est préconfiguré comme statique. 

        ![Interface utilisateur web locale, « Paramètres réseau du Port 1 »](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - Si vous sélectionnez le Port 2, 3, 4 ou 5, tous ces ports sont configurés en tant que DHCP par défaut.

        ![Interface utilisateur web locale, « Paramètres réseau du Port 3 »](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    Lorsque vous configurez les paramètres réseau, gardez à l’esprit :

    * Assurez-vous que les ports 5 et 6 sont connectés pour les déploiements de Network Function Manager. Pour plus d’informations, consultez [Tutoriel : Déployer des fonctions réseau sur Azure Stack Edge (préversion)](../network-function-manager/deploy-functions.md).
    * Si le protocole DHCP est activé dans votre environnement, les interfaces réseau sont configurées automatiquement. Une adresse IP, un sous-réseau, une passerelle et un DNS sont automatiquement attribués.
    * Si le protocole DHCP n’est pas activé, vous pouvez, le cas échéant, attribuer des adresses IP statiques.
    * Vous pouvez configurer votre interface réseau sur IPv4.
    * Sur les interfaces 25 Gbits/s, vous pouvez définir le mode RDMA (accès direct à distance) sur iWarp ou RoCE (RDMA sur Ethernet). Si des latences faibles sont l’exigence principale et que l’évolutivité n’est pas un problème, utilisez RoCE. Lorsque la latence est une exigence essentielle, mais que la facilité d’utilisation et l’évolutivité ont également des priorités élevées, iWARP est le meilleur choix.
    * L’association de cartes d’interface réseau et l’agrégation de liens ne sont pas prises en charge avec Azure Stack Edge. 
    * Le numéro de série d’un port correspond au numéro de série du nœud.

    Une fois le réseau de l’appareil configuré, la page est mise à jour comme indiqué ci-dessous.

    ![Page 2 « Paramètres réseau » de l’interface utilisateur web locale](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     > [!NOTE]
     > Nous vous recommandons de ne pas remplacer Statique par DHCP pour l’adresse IP locale de l’interface réseau, sauf si vous disposez d’une autre adresse IP pour vous connecter à l’appareil. Si vous utilisez une seule interface réseau et que vous passez à DHCP, il n’y a aucun moyen de déterminer l’adresse DHCP. Si vous voulez passer à une adresse DHCP, attendez que l’appareil se soit activé auprès du service, puis faites la modification. Vous pouvez ensuite voir les adresses IP de tous les adaptateurs dans les **propriétés de l’appareil** dans le portail Azure pour votre service.


    Après avoir configuré et appliqué les paramètres réseau, sélectionnez **Suivant : Calcul** pour configurer le réseau de calcul.

## <a name="enable-compute-network"></a>Activer le réseau de calcul

Procédez comme suit pour activer le calcul et configurer le réseau de calcul. 

<!--1. Go to the **Get started** page in the local web UI of your device. On the **Network** tile, select **Compute network**.  

    ![Compute page in local UI 1](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)-->

1. Dans la page **Calcul**, sélectionnez une interface réseau que vous souhaitez activer pour le calcul. 

    ![Page Calcul de l'interface utilisateur web locale 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. Dans la boîte de dialogue **Paramètres réseau**, sélectionnez **Activer**. Lorsque vous activez le calcul, un commutateur virtuel est créé sur votre appareil sur cette interface réseau. Le commutateur virtuel est utilisé pour l’infrastructure de calcul sur l’appareil. 
    
1. Affectez des **adresses IP aux nœuds Kubernetes**. Ces adresses IP statiques sont destinées à la machine virtuelle de calcul.  

    Pour un appareil à *n* nœuds, une plage contiguë d’au moins *n+1* adresses IPv4 (ou plus) est fournie pour la machine virtuelle de calcul en utilisant les adresses IP de début et de fin. Étant donné que Azure Stack Edge est un appareil à 1 nœud, un minimum de 2 adresses IPv4 contiguës sont fournies.

    > [!IMPORTANT]
    > Kubernetes sur Azure Stack Edge utilise le sous-réseau 172.27.0.0/16 pour le pod, et le sous-réseau 172.28.0.0/16 pour le service. Vérifiez qu’ils ne sont pas actuellement utilisés sur votre réseau. Si ces sous-réseaux sont déjà utilisés sur votre réseau, vous pouvez modifier ces sous-réseaux en exécutant l’applet de commande `Set-HcsKubeClusterNetworkInfo` à partir de l’interface PowerShell de l’appareil. Pour plus d’informations, consultez [Modifier les sous-réseaux du pod et du service Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Affectez des **adresses IP des services externes Kubernetes**. Il s’agit également des adresses IP d’équilibrage de charge. Ces adresses IP contiguës sont destinées aux services que vous souhaitez exposer en dehors du cluster Kubernetes, et vous spécifiez la plage d’adresses IP statiques en fonction du nombre de services exposés. 
    
    > [!IMPORTANT]
    > Nous vous recommandons vivement de spécifier un minimum de 1 adresse IP pour le service Azure Stack Edge Hub pour accéder aux modules de calcul. Vous pouvez ensuite éventuellement spécifier des adresses IP supplémentaires pour d’autres services/modules IoT Edge (1 par service/module) qui doivent être accessibles à partir de l’extérieur du cluster. Les adresses IP du service peuvent être mises à jour ultérieurement. 
    
1. Sélectionnez **Appliquer**.

    ![Page Calcul dans l'interface utilisateur web locale 3](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Quelques minutes sont nécessaires pour appliquer la configuration, et une actualisation du navigateur peut être requise. Vous pouvez constater que le port spécifié est activé pour le calcul. 
 
    ![Page Calcul de l'interface utilisateur web locale 4](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Sélectionnez **Suivant : Proxy web** pour configurer le proxy web.  

  
## <a name="configure-web-proxy"></a>Configurer le proxy web

Cette configuration est facultative.

> [!IMPORTANT]
> * Les fichiers PAC ne sont pas pris en charge. Un fichier PAC définit la manière dont les navigateurs web et les autres agents utilisateurs peuvent choisir automatiquement le serveur proxy approprié (méthode d’accès) pour récupérer une URL donnée. 
> * Les proxys transparents fonctionnent bien avec Azure Stack Edge Pro. Pour les proxies non transparents qui interceptent et lisent tout le trafic (par le biais de leurs propres certificats installés sur le serveur proxy), chargez la clé publique du certificat du proxy comme chaîne de signature sur votre appareil Azure Stack Edge Pro. Vous pouvez alors configurer les paramètres du serveur proxy sur votre appareil Azure Stack Edge. Pour plus d’informations, consultez [Apporter vos propres certificats et les télécharger par le biais de l’interface utilisateur locale](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates).  

<!--1. Go to the **Get started** page in the local web UI of your device.
2. On the **Network** tile, configure your web proxy server settings. Although web proxy configuration is optional, if you use a web proxy, you can configure it on this page only.

   ![Local web UI "Web proxy settings" page](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)-->

1. Dans la page **Paramètres du proxy web**, procédez comme suit :

   1. Dans la zone **URL de proxy Web**, entrez l’URL dans ce format : `http://host-IP address or FQDN:Port number`. Les URL HTTPS ne sont pas prises en charge.

   2. Cliquez sur **Appliquer** pour valider et appliquer les paramètres du proxy web que vous avez configurés.

   ![Page 2 « Paramètres du proxy web » de l’interface utilisateur web locale](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)<!--UI text update for instruction text is needed.-->

2. Une fois les paramètres appliqués, sélectionnez **Suivant : Appareil**.


## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous à présenté les point suivants :

> [!div class="checklist"]
> * Prérequis
> * Configurer le réseau
> * Activer le réseau de calcul
> * Configurer le proxy web


Pour savoir comment configurer votre appareil Azure Stack Edge Pro, consultez :

> [!div class="nextstepaction"]
> [Configurer les paramètres des appareils](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
