---
title: Création et gestion de runtimes d’intégration
description: Cet article décrit la procédure à suivre pour créer et gérer des runtimes d’intégration dans Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 1a51af8fd34516ca87d7ab98332221a308480193
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217134"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>Création et gestion d’un runtime d’intégration auto-hébergé

Cet article explique comment créer et gérer un runtime d’intégration auto-hébergé qui vous permet d’analyser des sources de données dans Azure Purview.

> [!NOTE]
> Le runtime d’intégration Purview ne peut pas être partagé avec un runtime d’intégration Azure Synapse Analytics ou Azure Data Factory sur la même machine. Il doit être installé sur une machine distincte.

> [!IMPORTANT]
> Si vous avez créé votre compte Azure Purview après le 18 août 2021, veillez à télécharger et à installer la dernière version du runtime d’intégration auto-hébergé à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="prerequisites"></a>Prérequis

- Les versions de Windows Server prises en charge sont les suivantes :
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

L’installation du runtime d’intégration auto-hébergé sur un contrôleur de domaine n’est pas prise en charge.

- Le runtime d’intégration auto-hébergé nécessite un système d’exploitation 64 bits avec .NET Framework 4.7.2 ou ultérieur. Consultez [Configuration système requise pour .NET Framework](/dotnet/framework/get-started/system-requirements) pour plus d’informations.
- La configuration minimale recommandée pour la machine exécutant le runtime d’intégration auto-hébergé correspond à un processeur à 2 GHz avec 4 cœurs, 8 Go de RAM et 80 Go d’espace disponible sur le disque dur. Pour plus d'informations sur la configuration requise, consultez [Télécharger](https://www.microsoft.com/download/details.aspx?id=39717).
- Si la machine hôte est en veille prolongée, le runtime d’intégration auto-hébergé ne répond pas aux demandes de données. Configurez un plan d’alimentation approprié sur l’ordinateur avant d’installer le runtime d’intégration auto-hébergé. Si la machine est configurée pour se mettre en veille prolongée, le programme d'installation du runtime d’intégration auto-hébergé ouvre un message.
- Vous devez disposer de droits d'administrateur sur la machine pour correctement installer et configurer le runtime d’intégration auto-hébergé.
- Les analyses sont effectuées à une fréquence spécifique, selon le calendrier que vous avez établi. L'utilisation du processeur et de la RAM sur la machine suit le même modèle avec des périodes de pointe et d’inactivité. L'utilisation des ressources dépend aussi fortement de la quantité de données qui sont analysées. Lorsque plusieurs tâches de numérisation sont en cours, vous constatez une augmentation de l'utilisation des ressources aux heures de pointe.
- Les tâches peuvent échouer lors de l’extraction de données aux formats Parquet, ORC ou Avro.

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Installation d’un runtime d’intégration auto-hébergé

Pour créer et installer un runtime d’intégration auto-hébergé, suivez les procédures ci-dessous.

## <a name="create-a-self-hosted-integration-runtime"></a>Créer un runtime d’intégration auto-hébergé

