---
title: Préparation d’un package AppSource | Place de marché Azure
description: Explique comment préparer et créer un package AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 76f8cbd6cb16b585a7dbda7b2ffa5eeeeb1b68d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280607"
---
# <a name="appsource-package-preparation"></a>Préparation d'un package AppSource

Outre le fichier solution.zip, vous aurez besoin d'un **package AppSource**. Il s’agit d’un fichier .zip incluant toutes les ressources nécessaires à l'automatisation du processus de déploiement de votre solution dans l’environnement CRM des clients. Le **package AppSource** comprend les éléments suivants :

* Package pour l'outil Package Deployer
* Fichier **Content_Types.xml** contenant les ressources que vous utilisez
* Fichier xml contenant les données spécifiques à votre application
* Logo de 32 x 32 qui s’affichera avec votre listing dans le Centre d’administration
* Termes du contrat de licence, politique de confidentialité

Les étapes ci-dessous vous aideront à créer votre package AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a>a. Créer un package pour l'outil Package Deployer

Le package créé pour l'outil Package Deployer fait partie du package AppSource.

Pour créer un package pour l'outil Package Deployer, suivez les instructions ci-dessous : [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx). Lorsqu'il sera terminé, votre package comprendra les ressources suivantes :

1. Dossier du package : contient l'ensemble des solutions, données de configuration, fichiers plats et contenus de votre package. _Remarque : dans l’exemple suivant, le dossier du package s'appelle « PkgFolder »_ .
2. dll : l’assembly contient le code personnalisé de votre package. _Remarque : dans l’exemple suivant, ce fichier s’appelle « MicrosoftSample.dll »._

Vous devez maintenant créer un fichier appelé « **Content_Types.xml** ». Celui-ci répertoriera les extensions de toutes les ressources contenues dans votre package. Voici un exemple de code pour le fichier.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

La dernière étape consiste à compresser ce qui suit dans un seul fichier. Appelez-le **package.zip**. Il contiendra :

1. PkgFolder (et tout le contenu du dossier)
2. dll
3. **Content_Types.xml**

Étapes à suivre pour créer le fichier package.zip :

1. Placez le dossier du package, le fichier **Content_Types.xml** et le fichier PackageName.dll dans un même répertoire.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Sélectionnez tous les éléments contenus dans le dossier, cliquez sur le bouton droit de la souris et choisissez Envoyer au dossier compressé (zip).

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Remplacez le nom par package.zip.

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Créer un package AppSource

Le package AppSource requiert quelques fichiers supplémentaires.

1. jpg (résolution de 32 x 32)
2. html (fichier au format HTML)
3. **Content_Types.xml** (identique au précédent)
4. Xml

Voici un exemple de code pour le fichier input.xml. Voir les définitions dans le tableau ci-dessous.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Où :**

|Champ|Détails|
|---|---|
|ProviderName|Source de la solution. S'il s'agit d'une équipe Microsoft, entrez Microsoft.|
|PackageFile |Ressources de l'outil Package Deployer zippées avec un fichier content\_types.xml. Ce fichier zip doit contenir l’assembly de l'outil Package Deployer et le dossier des ressources de l'outil Package Deployer. Autrement dit, package.zip.|
|SolutionAnchorName |Nom du fichier zip de la solution dans l'outil Package Deployer utilisé pour le nom d’affichage et la description des ressources de la solution.|
| StartDate| Date à laquelle le package de la solution sera disponible. Le format est MM/JJ/AAAA.|
|EndDate|Date à partir de laquelle le package de la solution ne sera plus disponible. Le format est MM/JJ/AAAA. |
|SupportedCountries |Il s’agit d’une liste des pays/régions (séparés par des virgules) concernés par ce package. Contactez les services en ligne pour obtenir la liste de tous les codes de pays actuels. Au moment où ce document a été rédigé, la liste était : AE,AL,AM,AO,AR,AT,AU,AZ,BA,BB,BD,BE,BG,BH,BM,BN,BO,BR,BY,CA,CH,CI,CL,CM,CO,CR,CV,CW,CY,CZ,DE,DK,DO,DZ,EC,EE,EG,ES,FI,FR,GB,GE,GH,GR,GT,HK,HN,HR,HU,ID,IE,IL,IN,IQ,IS,IT,JM,JO,JP,KE,KG,KN,KR,KW,KY,KZ,LB,LK,LT,LU,LV,LY,MA,MC,MD,ME,MK,MN,MO,MT,MU,MX,MY,NG,NI,NL,NO,NZ,OM,PA,PE,PH,PK,PL,PR,PS,PT,PY,QA,RO,RS,RU,RW,SA,SE,SG,SI,SK,SN,SV,TH,TM,TN,TR,TT,TW,UA,US,UY,UZ,VE,VI,VN,ZA,ZW |
|LearnMoreLink | URL de la page Plus d’informations concernant ce package. |
|Locales|Une instance de ce nœud pour chacune des langues que vous souhaitez prendre en charge dans l'expérience utilisateur préférée de la solution. Ce nœud contient des enfants qui décrivent les paramètres régionaux, le logo et les conditions de chaque langue.|
|Locales: PackageLocale.Code|LCID de la langue correspondant à ce nœud. Exemple : le LCID de l'anglais des États-Unis est 1033.|
|Locales: PackageLocale.IsDefault|Indique qu’il s’agit de la langue par défaut. Celle-ci est utilisée comme langue de secours si la langue choisie par le client n’est pas disponible.|
|Locales: Logo|Désigne le logo que vous souhaitez utiliser pour ce package. La taille de l’icône est de 32 x 32. Les formats autorisés sont PNG et JPG.|
|Locales:Terms: PackageTerm.File|Nom du fichier HTML contenant les termes de votre contrat de licence.|

Le logo apparaîtra ici :

![CRMScreenShot5](media/CRMScreenShot5.png)

La dernière étape consiste à compresser ce qui suit dans un seul fichier.

1. zip (créé précédemment)
2. **Content_Types.xml**
3. Xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Renommez le fichier. Choisissez un nom adapté à votre application. Nous vous conseillons d'inclure le nom de votre société et le nom de l’application. Par exemple : **_Microsoft_PackageExemple.zip**.
