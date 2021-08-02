---
title: Azure Private Link pour Azure Data Factory
description: Découvrez comment fonctionne Azure Private Link dans Azure Data Factory.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/10/2021
ms.openlocfilehash: 9d41ff8d2b0bfd1e83f15366e152398f5de8ccf9
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112020964"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Private Link pour Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

En utilisant Azure Private Link, vous pouvez vous connecter à différents déploiements PaaS (platform as a service) dans Azure via un point de terminaison privé. Un point de terminaison privé est une adresse IP privée au sein d’un réseau et d’un sous-réseau virtuels spécifiques. Pour obtenir la liste des déploiements PaaS prenant en charge la fonctionnalité Liaison privée, consultez la [documentation sur Liaison privée](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Sécuriser la communication entre les réseaux du client et Azure Data Factory 
Vous pouvez configurer un réseau virtuel Azure comme une représentation logique de votre réseau dans le cloud. Les avantages que vous en tirez sont les suivants :
* Vous protégez mieux vos ressources Azure contre les attaques sur les réseaux publics.
* Vous laissez les réseaux et Data Factory communiquer entre eux de façon sécurisée. 

Vous pouvez également connecter un réseau local à votre réseau virtuel en configurant une connexion VPN (site à site) IPsec (sécurité du protocole Internet) ou une connexion Azure ExpressRoute (peering privé). 

Vous pouvez également installer un runtime d’intégration auto-hébergé sur une machine locale ou une machine virtuelle dans le réseau virtuel. Cela vous permet d’effectuer les opérations suivantes :
* Exécuter des activités de copie entre un magasin de données cloud et un magasin de données situé sur un réseau privé.
* Répartir les activités de transformation selon les ressources de calcul dans un réseau local ou un réseau virtuel Azure. 

Plusieurs canaux de communication sont nécessaires entre Azure Data Factory et le réseau virtuel du client, comme indiqué dans le tableau suivant :

| Domain | Port | Description |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Plan de contrôle nécessaire pour la création et la supervision de Data Factory. |
| `*.{region}.datafactory.azure.net` | 443 | Requis par le runtime d’intégration auto-hébergé pour se connecter au service Data Factory. |
| `*.servicebus.windows.net` | 443 | Requis par le runtime d’intégration auto-hébergé pour la création interactive. |
| `download.microsoft.com` | 443 | Exigé par le runtime d’intégration auto-hébergé pour télécharger les mises à jour. |

Avec la prise en charge de Liaison privée pour Azure Data Factory, vous pouvez :
* Créer un point de terminaison privé dans votre réseau virtuel.
* Activer la connexion privée à une instance spécifique de la fabrique de données. 

Les communications avec le service Azure Data Factory transitent par Liaison privée et assurent une connectivité privée sécurisée. 

![Diagramme d’une liaison privée pour Azure Data Factory.](./media/data-factory-private-link/private-link-architecture.png)

L’activation du service Private Link pour chacun des canaux de communication précédents offre les fonctionnalités suivantes :
- **Pris en charge** :
   - Vous pouvez créer et superviser la fabrique de données sur votre réseau virtuel, même si vous bloquez toutes les communications sortantes.
   - Les communications de commande entre le runtime d’intégration auto-hébergé et le service Azure Data Factory peuvent être établies en toute sécurité dans un environnement de réseau privé. Le trafic entre le runtime d’intégration auto-hébergé et le service Azure Data Factory transite par une liaison privée. 
- **Non pris en charge pour l’instant** :
   - Une création interactive qui utilise un runtime d’intégration auto-hébergé, par exemple, pour tester la connexion, parcourir la liste des dossiers et la liste des tables, obtenir le schéma et afficher un aperçu des données transite par Private Link.
   - La nouvelle version du runtime d’intégration auto-hébergé, qui peut être téléchargée automatiquement à partir du centre de téléchargement Microsoft si vous activez la mise à jour automatique, n’est pas prise en charge à l’heure actuelle.

   > [!NOTE]
   > Pour les fonctionnalités non encore prises en charge, vous devez toujours configurer le domaine et le port précédemment mentionnés dans le réseau virtuel ou le pare-feu de votre entreprise. 

   > [!NOTE]
   > La connexion à Azure Data Factory via un point de terminaison privé s’applique uniquement au runtime d’intégration auto-hébergé dans la fabrique de données. Elle n’est pas prise en charge dans Synapse.

> [!WARNING]
> Si vous activez Private Link dans Azure Data Factory et bloquez simultanément l’accès public, assurez-vous que lorsque vous créez un service lié, vos informations d’identification sont stockées dans un coffre de clés Azure. Dans le cas contraire, les informations d’identification ne fonctionneront pas.

## <a name="dns-changes-for-private-endpoints"></a>Modifications DNS pour les points de terminaison privés
Quand vous créez un point de terminaison privé, l’enregistrement de la ressource DNS CNAME pour la fabrique de données est mis à jour avec un alias dans un sous-domaine avec le préfixe « privatelink ». Par défaut, nous créons également une [zone DNS privée](../dns/private-dns-overview.md) correspondant au sous-domaine « privatelink », avec les enregistrements de ressource DNS A pour les points de terminaison privés.

Lorsque vous résolvez l’URL du point de terminaison de la fabrique de données à l’extérieur du réseau virtuel avec le point de terminaison privé, elle correspond au point de terminaison public du service de fabrique de données. En cas de résolution à partir du réseau virtuel hébergeant le point de terminaison privé, l’URL du point de terminaison de stockage correspond à l’adresse IP du point de terminaison privé.

Pour l’exemple illustré ci-dessus, les enregistrements de ressources DNS correspondant à « DataFactoryA » de Data Factory, lorsqu’ils sont résolus à l’extérieur du réseau virtuel hébergeant le point de terminaison privé, sont les suivants :

| Name | Type | Valeur |
| ---------- | -------- | --------------- |
| DataFactoryA.{région}.datafactory.azure.net | CNAME   | DataFactoryA.{région}.privatelink.datafactory.azure.net |
| DataFactoryA.{région}.privatelink.datafactory.azure.net | CNAME   | < point de terminaison public du service de fabrique de données > |
| < point de terminaison public du service de fabrique de données >  | Un | < IP publique du service de fabrique de données > |

Les enregistrements de ressources DNS correspondant à DataFactoryA, lorsqu’ils sont résolus dans le réseau virtuel hébergeant le point de terminaison privé, sont les suivants :

| Name | Type | Valeur |
| ---------- | -------- | --------------- |
| DataFactoryA.{région}.datafactory.azure.net | CNAME   | DataFactoryA.{région}.privatelink.datafactory.azure.net |
| DataFactoryA.{région}.privatelink.datafactory.azure.net   | Un | < adresse IP du point de terminaison privé > |

Si vous utilisez un serveur DNS personnalisé sur votre réseau, les clients doivent pouvoir résoudre le nom de domaine complet du point de terminaison Data Factory vers l’adresse IP du point de terminaison privé. Vous devez configurer votre serveur DNS pour déléguer votre sous-domaine de liaison privée à la zone DNS privée du réseau virtuel, ou configurer les enregistrements A pour « DataFactoryA.{région}.privatelink.datafactory.azure.net » avec l’adresse IP du point de terminaison privé.

Pour plus d’informations sur la configuration de votre propre serveur DNS pour la prise en charge des points de terminaison privés, reportez-vous aux articles suivants :
- [Résolution de noms pour des ressources dans les réseaux virtuels Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Configuration DNS pour les points de terminaison privés](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-a-private-endpoint-link-for-azure-data-factory"></a>Configurer un lien de point de terminaison privé pour Azure Data Factory

Dans cette section, vous allez configurer un lien de point de terminaison privé pour Azure Data Factory.

Vous pouvez choisir de connecter votre runtime d'intégration auto-hébergé à Azure Data Factory via un point de terminaison public ou privé au cours de l’étape de création de la fabrique de données, comme indiqué ici : 

:::image type="content" source="./media/data-factory-private-link/disable-public-access-shir.png" alt-text="Capture d’écran du blocage de l’accès public du runtime d’intégration auto-hébergé.":::

Après la création, vous pouvez à tout moment modifier cette sélection dans le panneau Mise en réseau de la page du portail de fabrique de données.  Après y avoir activé les points de terminaison privés, vous devez également ajouter un point de terminaison privé pour la fabrique de données.

Un point de terminaison privé requiert un réseau virtuel et un sous-réseau pour le lien, de même qu’une machine virtuelle dans le sous-réseau. Elle sera utilisée pour exécuter le runtime d'intégration auto-hébergé, en se connectant via le lien de point de terminaison privé.

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel
En l’absence de réseau virtuel existant à utiliser avec votre lien de point de terminaison privé, vous devez en créer un et attribuer un sous-réseau.  

1. Connectez-vous au portail Azure à l’adresse https://portal.azure.com.
2. En haut à gauche de l’écran, sélectionnez **Créer une ressource > Réseau > Réseau virtuel**, ou recherchez **Réseau virtuel** à partir de la zone de recherche.

3. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | **Paramètre**          | **Valeur**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Détails du projet**  |                                                                 |
    | Abonnement     | Sélectionner votre abonnement Azure                                  |
    | Groupe de ressources   | Sélectionner un groupe de ressources pour votre réseau virtuel |
    | **Détails de l’instance** |                                                                 |
    | Nom             | Entrer un nom pour votre réseau virtuel |
    | Région           | IMPORTANT : sélectionner la même région que celle utilisée par votre point de terminaison privé |

4. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

5. Sous l’onglet **Adresses IP**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Espace d’adressage IPv4 | Entrez **10.1.0.0/16** |

6. Sous **Nom de sous-réseau**, sélectionnez le mot **par défaut**.

7. Dans **Modifier le sous-réseau**, entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du sous-réseau | Entrer un nom pour votre sous-réseau |
    | Plage d’adresses de sous-réseau | Entrez **10.1.0.0/24** |

8. Sélectionnez **Enregistrer**.

9. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

10. Sélectionnez **Create** (Créer).

### <a name="create-a-virtual-machine-for-the-self-hosted-integration-runtime-shir"></a>Créer une machine virtuelle pour le runtime d'intégration auto-hébergé
Vous devez également créer ou attribuer une machine virtuelle existante pour exécuter le runtime d'intégration auto-hébergé dans le nouveau sous-réseau créé ci-dessus.

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**, ou recherchez **Machine virtuelle** dans la zone de recherche.
   
2. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    | Paramètre | Value                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionner un groupe de ressources |
    | **Détails de l’instance** |  |
    | Nom de la machine virtuelle | Entrez un nom pour la machine virtuelle |
    | Région | Sélectionnez la région utilisée ci-dessus pour votre réseau virtuel |
    | Options de disponibilité | Sélectionnez **Aucune redondance d’infrastructure requise**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter - Gen1** (ou toute autre image Windows prenant en charge le runtime d'intégration auto-hébergé) |
    | Instance Azure Spot | Sélectionnez **Non** |
    | Taille | Choisissez la taille de la machine virtuelle ou acceptez le paramètre par défaut |
    | **Compte administrateur** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur |
    | Mot de passe | Entrez un mot de passe |
    | Confirmer le mot de passe | Entrez à nouveau le mot de passe |

3. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**.
  
4. Sous l’onglet Réseau, sélectionnez ou entrez :

    | Paramètre | Value |
    |-|-|
    | **Interface réseau** |  |
    | Réseau virtuel | Sélectionnez le réseau virtuel que vous avez créé ci-dessus. |
    | Subnet | Sélectionnez le sous-réseau créé ci-dessus. |
    | Adresse IP publique | Sélectionnez **Aucun**. |
    | Groupe de sécurité réseau de la carte réseau | **De base**|
    | Aucun port d’entrée public | Sélectionnez **Aucun**. |
   
5. Sélectionnez **Revoir + créer**. 
  
6. Passez en revue les paramètres, puis sélectionnez **Créer**.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="create-the-private-endpoint"></a>Créer le point de terminaison privé 
Enfin, vous devez créer le point de terminaison privé dans votre fabrique de données.

1. Sur la page du portail Azure correspondant à votre fabrique de données, sélectionnez le panneau **Mise en réseau** et l’onglet **Connexions des points de terminaison privés**, puis **+ Point de terminaison privé**. 

:::image type="content" source="./media/data-factory-private-link/create-private-endpoint.png" alt-text="Capture d’écran du volet « Connexions des points de terminaison privés » pour la création d’un point de terminaison privé.":::

2. Sous l’onglet **Général** de **Créer un point de terminaison privé**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** | |
    | Abonnement | Sélectionnez votre abonnement |
    | Resource group | Sélectionner un groupe de ressources |
    | **Détails de l’instance** |  |
    | Nom  | Entrez un nom pour votre point de terminaison |
    | Région | Sélectionnez la région du réseau virtuel créé ci-dessus |

3. Sélectionnez l’onglet **Ressource** ou le bouton **Suivant : Ressource** en bas de la page.
    
4. Dans **Ressource**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Méthode de connexion | Sélectionnez **Se connecter à une ressource Azure dans mon répertoire** |
    | Abonnement | Sélectionnez votre abonnement |
    | Type de ressource | Sélectionnez **Microsoft.Datafactory/factories** |
    | Ressource | Sélectionner votre fabrique de données |
    | Sous-ressource cible | Si vous souhaitez utiliser le point de terminaison privé pour les communications de commande entre le runtime d’intégration auto-hébergé et le service Azure Data Factory, sélectionnez **datafactory** comme **sous-ressource cible**. Si vous souhaitez utiliser le point de terminaison privé pour la création et la surveillance de la fabrique de données dans votre réseau virtuel, sélectionnez **portail** comme **sous-ressource cible**.|

5. Sélectionnez l’onglet **Configuration** ou le bouton **Suivant : Configuration** en bas de l’écran.

6. Dans **Configuration**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Mise en réseau** |  |
    | Réseau virtuel | Sélectionnez le réseau virtuel que vous avez créé ci-dessus. |
    | Subnet | Sélectionnez le sous-réseau créé ci-dessus. |
    | **Intégration à un DNS privé** |  |
    | Intégrer à une zone DNS privée | Conservez la valeur par défaut **Oui**. |
    | Abonnement | Sélectionnez votre abonnement. |
    | Zones DNS privées | Conservez la valeur par défaut de **(Nouveau) privatelink.azurewebsites.net**.
    

7. Sélectionnez **Revoir + créer**.

8. Sélectionnez **Create** (Créer).

> [!NOTE]
> La désactivation de l’accès au réseau public s’applique uniquement au runtime d’intégration auto-hébergé, pas à Azure Integration Runtime ni au runtime d’intégration SSIS (SQL Server Integration Services).

> [!NOTE]
> Vous pouvez toujours accéder au portail Azure Data Factory par le biais d’un réseau public après avoir créé un point de terminaison privé pour le portail.

## <a name="next-steps"></a>Étapes suivantes

- [Créer une fabrique de données à l’aide de l’interface utilisateur d’Azure Data Factory](quickstart-create-data-factory-portal.md)
- [Présentation du service Azure Data Factory](introduction.md)
- [Création visuelle dans Azure Data Factory](author-visually.md)
