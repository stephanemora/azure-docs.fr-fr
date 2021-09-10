---
title: Analyser vos sources de données de manière privée et sécurisée
description: Cet article explique comment configurer un point de terminaison privé pour analyser des sources de données à partir d’un réseau restreint
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: cbdf2220d1b4087376bc40db5b7da167144e5d9b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257191"
---
# <a name="scan-your-data-sources-privately-and-securely"></a>Analyser vos sources de données de manière privée et sécurisée

Si vous envisagez d’analyser vos sources de données dans des réseaux privés, des réseaux virtuels et derrière des points de terminaison privés, des points de terminaison privés d’ingestion Azure Purview doivent être déployés pour garantir l’isolement réseau de vos métadonnées qui circulent à partir des sources de données qui sont analysées dans le mappage de données Azure Purview.

Azure Purview peut analyser des sources de données dans un environnement Azure ou local à l’aide de points de terminaison privés d’_ingestion_. Trois ressources de point de terminaison privé supplémentaires doivent être déployées et liées aux ressources managées Azure Purview quand des points de terminaison privés d’ingestion sont déployés :

- Le point de terminaison privé d’objet blob est lié à un compte de stockage géré Azure Purview.
- Le point de terminaison privé de file d’attente est lié à un compte de stockage managé Azure Purview.
- le point de terminaison privé de l’espace de noms est lié à l’espace de noms Event Hub managé Azure Purview.

:::image type="content" source="media/catalog-private-link/purview-private-link-architecture-ingestion.png" alt-text="Le diagramme qui affiche l’architecture d’Azure Purview et Private Link.":::

## <a name="deployment-checklist"></a>Liste de vérification de déploiement
À l’aide de l’une des options de déploiement de ce guide, activez les points de terminaison privés d’ingestion pour votre compte Azure Purview :

1. Choisissez un réseau virtuel Azure approprié et un sous-réseau pour déployer des points de terminaison privés d’ingestion Azure Purview. Sélectionnez l’une des options suivantes :
   - Déployez un [nouveau réseau virtuel](../virtual-network/quick-create-portal.md) dans votre abonnement Azure.
   - Localisez un réseau virtuel Azure existant et un sous-réseau dans votre abonnement Azure.
  
