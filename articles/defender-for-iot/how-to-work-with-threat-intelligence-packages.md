---
title: Mettre à jour les données de renseignement sur les menaces
description: Le paquet de données pour le renseignement sur les menaces est fourni avec chaque nouvelle version de Defender pour IoT, ou entre deux versions si nécessaire.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: ddf2eb2fab6281e76bac0a958024b639d18a7ccc
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835920"
---
# <a name="threat-intelligence-research-and-packages"></a>Recherche et packages de renseignement sur les menaces

Les équipes de sécurité de Microsoft mènent des recherches exclusives sur les menaces et les vulnérabilités des ICS. Ces équipes incluent le centre Microsoft de renseignement sur les menaces (Microsoft Threat Intelligence Center, MSTIC), l’équipe Microsoft de détection et de réponse (Microsoft Detection and Response Team, DART), l’unité des crimes numériques (Digital Crimes Unit, DCU) et Section 52 (experts de domaine IoT/OT/ICS qui effectuent le suivi des programmes malveillants, des campagnes et des adversaires de l’ingénierie à rebours zero-day spécifiques à l’ICS).

Les équipes assurent la détection de sécurité, l’analyse et la réponse aux services et produits de Microsoft :

- Infrastructure et services cloud.
- Produits et appareils traditionnels.
- Ressources internes de l’entreprise.

Les équipes de sécurité bénéficient des avantages suivants :

- Protection contre les menaces connues et pertinentes.
- Insights qui permettent de trier et de classer par ordre de priorité.
- Compréhension du contexte complet des menaces avant d’être concerné par ces dernières.
- Données plus pertinentes, précises et exploitables.

Ces renseignements ajoutent des informations contextuelles pour enrichir l’analyse de la plateforme Microsoft et prend en charge les services gérés de l’entreprise pour la réponse aux incidents et les enquêtes sur les violations. Les packages de renseignement sur les menaces contiennent des signatures (y compris des signatures de programmes malveillants), des CVE et d’autres contenus de sécurité.

Vous pouvez télécharger des packages à partir de la page **Mises à jour** du portail Azure Defender pour IoT.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Téléchargez les mises à jour via le portail Azure Defender pour IoT.":::

## <a name="update-threat-intelligence-data"></a>Mettre à jour les données de renseignement sur les menaces

Les packages de renseignement sur les menaces sont fournis avec chaque nouvelle mise à jour de la version de Defender pour IoT, ou entre deux versions si nécessaire.

Les packages que vous téléchargez à partir du portail Defender pour IoT peuvent être téléchargés manuellement sur des capteurs individuels. Si la console de gestion locale gère vos capteurs, vous pouvez télécharger des packages de renseignement sur les menaces sur la console de gestion et les envoyer (push) simultanément à plusieurs capteurs.

Pour mettre à jour un package sur un seul capteur :

1. Accédez à la page **Mises à jour** d’Azure Defender pour IoT.

2. Téléchargez et enregistrez le package **Renseignement sur les menaces**.

3. Connectez-vous à la console du capteur.

4. Dans le menu latéral, sélectionnez **Paramètres système**.

5. Sélectionnez **Données de renseignement sur les menaces**, puis sélectionnez **Mettre à jour**.

6. Chargez le nouveau package.

Pour mettre à jour un package sur plusieurs capteurs simultanément :

1. Accédez à la page **Mises à jour** d’Azure Defender pour IoT.

2. Téléchargez et enregistrez le package **Renseignement sur les menaces**.

3. Connectez-vous à la console de gestion.

4. Dans le menu latéral, sélectionnez **Paramètres système**.

5. Dans la section **Configuration des moteurs des capteurs**, sélectionnez les capteurs qui doivent recevoir les packages mis à jour.  

6. Dans la section **Sélectionner les données de renseignement sur les menaces**, sélectionnez le signe plus ( **+** ).

7. Chargez le package.

## <a name="see-also"></a>Voir aussi

[Mettre à jour les versions](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
