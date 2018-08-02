---
title: Utilisation du service Azure Import/Export pour transférer des données vers et à partir de Stockage Azure | Microsoft Docs
description: Découvrez comment créer des tâches d’importation et d’exportation dans le portail Azure pour transférer des données vers et à partir de Stockage Azure.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/11/2018
ms.author: alkohli
ms.openlocfilehash: c435e21d85ae0ab35bc2fa99f7006e841eaecec0
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248772"
---
# <a name="what-is-azure-importexport-service"></a>Qu’est-ce que le service Azure Import/Export ?

Le service Azure Import/Export est utilisé pour importer en toute sécurité des volumes importants de données dans Stockage Blob Azure et Azure Files en expédiant des lecteurs de disque vers un centre de données Azure. Vous pouvez également utiliser ce service pour transférer des données de Stockage Blob Azure vers des lecteurs de disque et les expédier vers vos sites locaux. Les données d’un ou plusieurs disques peuvent être importées dans Stockage Blob Azure ou Azure Files. 

Le service Azure Import/Export vous oblige à fournir vos propres disques. Si vous souhaitez transférer des données à l’aide de disques fournis par Microsoft, vous pouvez utiliser un disque Azure Data Box pour importer des données dans Azure. Microsoft envoie jusqu’à 5 disques SSD chiffrés dotés d’une capacité de 40 To par commande à votre centre de données via un transporteur régional. Vous pouvez rapidement configurer les disques, y copiez des données via une connexion USB 3.0, puis les réexpédier à Azure. Pour plus d’informations, accédez à la [vue d’ensemble d’Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview).

## <a name="azure-importexport-usecases"></a>Scénarios d’utilisation du service Azure Import/Export

Envisagez d’utiliser le service Azure Import/Export lorsque le chargement ou le téléchargement de données sur le réseau est trop lent ou lorsque l’obtention d’une bande passante réseau supplémentaire est coûteuse. Utilisez ce service dans les situations suivantes :

* **Migration de données vers le cloud** : déplacez rapidement de grandes quantités de données vers Azure et à moindre coût.
* **Distribution de contenu** : envoyez rapidement des données aux sites de vos clients.
* **Sauvegarde** : sauvegardez vos données locales dans Stockage Azure.
* **Récupération des données** : récupérez de grandes quantités de données stockées pour les transférer vers votre site local.

## <a name="importexport-components"></a>Composants du service Azure Import/Export

Le service Azure Import/Export utilise les composants suivants :

- **Service Import/Export** : ce service disponible dans le Portail Azure permet à l’utilisateur de créer et d’effectuer le suivi de travaux d’importation et d’exportation.  

