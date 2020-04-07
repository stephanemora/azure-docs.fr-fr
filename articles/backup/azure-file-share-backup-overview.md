---
title: À propos de la sauvegarde des partages de fichiers Azure
description: Découvrir comment sauvegarder des partages de fichiers Azure dans le coffre Recovery Services
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: fd5bb51b2c7b5c09e9d859b69c3094eb50c205b5
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396282"
---
# <a name="about-azure-file-share-backup"></a>À propos de la sauvegarde des partages de fichiers Azure

La sauvegarde des partages de fichiers Azure est une solution de sauvegarde cloud native qui protège vos données dans le cloud et élimine les frais de maintenance supplémentaires impliqués dans les solutions de sauvegarde locales. Le service Sauvegarde Azure s’intègre parfaitement à Azure File Sync et vous permet de centraliser vos données de partage de fichiers, ainsi que vos sauvegardes. Cette solution simple, fiable et sécurisée vous permet de configurer la protection de vos partages de fichiers d’entreprise en quelques étapes simples et vous garantit de pouvoir récupérer vos données en cas de sinistre.

## <a name="key-benefits-of-azure-file-share-backup"></a>Principaux avantages de la sauvegarde des fichiers Azure

* Aucune infrastructure : Aucun déploiement n’est requis pour configurer la protection de vos partages de fichiers.
* Fonctionnalités de gestion intégrées : Vous pouvez planifier des sauvegardes et spécifier la période de rétention souhaitée sans surcharge de nettoyage des données.
* Restauration instantanée : La sauvegarde des partages de fichiers Azure utilise des instantanés de partage de fichiers, ce qui vous permet de sélectionner uniquement les fichiers que vous souhaitez restaurer instantanément.
* Alertes et rapports : Vous pouvez configurer des alertes pour les échecs de sauvegarde et de restauration et utiliser la solution de création de rapports fournie par Sauvegarde Azure pour obtenir des informations sur les sauvegardes relatives à vos partages de fichiers.

## <a name="architecture"></a>Architecture

![Architecture de sauvegarde des partages de fichiers Azure](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Fonctionnement du processus de sauvegarde

1. La première étape de configuration de la sauvegarde des partages de fichiers Azure consiste à créer un coffre Recovery Services. Ce coffre vous offre une vue consolidée des sauvegardes configurées sur différentes charges de travail.

2. Après avoir créé un coffre, le service Sauvegarde Azure découvre les comptes de stockage susceptibles d'être inscrits auprès du coffre. Vous pouvez sélectionner le compte de stockage hébergeant les partages de fichiers que vous souhaitez protéger.

3. Une fois le compte de stockage sélectionné, le service Sauvegarde Azure répertorie l’ensemble des partages de fichiers présents dans le compte de stockage et stocke leurs noms dans le catalogue de couches de gestion.

4. Vous configurez ensuite la stratégie de sauvegarde (planification et rétention) en fonction de vos besoins, puis vous sélectionnez les partages de fichiers à sauvegarder. Le service Sauvegarde Azure enregistre les planifications dans le plan de contrôle pour effectuer des sauvegardes planifiées.

5. En fonction de la stratégie spécifiée, le planificateur Sauvegarde Azure déclenche des sauvegardes à l’heure planifiée. Dans le cadre de ce travail, l’instantané de partage de fichiers est créé à l’aide de l’API Partage de fichiers. Seule l’URL d’instantané est stockée dans le magasin de métadonnées.

    >[!NOTE]
    >Les données de partage de fichiers ne sont pas transférées vers le service de sauvegarde, car ce dernier crée et gère les captures instantanées qui font partie de votre compte de stockage.

6. Vous pouvez restaurer le contenu du partage de fichiers Azure (fichiers individuels ou partage complet) à partir des captures instantanées disponibles sur le partage de fichiers source. Une fois l’opération déclenchée, l’URL de capture instantanée est récupérée à partir du magasin de métadonnées et les données sont listées et transférées de l’instantané source vers le partage de fichiers cible de votre choix.

7. Les données de surveillance des travaux de sauvegarde et de restauration sont envoyées au service Azure Backup Monitoring. Cela vous permet de surveiller les sauvegardes cloud de vos partages de fichiers dans un tableau de bord unique. En outre, vous pouvez également configurer des alertes ou des notifications par e-mail lorsque l’intégrité de la sauvegarde est affectée. Les e-mails sont envoyés par le biais du service de messagerie Azure.

## <a name="backup-costs"></a>Coûts de sauvegarde

La sauvegarde du partage de fichiers Azure est une solution basée sur des instantanés. Les frais de stockage inhérents sont facturés avec l’utilisation d’Azure Files selon la grille tarifaire présentée [ici](https://azure.microsoft.com/pricing/details/storage/files/).

Toutefois, les frais de l’instance protégée pour tirer parti de la solution de sauvegarde suivent le modèle de tarification décrit dans la section [Sauvegarde pour Azure Files](https://azure.microsoft.com/pricing/details/backup/). Le prix réel n’a été mis à jour que pour la zone USA Centre-Ouest pour le moment. Pour les autres régions, les prix exacts seront mis à jour prochainement avec des variations régionales, mais sur le même modèle de tarification.

>[!NOTE]
>Pendant la préversion, il n’y a pas de « frais d’instance protégée ». Seuls les instantanés sont facturés selon la tarification indiquée [ici](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Étapes suivantes

* Découvrir comment [sauvegarder des partages de fichiers Azure](backup-afs.md)
* Trouver des réponses aux [questions sur la sauvegarde Azure Files](backup-azure-files-faq.md)
