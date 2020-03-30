---
title: Version d’évaluation de l’offre d’application Azure | Place de marché Azure
description: Comment configurer une version d’évaluation pour une offre d’application Azure sur la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 2f35dd69c1f29350049ed8f62dd9cbb0e60a7718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289003"
---
# <a name="azure-applications-test-drive-tab"></a>Onglet Version d’évaluation d’applications Azure

Utilisez l’onglet Version d’évaluation pour offrir une option d’évaluation à vos clients.

## <a name="test-drive-benefits"></a>Avantages de la version d’évaluation

Nous vous recommandons de proposer une option d’évaluation à vos clients pour leur permettre d'acheter votre application en toute confiance. Parmi les options d’évaluation disponibles, la version d’évaluation est la plus efficace pour générer des prospects pertinents et les transformer en clients.

La version d’évaluation permet aux clients d'essayer les principales fonctionnalités de votre produit et d'en apprécier les avantages grâce à un scénario d’implémentation réel.

## <a name="how-a-test-drive-works"></a>Fonctionnement d’une version d’évaluation

Un client potentiel accède à votre application après avoir lancé une recherche sur la Place de marché. Le client se connecte et accepte les conditions d’utilisation. À ce stade, le client reçoit votre environnement préconfiguré afin de l'essayer pendant un nombre d'heures défini, et vous disposez d'un prospect pertinent auquel vous pouvez donner suite. Pour plus d’informations, consultez la rubrique [Qu’est-ce qu’une version d’évaluation ?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Configuration d’une version d’évaluation

Utilisez les étapes suivantes pour activer et configurer une version d’évaluation.

### <a name="to-enable-a-test-drive"></a>Pour activer une version d’évaluation :

1. Sous **Nouvelle offre**, sélectionnez l’onglet **Version d’évaluation**.
2. Sous **Version d’évaluation**, sélectionnez **Oui** pour l’option **Activer une version d’évaluation**.

   ![Activer une version d’évaluation](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Pour configurer une version d’évaluation :

Après avoir activé une version d’évaluation, vous devez remplir les formulaires suivants pour la configurer :
  
 - Détails
 - Configuration technique
 - Détails d’abonnement du déploiement de la version d’évaluation

La capture d’écran suivante affiche tous les formulaires d’une version d’évaluation. Si un astérisque (*) se trouve en regard du nom du champ, cela signifie que ce champ est obligatoire. 

![Configurer une version d’évaluation](./media/managed-app-configure-testdrive.png)

Le tableau suivant décrit les champs requis pour configurer la version d’évaluation de votre application gérée.  Les champs marqués d’un astérisque sont obligatoires.

|      Champ         |  Description      |
|  ---------------   |  ---------------  |
| **Description\***  |  Décrivez ce qui peut être fait sur votre version d’évaluation. Vous pouvez utiliser des balises HTML de base pour mettre cette description en forme. Par exemple, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;et les en-têtes.                |
| **Manuel utilisateur\***  |  Téléchargez un manuel utilisateur que vos clients peuvent utiliser pour se guider au cours de l’expérience d’évaluation. Ce document doit être au format pdf.    |
| **Vidéo de démonstration de version d’évaluation** |  Vidéo de procédure pas à pas facultative de votre version d’évaluation. Un client peut regarder cette vidéo avant de se procurer une version d’évaluation. Fournissez une URL vers la vidéo sur YouTube ou Vimeo. Si vous sélectionnez **+ Ajouter une vidéo**, il vous sera demandé de fournir les informations suivantes :<ul><li>Name</li><li>URL</li><li>Miniature (au format PNG, 533 x 324 pixels)</li></ul>  |
| **Instances\***      | Configurez le nombre d’instances que vous désirez, leur(s) région(s), et la vitesse à laquelle vos clients peuvent obtenir la version d’évaluation. Pour plus d’informations, consultez [Publication d’une version d’évaluation](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Durée d’une version d’évaluation (heures)\*** | Entrez un nombre entier correspondant au nombre d’heures. La plage autorisée est comprise entre 1 et 999. |
| **Modèle ARM de version d’évaluation\***     | Téléchargez un fichier compressé (.zip) contenant vos modèles Azure Resource Manager pour votre application. Pour plus d’informations, consultez [Version d’évaluation d’Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Informations d’accès\***          | Fournissez les informations d’accès une fois que votre client a obtenu la version d’évaluation. Par exemple, une URL pour accéder à la version d’évaluation et les informations de connexion. . Vous pouvez utiliser des balises HTML de base pour mettre cette description en forme. Par exemple, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;et les en-têtes. |
| **ID d’abonnement Azure\***       | Ce paramètre accorde l’accès aux services Azure et au portail Azure. C’est dans l’abonnement que l’utilisation des ressources est signalée et que les services sont facturés. Si vous n’avez pas encore d’abonnement Azure distinct réservé aux versions d’évaluation, créez-en un.  |
| **ID de locataire Azure AD\***          | Fournissez un locataire existant dans Azure Active Directory ou créez un locataire pour cette version d’évaluation.  |
| **ID de l’application Azure AD\***             | Créez et inscrivez une nouvelle application. Microsoft utilise cette application pour effectuer des opérations sur l’instance de votre version d’évaluation.  |
| **Clé d’application Azure AD\***            | Créez une clé d’authentification pour l’application et collez-la dans ce champ.   |
|  |  |

Après avoir fourni toutes les informations requises, sélectionnez **Enregistrer** pour terminer la configuration de la version d’évaluation.


## <a name="next-steps"></a>Étapes suivantes

[Onglet de la Place de marché](./cpp-marketplace-tab.md)