1. Dans la page d’accueil de [Purview Studio](https://web.purview.azure.com/resource/), sélectionnez **Data Map** dans le volet de navigation gauche.

2. Sous **Sources et analyse** dans le volet gauche, sélectionnez **Runtimes d’intégration**, puis **+ Nouveau**.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="Sélectionnez sur IR.":::

3. Sur la page **Configuration des runtimes d’intégration**, sélectionnez **Auto-hébergé** pour créer un runtime d’intégration auto-hébergé, puis **Continuer**.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="Création d’un runtime d’intégration auto-hébergé":::

4. Entrez un nom pour votre runtime d’intégration, puis sélectionnez Créer.

5. Sur la page **Paramètres des runtime d’intégration**, suivez la procédure décrite dans la section **Configuration manuelle**. Vous devrez télécharger le runtime d’intégration sur une machine virtuelle ou l’ordinateur sur lequel vous envisagez de l’exécuter à partir du site de téléchargement.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="Récupération de la clé":::

   - Copiez et collez la clé d’authentification.

   - Téléchargez le runtime intégration auto-hébergé sur une machine Windows locale à partir de [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717). Exécutez le programme d’installation. Les versions du runtime d’intégration auto-hébergées telles que 5.4.7803.1 et 5.6.7795.1 sont prises en charge. 

   - Dans la page **Inscrire le runtime d’intégration (auto-hébergé)** , collez une des deux clés que vous avez enregistrées avant, puis sélectionnez **Inscrire**.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="Clé d’entrée":::

   - Dans la page **Nouveau runtime d’intégration (auto-hébergé)** , sélectionnez **Terminer**.

6. Une fois le runtime d’intégration auto-hébergé inscrit, la fenêtre suivante s’affiche :

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="Inscription réussie":::

## <a name="networking-requirements"></a>Configuration requise du réseau

Votre machine de runtime d’intégration auto-hébergé doit se connecter à plusieurs ressources pour fonctionner correctement :

* Les sources que vous souhaitez analyser à l’aide du runtime d’intégration auto-hébergé.
* Tout Azure Key Vault utilisé pour stocker des informations d’identification de la ressource Purview.
* Le compte Stockage managé et les ressources Event Hub créées par Purview.

Les ressources Stockage et Event Hub managées sont accessibles dans votre abonnement sous un groupe de ressources contenant le nom de votre ressource Purview. Azure Purview utilise ces ressources pour ingérer les résultats de l’analyse, entre autres choses, afin que le runtime d’intégration auto-hébergé puisse être en mesure de se connecter directement à ces ressources.

Voici les domaines et les ports qui doivent être autorisés via des pare-feu d’entreprise et d’ordinateur.

> [!NOTE]
> Pour les domaines listés avec « \<managed Purview storage account> », vous devez ajouter le nom du compte de stockage managé associé à votre ressource Purview. Vous pouvez trouver cette ressource dans le portail. Recherchez dans vos groupes de ressources un groupe nommé : managed-rg-\<your Purview Resource name>. Par exemple : managed-rg-contosoPurview. Vous allez utiliser le nom du compte de stockage dans ce groupe de ressources.
> 
> Pour les domaines listés avec « \<managed Event Hub resource> », vous devez ajouter le nom du Event Hub managé associé à votre ressource Purview. Vous pouvez le trouver dans le même groupe de ressources que le compte de stockage managé.

| Noms de domaine                  | Ports sortants | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net` | 443            | L’infrastructure globale que Purview utilise pour exécuter ses analyses. Caractère générique requis, car il n’existe aucune ressource dédiée. |
| `<managed Event Hub resource>.servicebus.windows.net` | 443            | Purview utilise cette valeur pour se connecter au service bus associé. Il sera couvert par l’autorisation du domaine ci-dessus, mais si vous utilisez des points de terminaison privés, vous devrez tester l’accès à ce domaine unique.|
| `*.frontend.clouddatahub.net` | 443            | L’infrastructure globale que Purview utilise pour exécuter ses analyses. Caractère générique requis, car il n’existe aucune ressource dédiée. |
| `<managed Purview storage account>.core.windows.net`          | 443            | Utilisé par le runtime d’intégration auto-hébergé pour se connecter au compte de stockage Azure managé.|
| `<managed Purview storage account>.queue.core.windows.net` | 443            | Files d’attente utilisées par Purview pour exécuter le processus d’analyse. |
| `download.microsoft.com` | 443           | Facultatif pour les mises à jour SHIR. |

En fonction de vos sources, vous devrez peut-être également autoriser les domaines d’autres sources Azure ou externes. Quelques exemples sont fournis ci-dessous, ainsi que le domaine Azure Key Vault, si vous vous connectez à des informations d’identification stockées dans le Key Vault.

| Noms de domaine                  | Ports sortants | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `<storage account>.core.windows.net`          | 443            | Facultatif, pour se connecter à un compte de stockage Azure. |
| `*.database.windows.net`      | 1433           | Facultatif, pour se connecter à Azure SQL Database ou Azure Synapse Analytics. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Facultatif, pour se connecter à Azure Data Lake Store Gen 1. |
| `<datastoragename>.dfs.core.windows.net`    | 443            | Facultatif, pour se connecter à Azure Data Lake Store Gen 2. |
| `<your Key Vault Name>.vault.azure.net` | 443           | Obligatoire si des informations d’identification sont stockées dans Azure Key Vault. |
| Différents domaines | Dépendant          | Domaines pour toutes les autres sources auxquelles le SHIR se connectera. |
  
> [!IMPORTANT]
> Dans la plupart des environnements, vous devrez également vérifier que votre DNS est correctement configuré. Confirmer que vous pouvez utiliser **nslookup** depuis votre machine SHIR pour vérifier la connectivité à chacun des domaines susmentionnés. Chaque nslookup doit renvoyer l’adresse IP de la ressource. Si vous utilisez des [points de terminaison](catalog-private-link.md), c’est l’adresse IP privée qui doit être retournée et non l’adresse IP publique. Si aucune adresse IP n’est retournée, ou si, lors de l’utilisation de points de terminaison privés, l’adresse IP publique est retournée, vous devez gérer votre association DNS/VNET ou votre association point de terminaison privé/réseau virtuel.

## <a name="manage-a-self-hosted-integration-runtime"></a>Gestion d’un runtime d’intégration auto-hébergé

Vous pouvez modifier un runtime d'intégration hébergé par vous-même en accédant à **Integration runtimes** dans le **Management center**, en sélectionnant l'IR, puis en sélectionnant edit. Vous pouvez alors mettre à jour la description, copier la clé ou en regénérer de nouvelles.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="Modification du runtime d’intégration":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="Modification des détails du runtime d’intégration":::

Vous pouvez supprimer un runtime d'intégration auto-hébergé en accédant à **Integration runtimes** dans le Management center, en sélectionnant l'IR, puis en sélectionnant **Supprimer**. Une fois le runtime d’intégration supprimé, toutes les analyses en cours qui en dépendent échouent.

## <a name="next-steps"></a>Étapes suivantes

- [Comment les analyses détectent les éléments supprimés](concept-scans-and-ingestion.md#how-scans-detect-deleted-assets)

- [Utiliser des points de terminaison privés avec Purview](catalog-private-link.md)
