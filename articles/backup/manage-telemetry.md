---
title: Gérer les paramètres de télémétrie dans le serveur de sauvegarde Microsoft Azure (MABS)
description: Cet article explique comment gérer les paramètres de télémétrie dans MABS.
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: 353aec2b3bf03c7ce06d19db34a6b81f91d2ee12
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524022"
---
# <a name="manage-telemetry-settings"></a>Gérer les paramètres de télémétrie

>[!NOTE]
>Cette fonctionnalité s’applique à MABS v3 UR2 et version ultérieure.

Cet article explique comment gérer les paramètres de télémétrie (données de diagnostic et utilitaires) dans le serveur de sauvegarde Microsoft Azure (MABS).

Par défaut, MABS envoie des données de diagnostic et de connectivité à Microsoft. Microsoft les utilise pour assurer et améliorer la qualité, la sécurité et l’intégrité de ses produits et services.

Les administrateurs peuvent désactiver cette fonctionnalité à tout moment. Pour plus d'informations sur les données collectées, consultez la [ section suivante](#telemetry-data-collected).

## <a name="turn-onoff-telemetry-from-console"></a>Activer/désactiver la télémétrie avec la console

1. Dans la console du serveur de sauvegarde Microsoft Azure, allez à **Gestion** et cliquez sur **Options** dans le volet supérieur.
1. Dans la boîte de dialogue **Options**, sélectionnez **Paramètres des données de diagnostic et d’utilisation**.

    ![options de télémétrie de la console](./media/telemetry/telemetry-options.png)

1. Sélectionnez votre préférence en matière de partage des données de diagnostic et d’utilisation parmi les options proposées, puis cliquez sur **OK**.

    >[!NOTE]
    >Nous vous conseillons de lire la [Déclaration de confidentialité](https://privacy.microsoft.com/privacystatement) avant de faire votre choix.
    >- Pour activer la télémétrie, sélectionnez **Oui, j’accepte d’envoyer des données à Microsoft**.
    >- Pour désactiver la télémétrie, sélectionnez **Non, je préfère ne pas envoyer des données à Microsoft**.

## <a name="telemetry-data-collected"></a>Données de télémétrie collectées

| Données relatives à | Données collectées* |
| --- | --- |
| **Paramétrage** | Version de MABS installé. <br/><br/>Version du cumul des mises à jour MABS installé. <br/><br/> Identifiant unique d’ordinateur. <br/><br/> Système d’exploitation sur lequel MABS est installé. <br/><br/> Identificateur d’abonnement cloud unique.<br/><br/> Version de l'agent MARS.<br/><br/> Indique si le stockage hiérarchisé est activé. <br/><br/> Taille de stockage utilisée. |
| **Charge de travail protégée** | Identificateur unique de la charge de travail. <br/><br/>Taille de la charge de travail en cours de sauvegarde. <br/><br/>Type de charge de travail et son numéro de version. <br/><br/>Si la charge de travail est actuellement protégée par MABS. <br/><br/>Identificateur unique du groupe de protection sous lequel la charge de travail est protégée.<br/><br/> Emplacement où la charge de travail est en cours de sauvegarde sur disque/bande ou cloud.|
| **Tâches** | Statut du travail de sauvegarde/restauration. <br/><br/> Taille des données sauvegardées/restaurées. <br/><br/>Message d’échec, en cas d’échec du travail de sauvegarde/restauration.<br/><br/> Durée nécessaire à l’exécution du travail de restauration.<br/><br/>Détails de la charge de travail pour laquelle le travail de sauvegarde/restauration a été exécuté. |
| **Changement d’état de la télémétrie** | Détails du changement d’état des paramètres de télémétrie, s’ils sont activés ou désactivés, et à quel moment. |
| **Erreur d’incident de la console MABS** | Détails de l’erreur lorsqu’une console MABS rencontre un incident.|

## <a name="next-steps"></a>Étapes suivantes

[Protection des charges de travail](./back-up-hyper-v-virtual-machines-mabs.md)