---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/07/2021
ms.author: alkohli
ms.openlocfilehash: 6ccf37d96da5b52111fd08a51de4949185891193
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109736119"
---
Pour passer en revue les erreurs non renouvelables et poursuivre le traitement de votre commande, procédez comme suit :

1. Ouvrez votre commande dans le portail Azure.  

   Si des erreurs non renouvelables ont empêché le chargement des fichiers, la notification suivante s’affiche. L’état actuel de la commande sera **La copie des données s’est terminée avec des erreurs. L’appareil est en attente d’effacement des données.**

   ![Notification des erreurs de copie lors d’un chargement dans le portail Azure](media/data-box-review-nonretryable-errors/copy-errors-in-upload-01.png)

   Notez le **CHEMIN DU JOURNAL DE COPIE** dans **DÉTAILS DE LA COPIE DES DONNÉES**. Vous allez passer en revue les erreurs dans le journal de copie des données.

2. Sélectionnez **Confirmer l’effacement de l’appareil** pour ouvrir un volet de vérification.

   ![Volet de vérification et de traitement des erreurs de chargement dans le portail Azure](media/data-box-review-nonretryable-errors/copy-errors-in-upload-02.png)

3. Passez en revue les erreurs dans le journal de copie des données en utilisant le chemin du journal de copie que vous avez noté précédemment. Si nécessaire, vous pouvez sélectionner **Fermer** pour afficher à nouveau ce chemin. 

   Vous devez corriger les problèmes de configuration avant d’essayer un autre chargement via un transfert réseau ou une nouvelle commande d’importation. <!--For guidance, see [Review copy errors in uploads from Azure Data Box and Azure Data Box Heavy devices](../articles/databox/data-box-troubleshoot-data-upload.md). - To make the Include, I needed to move this reference out of the main procedure.-->

4. Après avoir passé en revue les erreurs, cochez la case pour confirmer que vous êtes prêt à poursuivre l’effacement des données. Ensuite, sélectionnez **Continuer**.

   ![Confirmez que vous êtes prêt à poursuivre l’effacement des données](media/data-box-review-nonretryable-errors/copy-errors-in-upload-03.png)

   Une fois les données effacées en toute sécurité de l’appareil, l’état de la commande est mis à jour sur **La copie s’est terminée avec des erreurs**.

   ![Affichage de l’état d’une commande d’importation Data Box qui s’est terminée avec des erreurs](media/data-box-review-nonretryable-errors/copy-errors-in-upload-04.png)

   Si vous n’entreprenez aucune action, la commande se termine automatiquement après 14 jours.