- **Outil WAImportExport** : outil en ligne de commande qui effectue les opérations suivantes : 
    - prépare vos lecteurs expédiés à l’importation ;
    - facilite la copie de vos données sur le lecteur ;
    - chiffre les données sur le lecteur avec BitLocker ;
    - génère les fichiers journaux de lecteur utilisés lors de la création de l’importation ;
    - permet d’identifier le nombre de disques requis pour les travaux d’exportation.

    Cet outil est disponible dans deux versions, la version 1 et la version 2. Nous vous recommandons d’utiliser :

    - la version 1 pour l’importation/exportation dans Stockage Blob Azure ; 
    - la version 2 pour l’importation de données dans des fichiers Azure Files.

    L’outil WAImportExport est compatible uniquement avec le système d’exploitation Windows 64 bits. Pour connaître les versions de système d’exploitation prises en charge, consultez [Conditions requises pour le service Azure Import/Export](storage-import-export-requirements.md#supported-operating-systems).

- **Disques** : vous pouvez expédier des disques SSD (Solid-state drive) ou des disques HDD (Hard disk drive) au centre de données Azure. Lorsque vous créez un travail d’importation, vous expédiez les lecteurs de disque contenant vos données. Lorsque vous créez un travail d’exportation, vous expédiez des lecteurs vides au centre de données Azure. Pour les types de disques, consultez [Types de disques pris en charge](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Comment fonctionne le service Import/Export ?

Le service Azure Import/Export permet le transfert de données dans des objets blob Azure ou des fichiers Azure Files en créant des travaux. Utilisez le Portail Azure ou l’API REST Azure Resource Manager pour créer des travaux. Chaque travail est associé à un seul compte de stockage. 

Il peut s’agir d’un travail d’importation ou d’exportation. Un travail d’importation vous permet d’importer des données dans des objets blob Azure ou des fichiers Azure Files, tandis que le travail d’exportation permet d’exporter des données à partir d’objets blob Azure. Dans le cas d’un travail d’importation, vous expédiez des disques contenant vos données. Lorsque vous créez un travail d’exportation, vous expédiez des lecteurs vides à un centre de données Azure. Dans les deux cas, vous pouvez expédier jusqu’à 10 lecteurs de disque par travail.

> [!IMPORTANT]
> L’exportation de données dans des fichiers Azure Files n’est pas prise en charge.

Cette section décrit la procédure générale mise en œuvre dans les travaux d’importation et d’exportation. 


### <a name="inside-an-import-job"></a>Dans un travail d’importation

Globalement, un travail d’importation comprend les opérations suivantes :

1. Déterminer les données à importer, le nombre de disques dont vous avez besoin, l’emplacement de l’objet blob de destination de vos données dans Stockage Azure.
2. Utiliser l’outil WAImportExport pour copier les données sur les lecteurs de disque. Chiffrer les disques avec BitLocker.
3. Créer un travail d’importation dans votre compte de stockage cible dans le Portail Azure. Charger les fichiers journaux du lecteur.
4. Indiquer l’adresse de retour et le numéro de compte transporteur à utiliser pour le retour des disques.
5. Expédier les lecteurs de disque à l’adresse d’expédition indiquée lors de la création du travail.
6. Mettez à jour le numéro de suivi et envoyez le travail d’importation.
7. Les disques sont réceptionnés et traités dans le centre de données Azure.
8. Les disques sont expédiés à l’aide de votre compte de transporteur à l’adresse de retour indiquée dans le travail d’importation.

> [!NOTE]
> Pour les expéditions à l’échelle nationale (dans le pays du centre de données), veuillez partager un compte de transporteur domestique 
>
> Pour les expéditions à l’étranger (en dehors du pays du centre de données), veuillez partager un compte de transporteur international

 ![Figure 1 : flux d’importation de travail](./media/storage-import-export-service/importjob.png)

Pour obtenir des instructions pas à pas sur l’importation de données, consultez :

- [Importer des données dans des objets blob Azure](storage-import-export-data-to-blobs.md)
- [Importer des données dans des fichiers Azure Files](storage-import-export-data-to-files.md)


### <a name="inside-an-export-job"></a>Dans un travail d’exportation

> [!IMPORTANT]
> Le service prend uniquement en charge l’exportation d’objets blob Azure. L’exportation de fichiers Azure n’est pas prise en charge.

Globalement, un travail d’exportation comprend les opérations suivantes :

1. Déterminer les données à exporter, le nombre de lecteurs vous besoin, les objets blob sources ou les chemins de conteneur de vos données dans le stockage blob.
3. Créer un travail d’importation dans votre compte de stockage source dans le Portail Azure.
4. Spécifier les objets blob sources ou les chemins de conteneur de vos données à exporter.
5. Indiquer l’adresse de retour et le numéro de compte transporteur à utiliser pour le retour des disques.
6. Expédier les lecteurs de disque à l’adresse d’expédition indiquée lors de la création du travail.
7. Mettez à jour le numéro de suivi et envoyez le travail d’exportation.
8. Les disques sont réceptionnés et traités dans le centre de données Azure.
9. Les disques sont chiffrés à l’aide de BitLocker et les clés sont disponibles via le Portail Azure.  
10. Les disques sont expédiés à l’aide de votre compte de transporteur à l’adresse de retour indiquée dans le travail d’importation.

> [!NOTE]
> Pour les expéditions à l’échelle nationale (dans le pays du centre de données), veuillez partager un compte de transporteur domestique 
>
> Pour les expéditions à l’étranger (en dehors du pays du centre de données), veuillez partager un compte de transporteur international
  
 ![Figure 2 : flux d’exportation de travail](./media/storage-import-export-service/exportjob.png)

Pour obtenir des instructions pas à pas sur l’exportation de données, consultez[Exporter des données à partir d’objets blob Azure](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Disponibilité des régions 

Le service Azure Import/Export prend en charge la copie des données vers et depuis tous les comptes de stockage Azure. Vous pouvez expédier des lecteurs de disque aux emplacements suivants. Si votre compte de stockage se trouve dans un emplacement Azure qui n’est pas spécifié ici, un emplacement d’expédition de remplacement est proposé lorsque vous créez le travail.

### <a name="supported-shipping-locations"></a>Emplacements d’expédition pris en charge :


|Pays  |Pays  |Pays  |Pays  |
|---------|---------|---------|---------|
|Est des États-Unis    | Europe du Nord        | Inde centrale        |US Gov Iowa         |
|États-Unis de l’Ouest     |Europe de l'Ouest         | Inde du Sud        | Est des États-Unis – US DoD        |
|Est des États-Unis 2    | Est de l'Asie        |  Inde occidentale        | Centre des États-Unis – US DoD        |
|Ouest des États-Unis 2     | Asie du Sud-Est        | Centre du Canada        | Chine orientale         |
|Centre des États-Unis     | Est de l’Australie        | Est du Canada        | Chine du Nord        |
|Centre-Nord des États-Unis     |  Sud-est de l’Australie       | Sud du Brésil        | Sud du Royaume-Uni        |
|États-Unis - partie centrale méridionale     | Ouest du Japon        |Centre de la Corée         | Centre de l’Allemagne        |
|Centre-Ouest des États-Unis     |  Est du Japon       | Gouvernement américain - Virginie        | Nord-Est de l’Allemagne        |


## <a name="security-considerations"></a>Considérations relatives à la sécurité

Les données contenues dans le disque doivent être chiffrées à l’aide du chiffrement de lecteur BitLocker. Ce chiffrement permet de protéger vos données en transit.

Pour les travaux d’importation, les lecteurs sont chiffrés de deux manières.  


- Spécifiez l’option lors de l’utilisation du fichier *dataset.csv* pendant l’exécution de l’outil WAImportExport pour préparer le disque. 

- Activez le chiffrement BitLocker manuellement sur le disque. Spécifiez la clé de chiffrement dans le fichier *driveset.csv* lors de l’exécution de la ligne de commande de l’outil WAImportExport pendant la préparation du disque.


Pour les travaux d’exportation, une fois vos données copiées sur le disque, le service chiffre le disque à l’aide de BitLocker avant de vous le renvoyer. La clé de chiffrement vous est fournie par le biais du Portail Azure.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../../includes/storage-import-export-delete-personal-info.md)]


### <a name="pricing"></a>Tarifs

**Frais de manipulation de disque**

Des frais de manipulation sont appliqués pour chaque disque traité dans le cadre de votre travail d’importation/exportation. Pour plus d’informations, consultez [Tarification d’Azure Import/Export](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Frais d’expédition**

Lorsque vous envoyez des disques à Azure, vous payez le coût d’expédition au transporteur. Lorsque Microsoft vous renvoie les disques, les frais d’expédition sont facturés au compte de transporteur que vous avez spécifié lors de la création du travail.

**Frais de transaction**

L’importation de données dans le stockage Azure n’entraîne pas de frais de transaction supplémentaires par rapport aux frais de transaction du stockage standard. Des frais de sortie standard s’appliquent quand les données sont exportées à partir de Stockage Blob. Pour plus d’informations sur les frais de transaction, consultez [Tarification - Transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/).



## <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser le service Azure Import/Export pour :
* [Importer des données dans des objets blob Azure](storage-import-export-data-to-blobs.md)
* [Exporter des données à partir d’objets blob Azure](storage-import-export-data-from-blobs.md)
* [Importer des données dans des fichiers Azure Files](storage-import-export-data-to-files.md)

