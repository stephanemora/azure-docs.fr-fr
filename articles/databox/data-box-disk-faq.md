---
title: FAQ Microsoft Azure Data Box Disk | Microsoft Docs sur les données
description: Contient des questions fréquemment posées et des réponses au sujet d’Azure Data Box Disk, une solution de cloud qui vous permet de transférer de grandes quantités de données dans Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/04/2018
ms.author: alkohli
ms.openlocfilehash: 26f8f59d940c1e4a25e24229c2a3df75052e63e4
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43783284"
---
# <a name="what-is-azure-data-box-disk-preview"></a>Qu’est-ce qu’Azure Data Box Disk ? (Préversion)

La solution de cloud Microsoft Azure Data Box Disk vous permet d’envoyer des téraoctets de données vers Azure de façon rapide, économique et fiable. Cette FAQ contient des réponses aux questions que vous pouvez vous poser lorsque vous utilisez Data Box Disk sur le portail Azure. 

Les questions/réponses sont classées dans les catégories suivantes :

- À propos du service
- Configuration et connexion 
- Suivi de l'état
- Migration des données 
- Vérification et chargement de données 

> [!IMPORTANT]
> Data Box Disk est disponible en préversion. Veuillez lire les [conditions d’utilisation de la version préliminaire Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) avant de déployer cette solution.

## <a name="about-the-service"></a>À propos du service

### <a name="q-what-is-azure-data-box-service"></a>Q. Qu’est-ce que le service Azure Data Box ? 
R.  Le service Azure Data Box a été conçu pour l’ingestion des données hors connexion. Ce service gère une gamme de produits tous conçus pour le transport des données pour différentes capacités de stockage. 

### <a name="q-what-are-azure-data-box-disks"></a>Q. Que sont les disques Azure Data Box ?
R. Les disques Azure Data Box permettent un transfert rapide, économique et sécurisé de plusieurs téraoctets de données sur et depuis Azure. Microsoft vous envoie entre 1 et 5 disques d’une capacité de stockage maximale de 35 To. Vous pouvez facilement configurer, connecter et déverrouiller ces disques via le service Data Box du portail Azure.  

Les disques sont chiffrés à l’aide du chiffrement de lecteur BitLocker et vos clés de chiffrement sont gérées sur le portail Azure. Ensuite, vous copiez les données à partir des serveurs du client. Dans le centre de données, Microsoft migre vos données depuis le disque vers le cloud en utilisant une liaison montante rapide de réseau privé, puis les charge dans Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>Q. Quand dois-je utiliser des disques Data Box ?
R. Si vous souhaitez transférer vers Azure 40 To de données (maximum), les disques Data Box pourront vous être utiles.

### <a name="q-what-is-the-price-of-data-box-disks"></a>Q. Combien coûtent les disques Data Box ?
R. Pendant la période de préversion, les disques Data Box sont disponibles gratuitement. L’expédition est également gratuite, toutefois, les frais de stockage Azure s’appliquent.

