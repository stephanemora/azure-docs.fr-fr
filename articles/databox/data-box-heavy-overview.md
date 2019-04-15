---
title: Présentation de Microsoft Azure Data Box Heavy | Microsoft Docs sur les données
description: Description d’Azure Data Box, une solution hybride qui vous permet de transférer des volumes importants de données dans Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 0a5b7f93f9ac6cc5b1076881727a42fd5b95ff4b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235154"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>Qu’est-ce qu’Azure Data Box Heavy ? (Préversion)

La solution hybride Microsoft Azure Data Box permet d’envoyer des centaines de téraoctets de données vers Azure de façon rapide, économique et fiable. Le transfert de données sécurisé est accéléré avec l’appareil de stockage propriétaire d’une capacité d’1 Po qui vous est expédié via un transporteur. L’appareil dispose d’un caisson robuste afin de protéger et de sécuriser les données au cours du transport.

Data Box Heavy est actuellement en préversion, et vous pouvez vous inscrire pour demander un appareil via le portail Azure. Une fois que l’appareil est reçu dans votre centre de données, vous pouvez le configurer à l’aide de l’interface utilisateur web locale. Copiez les données à partir de vos serveurs vers l’appareil et réexpédiez l’appareil à Azure. Dans le centre de données Azure, vos données sont automatiquement chargées de l’appareil vers Azure. L’ensemble du processus est suivi de bout en bout par le service Data Box dans le portail Azure.


