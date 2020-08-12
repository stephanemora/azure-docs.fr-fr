---
title: Utiliser Application Gateway pour protéger vos applications web sur Azure VMware Solution
description: Configurez Azure Application Gateway pour exposer en toute sécurité vos applications web s’exécutant sur Azure VMware Solution (AVS).
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: dfe55ab6b32e9c7b73b8501a16fa6cfaad5bbabe
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87514123"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Utiliser Application Gateway pour protéger vos applications web sur Azure VMware Solution

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) est un équilibreur de charge de trafic web de couche 7 qui vous permet de gérer le trafic vers vos applications web. Il offre de nombreuses fonctionnalités telles que l’affinité de session basée sur des cookies, le routage basé sur l’URL et le pare-feu d’applications web (WAF) (pour obtenir la liste complète des fonctionnalités, consultez [Fonctionnalités Azure Application Gateway](../application-gateway/features.md)). Elle est proposée dans deux versions, v1 et v2. Toutes deux ont été testées avec des applications web s’exécutant sur Azure VMware Solution (AVS).

Cet article aborde un scénario courant utilisant Application Gateway devant une batterie de serveurs web avec un ensemble de configurations et recommandations pour protéger une application web s’exécutant sur Azure VMware Solution (AVS). 

## <a name="topology"></a>Topologie
Comme illustré dans la figure suivante, Application Gateway peut être utilisé pour protéger des machines virtuelles Azure IaaS, des groupes de machines virtuelles identiques Azure ou des serveurs locaux. Application Gateway traite des machines virtuelles AVS comme des serveurs locaux.

![Application Gateway protège les machines virtuelles AVS.](media/protect-avs-web-apps-with-app-gw/app-gateway-protects.png)

> [!IMPORTANT]
> Azure Application Gateway est actuellement la seule méthode prise en charge pour exposer des applications web s’exécutant sur des machines virtuelles AVS.

Le diagramme suivant illustre le scénario de test utilisé pour valider Application Gateway avec des applications web AVS.

![Intégration d’Application Gateway avec AVS exécutant des applications web.](media/protect-avs-web-apps-with-app-gw/app-gateway-avs-scenario.png)

L’instance Application Gateway est déployée sur le hub dans un sous-réseau dédié. Comme elle a une adresse IP publique Azure, l’activation de la protection DDoS Standard pour le réseau virtuel est recommandée. Le serveur web est hébergé sur un cloud privé AVS derrière des routeurs NSX T0 et T1. AVS utilise le [Service Global Reach d’ExpressRoute](../expressroute/expressroute-global-reach.md) pour activer la communication avec le hub et les systèmes locaux.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif.
- Cloud privé AVS déployé et en cours d’exécution.

## <a name="deployment-and-configuration"></a>Déploiement et configuration

1. Dans le portail Azure, recherchez **Application Gateway**, puis sélectionnez **Créer une passerelle Application Gateway**.

2. Fournissez les informations de base, comme dans l’illustration suivante, puis sélectionnez **Suivant : Serveurs frontaux>** . 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/create-app-gateway.png" alt-text="Création d’une passerelle Application Gateway":::

3. Choisissez le type d’adresse IP frontale. Pour l’option Publique, choisissez une adresse IP publique ou créez-en une. Sélectionnez **Suivant : Serveurs principaux>** .

    > [!NOTE]
    > Seules les références (SKU) Standard et de pare-feu d’applications web (WAF) sont prises en charge pour des serveurs frontaux privés.

4. Ensuite, ajoutez un pool principal décrivant un ensemble d’instances faisant partie de l’application ou du service (dans ce cas, des machines virtuelles s’exécutant sur une infrastructure AVS). Fournissez les détails des serveurs web qui s’exécutent sur le cloud privé AVS, puis sélectionnez **Ajouter**. Ensuite, sélectionnez **Suivant : Configuration>** .

1. Sous l’onglet **Configuration**, sélectionnez **Ajouter une règle d’acheminement**.

6. Sous l’onglet **Écouteur**, entrez les détails de l’écouteur. Si le protocole HTTPS est sélectionné, un certificat doit être fourni, soit à partir d’un fichier PFX, soit à partir d’un certificat existant à d’Azure Key Vault. 

7. Sélectionnez l’onglet **Cibles de back-end**, puis le pool principal créé précédemment. Pour le champ **Paramètres HTTP**, sélectionnez **Ajouter nouveau**.

8. Configurez les paramètres HTTP. Sélectionnez **Ajouter**.

9. Si vous souhaitez configurer des règles basées sur le chemin d’accès, sélectionnez **Ajouter plusieurs cibles pour créer une règle basée sur le chemin**. 

10. Ajoutez une règle basée sur un chemin d’accès, et sélectionnez **Ajouter**. Répétez cette opération pour ajouter d’autres règles basées sur un chemin d’accès. 

11. Lorsque vous avez fini d’ajouter des règles basées sur un chemin d’accès, sélectionnez de nouveau **Ajouter**. Ensuite, sélectionnez **Suivant : Balises>** . 

12. Ajoutez les balises souhaitées. Sélectionnez **Suivant : Vérifier + créer**.

13. Une validation est exécutée sur votre passerelle Application Gateway. En cas de réussite, sélectionnez **Créer** pour opérer le déploiement.

## <a name="configuration-examples"></a>Exemples de configuration

Dans cette section, vous allez apprendre à configurer Application Gateway avec des machines virtuelles AVS comme pools principaux pour les cas d’utilisation suivants : 