### <a name="q-how-do-i-get-data-box-disks"></a>Q. Comment faire pour obtenir des disques Data Box ? 
R.  Pour obtenir des disques Azure Data Box, commencez par vous inscrire à la [préversion de Data Box Disk](http://aka.ms/AzureDataBox). Ensuite, connectez-vous au portail Azure et créez une commande de disques Data Box. Indiquez vos coordonnées et vos paramètres de notification. Une fois que vous avez passé une commande, les disques sont expédiés dans les 10 jours, en fonction de leur disponibilité.   

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>Q. Quelle est la quantité maximale de données que je peux transférer avec les disques Data Box dans une seule instance ?
R. Pour 5 disques de 8 To chacun (7 To de capacité utilisable), la capacité utilisable maximale est de 35 To. Par conséquent, vous pouvez transférer 35 To de données dans une seule instance.  Pour transférer davantage de données, vous devez commander davantage de disques.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>Q. Comment puis-je vérifier si les disques Data Box sont disponibles dans ma région ? 
R.  Les disques Data Box sont disponibles aux États-Unis, au Canada, en Australie et dans tous les pays de l’Union européenne pendant la phase de préversion.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>Q. Dans quelles régions puis-je stocker des données avec des disques Data Box ?
R. La solution Data Box Disk est prise en charge dans toutes les régions des États-Unis, du Canada, d’Australie, de l’Europe Ouest et de l’Europe Nord pour la préversion. Seules les régions du cloud public Azure sont prises en charge. Azure Government et les autres clouds souverains ne sont pas pris en charge.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>Q. Qui dois-je contacter si je rencontre des problèmes avec des disques Data Box ?
R. Si vous rencontrez des problèmes avec des disques Data Box, veuillez contacter le [support Data Box Disk](mailto:expresspodsupport@microsoft.com).

## <a name="configure-and-connect"></a>Configuration et connexion
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>Q. Puis-je spécifier le nombre de disques Data Box dans ma commande ?
R.  Non. Vous recevez des disques de 8 To (5 disques au maximum) en fonction du volume de vos données et de la disponibilité des disques.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>Q. Comment faire pour déverrouiller les disques Data Box ? 
R.  Dans le portail Azure, accédez à votre commande Data Box Disk et accédez à **Informations sur l’appareil**. Copiez la clé d’accès. Téléchargez et extrayez l’outil de déverrouillage Data Box Disk depuis le portail Azure, puis exécutez *DataBoxDiskUnlock.exe* sur l’ordinateur qui contient les données que vous voulez copier sur les disques. Entrez la clé d'accès pour déverrouiller vos disques. La même clé d’accès déverrouille tous les disques.

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>Q. Puis-je utiliser un ordinateur hôte Linux pour me connecter et copier les données sur les disques Data Box ?
R.  Non. Seuls les ordinateurs Windows sont pris en charge. Pour plus d’informations, accédez à la liste des [systèmes d’exploitation pris en charge](data-box-disk-system-requirements.md) pour votre ordinateur hôte.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. Mes disques ont été expédiés, mais je souhaite quand même annuler ma commande. Pourquoi le bouton Annuler n’est-il pas disponible ?
R.  Vous pouvez annuler une commande passée, mais avant l’expédition des disques uniquement. Une fois que les disques ont été expédiés, vous ne pouvez plus annuler la commande. Dans la phase de préversion, vous pouvez retourner vos disques sans frais, bien que cela changera probablement la date de mise à disposition générale de la solution. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>Q. Puis-je connecter plusieurs disques Data Box à mon ordinateur hôte simultanément pour transférer des données ?
R. Oui. Vous pouvez connecter plusieurs disques Data Box au même ordinateur hôte pour transférer des données et plusieurs travaux de copie peuvent être exécutés en parallèle.

## <a name="track-status"></a>Suivi de l'état

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>Q. Comment faire pour suivre les disques depuis leur commande à leur renvoi ? 
R.  Vous pouvez suivre l’état de la commande de disques Data Box dans le portail Azure. Lorsque vous créez la commande, vous êtes également invité à fournir une adresse e-mail pour recevoir des notifications. Si vous en avez fourni une, vous serez averti par e-mail de toutes les modifications d’état de la commande. Plus d’informations sur la manière de [configurer les notifications par e-mail](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>Q. Comment faire pour renvoyer les disques ? 
R.  Microsoft inclut une étiquette d’expédition dans le colis contenant les disques Data Box. Collez l’étiquette sur la boîte d’envoi et déposez le paquet scellé chez votre livreur. Si vous avez endommagé ou perdu l’étiquette, vous pouvez accéder à **Vue d’ensemble > Télécharger une étiquette d'expédition** et télécharger une nouvelle étiquette d’expédition.

## <a name="migrate-data"></a>Migration des données

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>Q. Quel volume de données puis-je utiliser au maximum avec des disques Data Box ?  
R.  La solution de disques Data Box peut inclure jusqu'à 5 disques avec une capacité utilisable maximale de 35 To. Les disques eux-mêmes font 8 To (7 To utilisables). 

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>Q. Quelle est la taille maximale des objets blob de blocs et des objets blob de pages prise en charge par chaque disque Data Box ? 
R.  Les tailles maximales dépendent des limites du Stockage Azure. La taille maximale pour un objet blob de blocs est d’environ 4,768 Tio et la taille maximale pour un objet blob de page est de 8 Tio. Pour plus d’informations, consultez la page [Objectifs de performance et évolutivité d'Azure Storage](../storage/common/storage-scalability-targets.md). 

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>Q. Quelle est la vitesse de transfert des données des disques Data Box ?
R. Lors de tests utilisant des disques connectés via USB 3.0, les performances des disques ont atteint 430 Mo/s. Les chiffres réels varient en fonction de la taille de fichier utilisée. Pour les fichiers plus petits, vous pouvez constater des performances moins élevées.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. Comment faire pour savoir si mes données sont sécurisées pendant le transfert ? 
R.  Les disques Data Box sont chiffrés à l’aide du chiffrement BitLocker AES-128 bits et la clé d’accès est uniquement disponible dans le portail Azure. Connectez-vous au portail Azure à l’aide de vos informations d’identification pour obtenir la clé d'accès. Vous devez fournir cette clé d’accès lorsque vous exécutez l’outil de déverrouillage Data Box Disk.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>Q. Comment faire pour copier des données sur les disques Data Box ? 
R.  Utilisez un outil de copie SMB tel que Robocopy, Diskboss ou même la fonction glisser-déplacer de l’explorateur de fichiers Windows pour copier des données sur les disques. 

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. Y a-t-il des astuces permettant d’accélérer la copie des données ?
R.  Pour accélérer le processus de copie :

- Utilisez plusieurs flux de copie des données. Par exemple, avec Robocopy, utilisez l’option multithread. Pour plus d’informations sur la commande exacte à utiliser, accédez à [Tutorial: Copy data to Azure Data Box Disk and verify](data-box-disk-deploy-copy-data.md#copy-data-to-disks) (Didacticiel : copier des données sur Azure Data Box Disk et vérifier).
- Utilisez plusieurs sessions.
- Au lieu de copier sur un partage réseau (où vous pouvez être limité par la vitesse du réseau), vérifiez que les données résident en local sur l’ordinateur auquel les disques sont connectés.
- Vérifiez que vous utilisez l’USB 3.0 ou une version ultérieure tout au long du processus de copie. Téléchargez et utilisez l’[outil USBView](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview) pour identifier les contrôleurs USB et les périphériques USB connectés à l’ordinateur.
- Évaluez les performances de l’ordinateur utilisé pour copier les données. Téléchargez et utilisez l’[outil Bluestop FIO](https://bluestop.org/fio/) pour tester les performances du matériel du serveur.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>Q. Comment accélérer les données si la source de données contient des petits fichiers (quelques Ko ou Mo seulement) ?
R.  Pour accélérer le processus de copie :

- Créez un VHDx local sur un stockage rapide ou créez un disque dur virtuel vide sur le HDD/SSD (plus lent).
- Montez-le sur une machine virtuelle.
- Copiez les fichiers sur le disque de la machine virtuelle.


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>Q. Puis-je utiliser plusieurs comptes de stockage avec les disques Data Box ?
R.  Non. Pour l’instant, seul un compte de stockage, général ou classique, est pris en charge par les disques Data Box. Les objets blob chauds et froids sont pris en charge. Pendant la période de préversion, seuls les comptes de stockage aux États-Unis, en Europe de l’Ouest et en Europe du Nord dans le cloud public Azure sont pris en charge.

## <a name="verify-and-upload"></a>Vérification et chargement

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>Q. Après combien de temps puis-je accéder à mes données dans Azure une fois que j’ai renvoyé les disques ? 
R.  Dès que l’état de la commande pour la copie des données s’affiche comme complet, vous devriez pouvoir accéder à vos données.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. Où se trouvent mes données dans Azure après leur téléchargement ?
R.  Lorsque vous copiez des données dans les dossiers *BlockBlob* et *PageBlob* sur votre disque, un conteneur est créé sur le compte de stockage Azure pour chaque sous-dossier situé dans les dossiers *BlockBlob* et *PageBlob*. Si vous avez copié les fichiers dans les dossiers *BlockBlob* et *PageBlob* directement, ils se trouvent dans un conteneur *$root* par défaut sur le compte de stockage Azure. 

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. Je viens de remarquer que je n’ai pas suivi les conventions de dénomination pour mes conteneurs Azure. Le chargement de mes données sur Azure risque-t-il d’échouer ?
R. Si les noms des conteneurs contiennent des lettres majuscules, celles-ci sont automatiquement converties en minuscules. Si les noms ne sont pas conformes d’une autre manière (caractères spéciaux, autres langues, etc.), le chargement échouera.

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>Q. Comment faire pour vérifier les données que j’ai copiées sur plusieurs disques Data Box ?
R.  Une fois la copie des données terminée, vous pouvez exécuter `AzureExpressDiskService.cmd` disponible dans le dossier *AzureImportExport* pour générer des sommes de contrôle pour la validation. Si vous avez plusieurs disques, vous devez ouvrir une fenêtre de commande par disque avant d’exécuter cette commande. N’oubliez pas que cette opération peut être longue (plusieurs heures) en fonction de la taille de vos données.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>Q. Que deviennent mes données une fois que j’ai renvoyé les disques ?
R.  Une fois la copie des données vers Azure terminée, les données des disques sont effacées en toute sécurité, conformément aux instructions du standard NIST SP 800-88 Revision 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. Comment mes données sont-elles protégées pendant le transfert ? 
R.  Les disques Data Box sont chiffrés à l’aide du chiffrement AES-128 Microsoft BitLocker. Vous devez utiliser une clé d’accès unique pour déverrouiller tous les disques et accéder aux données.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>Q. Dois-je réexécuter la validation des sommes de contrôle si j’ajoute d’autres données aux disques Data Box ?
R. Oui. Si vous décidez de valider vos données (et nous vous recommandons de le faire), vous devez réexécuter la validation si vous avez ajouté de nouvelles données sur les disques.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>Q. J’ai utilisé tous mes disques pour transférer des données et j’ai besoin de commander davantage de disques. Existe-t-il un moyen de passer rapidement une commande ?
R. Vous pouvez cloner votre commande précédente. Ainsi, vous créez une commande identique à la précédente et vous pouvez en modifier les détails sans avoir à entrer votre adresse, vos informations de contact et vos préférences de notification. 



## <a name="next-steps"></a>Étapes suivantes

- Relire les [Conditions requises pour le système Data Box](data-box-disk-system-requirements.md).
- Comprendre les limites de [Data Box](data-box-disk-limits.md).
- Déployer rapidement [Azure Data Box Disk](data-box-disk-quickstart-portal.md) sur le portail Azure.
