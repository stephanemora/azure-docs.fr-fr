---
title: Fichier Include
description: Fichier Include
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/16/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: 43b551639fface699b1c0ec95500de8ea2089b26
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900104"
---
Lorsque vous publiez votre offre sur la Place de marché via l'Espace partenaires, vous devez la connecter à votre système de gestion de la relation client (CRM) afin de recevoir les informations de contact client immédiatement après qu'un client ait manifesté son intérêt ou déployé votre produit.

1. **Sélectionnez une destination de prospect afin de diriger les prospects vers le système de votre choix**. Les systèmes CRM pris en charge sont les suivants :

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    Si votre système CRM n'est pas explicitement pris en charge dans la liste ci-dessus, vous disposez des options suivantes pour stocker les données des prospects. Vous pourrez ensuite exporter ou importer ces données dans votre système CRM.

    * [Table Azure](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [Point de terminaison HTTPS](../commercial-marketplace-lead-management-instructions-https.md)

2. Lisez la documentation mentionnée ci-dessus afin de savoir comment configurer la destination de prospect que vous avez sélectionnée pour recevoir des prospects issus de votre offre de la Place de marché. 
3. Connectez votre offre à la destination de prospect tout en la publiant sur la Place de marché de l'Espace partenaires. Consultez la documentation mentionnée ci-dessus pour savoir comment procéder.
4. Vérifiez que la connexion à la destination de prospect est correctement configurée. Une fois votre destination de prospect correctement configurée et votre offre publiée dans l'Espace partenaires, nous validons la connexion et vous envoyons un prospect de test. Lorsque vous visualisez l’offre avant son lancement, vous pouvez également tester votre connexion de prospect en essayant d’acquérir vous-même l’offre dans l’environnement en version préliminaire. 
5. La connexion à la destination de prospect doit rester à jour afin d'éviter de perdre des prospects. Veillez donc à mettre à jour ces connexions chaque fois qu'une modification intervient de votre côté.