- [Hébergement de plusieurs sites](#hosting-multiple-sites)
- [Routage par URL](#routing-by-url)

### <a name="hosting-multiple-sites"></a>Hébergement de plusieurs sites

Vous pouvez utiliser le portail Azure pour configurer l’hébergement de plusieurs sites web quand vous créez une passerelle Application Gateway. Dans ce tutoriel, vous allez définir des pools d’adresses principaux à l’aide de machines virtuelles s’exécutant sur un cloud privé AVS sur une passerelle Application Gateway existante. La passerelle Application Gateway fait partie d’un réseau virtuel hub, comme décrit dans [intégrer AVS dans une architecture en étoile](concepts-avs-hub-and-spoke-integration.md). Ce tutoriel part du principe que vous avez plusieurs domaines et utilise les exemples de www.contoso.com et www.fabrikam.com.

1. Créez les machines virtuelles. Sur le cloud privé AVS, créez deux pools de machines virtuelles. L’une représentera Contoso et l’autre Fabrikam. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs.png" alt-text="Pool de serveurs web sur AVS":::

    Pour illustrer ce tutoriel, nous avons utilisé Windows Server 2016 avec le rôle Internet Information Services (IIS) installé. Une fois les machines virtuelles installées, exécutez les commandes PowerShell suivantes pour configurer IIS sur chaque machine virtuelle. 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. Ajouter les pools principaux Dans une instance Application Gateway existante, sélectionnez **Pools principaux** dans le menu à gauche, sélectionnez **Ajouter**, puis entrez les détails des nouveaux pools. Sélectionnez **Ajouter** dans le volet droit.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png" alt-text="Configuration du pool principal" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png":::

3. Dans la section **Écouteurs**, créez un écouteur pour chaque site web. Entrez les détails de chaque écouteur, puis sélectionnez **Ajouter**.

4. Dans le volet de navigation à gauche, sélectionnez **Paramètres HTTP**, puis choisissez **Ajouter** dans le volet gauche. Renseignez les détails pour créer un paramètre HTTP, puis sélectionnez **Enregistrer**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png" alt-text="Configuration des paramètres HTTP" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png":::

5. Créez les règles dans la section **Règles** du menu à gauche. Associez chaque règle à l’écouteur correspondant. Sélectionnez **Ajouter**.

6. Configurez le pool principal et les paramètres HTTP correspondants. Sélectionnez **Ajouter**.

7. Testez la connexion. Ouvrez votre navigateur préféré et accédez aux différents sites web hébergés dans votre environnement AVS, par exemple http://www.fabrikam.com.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-07.png" alt-text="Règle de configuration du backend":::

### <a name="routing-by-url"></a>Routage par URL

Vous pouvez utiliser Azure Application Gateway pour configurer des règles d’acheminement basées sur le chemin d’accès de l’URL. Dans ce tutoriel, vous allez définir des pools d’adresses principaux à l’aide de machines virtuelles s’exécutant sur un cloud privé AVS sur une passerelle Application Gateway existante. La passerelle Application Gateway fait partie d’un réseau virtuel hub, comme décrit dans la [documentation relative à l’intégration native d’Azure AVS](concepts-avs-hub-and-spoke-integration.md). Il permet ensuite de définir des règles d’acheminement qui font en sorte que le trafic web arrive sur les serveurs voulus dans les pools.

1. Créez les machines virtuelles. Dans le cloud privé AVS, créez un pool de machines virtuelles pour représenter la batterie de serveurs web. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs.png" alt-text="Pool de serveurs web sur AVS":::

    Le rôle Windows Server 2016 avec IIS installé a été utilisé pour illustrer ce tutoriel. Une fois les machines virtuelles installées, exécutez les commandes PowerShell suivantes pour configurer IIS pour le tutoriel dans chaque machine virtuelle. 

    La première machine virtuelle, contoso-web-01, hébergera le site web principal.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    La deuxième machine virtuelle, contoso-web-02, hébergera le site d’images.
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    La troisième machine virtuelle, contoso-web-03, hébergera le site de vidéos.

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. Ajouter les pools principaux Vous devrez ajouter trois nouveaux pools principaux dans une instance Application Gateway existante. Sélectionnez **Pools principaux** dans le menu de gauche. Sélectionnez **Ajouter** et entrez les détails du premier pool, **contoso-web**. Ajoutez une machine virtuelle en tant que cible. Sélectionnez **Ajouter**. Répétez ce processus pour **contoso-images** et **contoso-video**, en ajoutant une machine virtuelle unique à chaque cible. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png" alt-text="Création d’un pool principal" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png":::

3. Dans la section **Écouteurs**, créez un écouteur de type De base utilisant le port 8080.

4. Dans le volet de navigation à gauche, sélectionnez **Paramètres HTTP**, puis choisissez **Ajouter** dans le volet gauche. Renseignez les détails pour créer un paramètre HTTP, puis sélectionnez **Enregistrer**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-04.png" alt-text="Configuration des paramètres HTTP":::

5. Créez les règles dans la section **Règles** du menu à gauche. Associez chaque règle à l’écouteur créé précédemment. Configurez ensuite le pool principal principal et les paramètres HTTP. Sélectionnez **Ajouter**.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-07.png" alt-text="Règle de configuration du backend":::

6. Testez la configuration. Accédez à la passerelle d’application sur le Portail Azure, puis, dans la section **Vue d’ensemble**, copiez l’adresse IP publique. Ouvrez ensuite une nouvelle fenêtre de navigateur et entrez l’URL `http://<app-gw-ip-address>:8080`. 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Test de configuration":::

    Définissez l’URL sur `http://<app-gw-ip-address>:8080/images/test.htm`.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-09.png" alt-text="Test de configuration":::

    Définissez de nouveau l’URL sur `http://<app-gw-ip-address>:8080/video/test.htm`.

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-10.png" alt-text="Test de configuration":::

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres exemples de configuration, consultez la [documentation d’Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/).