> [!IMPORTANT]
> - Data Box Heavy est disponible en préversion. Veuillez lire les [conditions d’utilisation de la préversion Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de déployer cette solution. 
> - Pour demander un appareil, inscrivez-vous dans le [Portail en version préliminaire](https://aka.ms/).
> - Pendant la période de préversion, Data Box Heavy peut être envoyé aux clients des États-Unis et de l’Union européenne. Pour plus d’informations, consultez [Disponibilité des régions](#region-availability).

## <a name="use-cases"></a>Cas d'utilisation

Data Box Heavy est parfaitement adapté au transfert de tailles de données supérieures à 500 To dans les scénarios où la connectivité réseau est limitée ou nulle. Il peut s’agir d’un déplacement de données à usage unique, périodique, ou d’un transfert de données en bloc initial suivi de transferts périodiques. Voici les différents scénarios où Data Box Heavy peut être utilisé pour le transfert de données.

 - **Migration unique** - lorsque de grandes quantités de données locales sont transférées vers Azure. 
     - Déplacement d’une bibliothèque multimédia à partir de bandes hors connexion dans Azure pour créer une bibliothèque multimédia en ligne.
     - Migration de votre batterie de machines virtuelles, de SQL Server et d’applications vers Azure
     - Déplacement des données historiques vers Azure pour une analyse et un compte rendu approfondis à l’aide de HDInsight

 - **Transfert en bloc initial** : lorsqu’un transfert en bloc initial est effectué à l’aide de Data Box Heavy (seed) suivi de transferts incrémentiels sur le réseau. 
     - Par exemple, les partenaires de solutions de sauvegarde tels que Commvault et Data Box Heavy sont utilisés pour déplacer d’importantes sauvegardes initiales de données historiques vers Azure. Une fois l’opération terminée, les données incrémentielles sont transférées via le réseau vers le stockage Azure.

 - **Chargements périodiques** - lorsqu’une grande quantité de données est générée régulièrement et doit être déplacée vers Azure. Par exemple, dans le secteur de la production d’énergie, où du contenu vidéo est généré sur des plateformes pétrolières et dans des parcs éoliens.      

## <a name="benefits"></a>Avantages

Data Box Heavy est conçu pour déplacer d’importantes quantités de données vers Azure avec un impact moindre ou sans aucun impact sur le réseau. La solution offre les avantages suivants :

- **Vitesse** : Data Box Heavy utilise des interfaces réseau hautes performances de 40 Gbit/s.

- **Sécurité** : Data Box Heavy intègre des protections de sécurité pour l’appareil, les données et le service.
    - L’appareil dispose d’un caisson robuste sécurisé par des vis et des autocollants inviolables. 
    - Les données sur l’appareil sont sécurisées en permanence à l’aide d’un chiffrement AES 256 bits.
    - L’appareil peut uniquement être déverrouillé à l’aide d’un mot de passe fourni dans le portail Azure.
    - Le service est protégé par les fonctionnalités de sécurité Azure.
    - Une fois que vos données sont chargées dans Azure, les disques sur l’appareil sont nettoyés, conformément aux normes NIST 800-88r1.


<!--## Features and specifications

The Data Box Heavy device has the following features in this release.

| Specifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | < 50 lbs.                |
| Dimensions                                              | Device - Width: 309.0 mm Height: 430.4 mm Depth: 502.0 mm |            
| Rack space                                              | 7 U when placed in the rack on its side (cannot be rack-mounted)|
| Cables required                                         | 1 X power cable (included) <br> 2 RJ45 cables <br> 2 X SFP+ Twinax copper cables|
| Storage capacity                                        | 100 TB <br> 80 TB usable capacity after RAID 5 protection|
| Network interfaces                                      | 2 X 1 GbE interface - MGMT, DATA 3. <br> MGMT - for management, not user configurable, used for initial setup <br> DATA3 - for data, user configurable, and is dynamic by default <br> MGMT and DATA 3 can also work as 10 GbE <br> 2 X 10 GbE interface - DATA 1, DATA 2 <br> Both are for data, can be configured as dynamic (default) or static |
| Data transfer media                                     | RJ45, SFP+ copper 10 GbE Ethernet  |
| Security                                                | Rugged device casing with tamper-proof custom screws <br> Tamper-evident stickers placed at the bottom of the device|
| Data transfer rate                                      | Up to 80 TB in a day over 10 GbE network interface        |
| Management                                              | Local web UI - one-time initial setup and configuration <br> Azure portal - day-to-day device management        |-->

## <a name="components"></a>Composants

Data Box Heavy inclut les composants suivants :

* **Appareil Data Box Heavy** : appareil physique doté d’une enveloppe extérieure robuste qui stocke les données en toute sécurité. Cet appareil a une capacité de stockage utilisable de 800 To. 

    
* **Service Data Box** : extension du portail Azure qui vous permet de gérer un appareil Data Box Heavy à partir d’une interface web accessible depuis divers emplacements géographiques. Utilisez le service Data Box pour effectuer l’administration quotidienne de votre appareil Data Box Heavy. Les tâches de service incluent la création et la gestion de commandes, l’affichage et la gestion des alertes ainsi que la gestion des partages.  

* **Interface utilisateur web locale** : interface utilisateur web qui est utilisée pour configurer l’appareil afin qu’il puisse se connecter au réseau local, puis pour inscrire l’appareil auprès du service Data Box. Utilisez l’interface utilisateur web locale pour arrêter et redémarrer l’appareil, afficher les journaux d’activité de copie et contacter le Support Microsoft pour créer une demande de service.


## <a name="the-workflow"></a>Workflow

Un flux type inclut les étapes suivantes :

1. **Commande** - Créez une commande dans le portail Azure et indiquez les informations d’expédition et le compte de stockage de destination Azure de vos données. Si l’appareil est disponible, Azure le prépare et l’expédie avec un ID de suivi d’expédition.

2. **Réception** : une fois l’appareil livré, connectez-le au réseau et à l’alimentation à l’aide des câbles spécifiés. Mettez l’appareil sous tension et connectez-vous à l’appareil. Configurez le réseau de l’appareil et montez les partages sur l’ordinateur hôte à l’emplacement à partir duquel vous souhaitez copier les données.

3. **Copie des données** : copiez les données vers les partages Data Box Heavy.

4. **Retour** : préparez l’appareil, mettez-le hors tension et retournez l’appareil au centre de données Azure.

5. **Chargement** : les données sont automatiquement copiées de l’appareil vers Azure. Les disques de l’appareil sont effacés en toute sécurité conformément aux instructions du National Institute of Standards and Technology (NIST).

Tout au long de ce processus, vous êtes averti par courrier électronique de toutes les modifications d’état. 

## <a name="region-availability"></a>Disponibilité des régions

Data Box Heavy peut transférer des données en fonction de la région dans laquelle le service est déployé, du pays dans lequel l’appareil est livré et du compte de stockage Azure cible vers lequel vous transférez les données. 

- **Disponibilité du service** : pour cette version, Data Box Heavy est disponible dans les régions suivantes :
    - Toutes les régions de cloud public aux États-Unis : USA Centre-Ouest, USA Ouest 2, USA Ouest, USA Centre Sud, USA Centre, USA Centre Nord, USA Est et USA Est 2.
    - Union européenne : Europe Ouest et Europe Nord.
    - Royaume-Uni : Royaume-Uni Sud et Royaume-Uni Ouest.
    - France : France Centre et France Sud.

- **Comptes de stockage de destination** : les comptes de stockage qui stockent les données sont disponibles dans toutes les régions Azure où le service est disponible. 

## <a name="sign-up"></a>Inscription

Data Box Heavy est en préversion et vous devez vous inscrire. Procédez comme suit pour vous inscrire à Data Box Heavy :

1. Connectez-vous au portail Azure à l’adresse : https://aka.ms/azuredatabox.
2. Cliquez sur **+** pour créer une nouvelle ressource. Recherchez **Azure Data Box**. Sélectionnez le service **Azure Data Box**.

    <!--![The Data Box Heavy sign up 1]()-->

3. Cliquez sur **Créer**.

    <!--![The Data Box Heavy sign up 2]()-->

4. Sélectionnez l’abonnement que vous souhaitez utiliser pour la préversion de Data Box Heavy. Sélectionnez la région dans laquelle vous souhaitez déployer la ressource Data Box Heavy. Dans l’option **Data Box Heavy**, cliquez sur **S’inscrire**.

   <!--![The Data Box Heavy sign up 3]()-->

5. Répondez aux questions concernant le pays de résidence des données, le laps de temps, le service Azure cible pour le transfert des données, la bande passante réseau et la fréquence de transfert des données. Consultez le texte Confidentialité et conditions d'utilisation et sélectionnez la case à cocher autorisant Microsoft à utiliser votre adresse de messagerie pour vous contacter.

    <!--![The Data Box Heavy sign up 4]()-->

Une fois que vous êtes inscrit et autorisé à utiliser la préversion, vous pouvez commander un Data Box Heavy.