2. Définissez une [méthode de résolution de noms DNS](./catalog-private-link-name-resolution.md#deployment-options) appropriée pour l’ingestion, de sorte qu’Azure Purview puisse analyser des sources de données à l’aide d’un réseau privé. Vous pouvez utiliser une des options suivantes :
   - Déployez de nouvelles zones Azure DNS à l’aide des étapes décrites plus loin dans ce guide.
   - Ajoutez les enregistrements DNS requis aux zones Azure DNS existantes à l’aide des étapes décrites plus loin dans ce guide.
   - Après avoir effectué les étapes de ce guide, ajoutez manuellement les enregistrements A DNS requis dans vos serveurs DNS existants.
3. Déployez un [nouveau compte Purview](#option-1---deploy-a-new-azure-purview-account-with-ingestion-private-endpoint) avec des points de terminaison privés d’ingestion ou déployez des points de terminaison privés d’ingestion pour un [compte Purview existant](#option-2---enable-ingestion-private-endpoint-on-existing-azure-purview-accounts).
4. Déployez et inscrivez le [runtime d’intégration auto-hébergé](#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources) à l’intérieur du même réseau virtuel où les points de terminaison privés d’ingestion Azure Purview sont déployés.
5. À la fin de ce guide, ajustez les configurations DNS si nécessaire.
6. Validez votre réseau et la résolution de noms entre l’ordinateur de gestion, les machines virtuelles IR auto-hébergées et les sources de données sur Azure Purview. 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_ingestion_-private-endpoint"></a>Option 1 : déployer un nouveau compte Azure Purview avec le point de terminaison privé d’_ingestion_

1. Accédez au [Portail Azure](https://portal.azure.com), puis accédez à la page **Comptes Purview**. Sélectionnez **+ Créer** afin de créer un compte Azure Purview.

2. Entrez les informations de base, puis, sous l’onglet **Mise en réseau**, définissez la méthode de connectivité sur **Point de terminaison privé**. Définissez l’activation d’un point de terminaison privé sur **Ingestion uniquement**.

      :::image type="content" source="media/catalog-private-link/purview-pe-scenario-2-1.png" alt-text="Capture d’écran montrant les sélections de pages Créer des points de terminaison privés.":::

3. Configurez vos points de terminaison privés d’ingestion en fournissant des détails pour **Abonnement**, **Réseau virtuel** et **Sous-réseau** que vous souhaitez appairer avec votre point de terminaison privé.

4. Si vous le souhaitez, sélectionnez **Intégration de DNS privé** pour utiliser les zones DNS privé Azure.
   
   > [!IMPORTANT]
   > Il est important de sélectionner les zones de DNS privé Azure appropriées pour permettre une résolution de nom correcte entre Azure Purview et les sources de données. Vous pouvez également utiliser vos zones Azure DNS privé existantes ou créer des enregistrements DNS dans vos serveurs DNS manuellement par la suite. Pour plus d’informations, consultez [Configurer la résolution de noms DNS pour les points de terminaison privés](./catalog-private-link-name-resolution.md).

5.  Sélectionnez **Vérifier + créer**. Sur la page **Revoir + créer**, Azure valide votre configuration.

6.  Quand le message « Validation réussie » s’affiche, sélectionnez **Créer**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Capture d’écran montrant que la validation a réussi pour la création du compte.":::

## <a name="option-2---enable-_ingestion_-private-endpoint-on-existing-azure-purview-accounts"></a>Option 2 : activer le points de terminaison privé _Ingestion_ sur les comptes Azure Purview existants

1.  Accédez au [Portail Azure](https://portal.azure.com), puis cliquez sur votre compte Azure Purview, et sous **Paramètres**, sélectionnez **Mise en réseau**, puis sélectionnez **Connexions de point de terminaison privé d’ingestion**.

2. Sous Connexions de point de terminaison privé d’ingestion, sélectionnez **+ Nouveau** pour créer un point de terminaison privé d’ingestion.

3. Renseignez les informations de base, en sélectionnant votre réseau virtuel existant et les détails du sous-réseau. Si vous le souhaitez, sélectionnez **Intégration de DNS privé** pour utiliser les zones DNS privé Azure. 
   
   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Capture d’écran montrant le remplissage des détails du point de terminaison privé.":::
   
   > [!IMPORTANT]
   > Il est important de sélectionner les zones de DNS privé Azure appropriées pour permettre une résolution de nom correcte entre Azure Purview et les sources de données. Vous pouvez également utiliser vos zones Azure DNS privé existantes ou créer des enregistrements DNS dans vos serveurs DNS manuellement par la suite. 
   > 
   >Pour plus d’informations, consultez [Configurer la résolution de noms DNS pour les points de terminaison privés](./catalog-private-link-name-resolution.md).


4. Sélectionnez **Créer** pour terminer le processus.

> [!NOTE]
> Vous ne pouvez créer des points de terminaison privés d’ingestion que via le portail Azure Purview décrit dans les étapes précédentes. Vous ne pouvez pas le faire à partir du Centre Private Link.

## <a name="deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources"></a>Déployez le runtime d’intégration auto-hébergé (IR) et analysez vos sources de données.
Une fois que vous avez déployé des points de terminaison privés d’ingestion pour votre Azure Purview, vous devez configurer et inscrire au moins un runtime d’intégration auto-hébergé (IR) :

- Tous les types de sources locales, tels que Microsoft SQL Server, Oracle, SAP et autres, ne sont actuellement pris en charge que via des analyses basées sur un IR auto-hébergé. L’IR auto-hébergé doit s’exécuter au sein de votre réseau privé, et être appairé avec votre réseau virtuel dans Azure. 
   
- Pour tous les types de sources Azure comme Stockage Blob Azure et Azure SQL Database, vous devez explicitement choisir d’exécuter l’analyse à l’aide d’un runtime d’intégration auto-hébergé qui est déployé dans le même réseau virtuel que le point de terminaison privé d’ingestion Azure Purview. 

Suivez les étapes dans [Création et gestion d’un runtime d’intégration auto-hébergé](manage-integration-runtimes.md) pour configurer un IR auto-hébergé. Configurez ensuite votre analyse sur la source Azure en sélectionnant cet IR auto-hébergé dans la liste déroulante **Se connecter via le runtime d’intégration** pour garantir l’isolement réseau.
    
   :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Capture d’écran montrant l’exécution d’une analyse Azure à l’aide du runtime d’intégration auto-hébergé.":::

> [!IMPORTANT]
> Si vous avez créé votre compte Azure Purview après le 18 août 2021, veillez à télécharger et à installer la dernière version du runtime d’intégration auto-hébergé à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
> 
## <a name="next-steps"></a>Étapes suivantes

-  [Vérifier la résolution des points de terminaison privés](./catalog-private-link-name-resolution.md)
-  [Gérer des sources de données dans Azure Purview](./manage-data-sources.md)
-  [Résolution des problèmes de configuration des point de terminaison privés pour votre compte Azure Purview](./catalog-private-link-troubleshoot.md)
