---
title: Liste des classifications prises en charge
description: Cette page répertorie les classifications système prises en charge dans Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 4/1/2021
ms.openlocfilehash: 9eed079b7beda59f4835bb1263a226e2aa7f74c2
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272617"
---
# <a name="supported-classifications-in-azure-purview"></a>Classifications prises en charge dans Azure Purview

Cet article répertorie les classifications système prises en charge et définies dans Azure Purview (préversion).


- **Seuil de données distinctes** : nombre total de valeurs de données distinctes qui doivent se trouver dans une colonne pour que l’analyseur exécute le modèle de données dessus. Le seuil de données distinctes n’a rien à voir avec les critères spéciaux, mais il s’agit d’un prérequis pour les critères spéciaux. Nos règles de classification système nécessitent qu’au moins 8 valeurs distinctes dans chaque colonne soient soumises à une classification. Le système a besoin de cette valeur afin de veiller à ce que la colonne contienne suffisamment de données pour que l’analyseur puisse les classer avec précision. Par exemple, une colonne qui comporte plusieurs lignes contenant toutes la valeur 1 ne sera pas classée. Les colonnes dont une ligne comprend une valeur et les autres lignes des valeurs Null ne sont pas non plus classées. Si vous spécifiez plusieurs modèles, cette valeur s’applique à chacun d’eux.

- **Seuil de correspondances minimales** : pourcentage minimal de correspondances de valeurs de données dans une colonne qui doivent être trouvées par l’analyseur pour que la classification soit appliquée. La valeur de classification système est définie sur 60 %.


## <a name="defined-system-classifications"></a>Classifications système définies

Azure Purview classifie les données en se basant sur des [expressions régulières](https://wikipedia.org/wiki/Regular_expression) et le [filtre de Bloom](https://wikipedia.org/wiki/Bloom_filter). Les listes suivantes décrivent le format, le modèle et les mots clés pour les classifications système définies par Azure Purview.

Chaque nom de classification est précédé du préfixe MICROSOFT.

## <a name="bloom-filter-classifications"></a>Classifications Filtre de Bloom

## <a name="city-country-and-place"></a>Ville, pays et lieu

Les filtres de ville, de pays et de lieu ont été préparés à l’aide des meilleurs jeux de données disponibles pour la préparation des données.

## <a name="person"></a>Personne

Le filtre de Bloom Person a été préparé à l’aide des deux jeux de données ci-dessous.

- [Données de recensement des noms de famille aux États-Unis en 2010 (162 000 entrées)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Noms de bébés populaires (à partir des numéros de sécurité sociale aux USA) entre 1880 et 2019 (98 000 entrées)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>Classifications RegEx

## <a name="aba-routing"></a>Routage ABA

### <a name="format"></a>Format

Neuf chiffres, qui peuvent être dans un modèle mis en forme ou non mis en forme

### <a name="pattern"></a>Modèle

Mis en forme :

- quatre chiffres commençant par 0, 1, 2, 3, 6, 7 ou 8
- trait d’union
- quatre chiffres
- trait d’union
- un chiffre

Non mis en forme : neuf chiffres consécutifs commençant par 0, 1, 2, 3, 6, 7 ou 8

### <a name="keywords"></a>Mots clés

#### <a name="keyword_aba_routing"></a>Mot clé\_aba\_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>Numéro d’identité nationale (DNI) en Argentine

### <a name="format"></a>Format

Huit chiffres avec ou sans points

### <a name="pattern"></a>Modèle

Huit chiffres :

- deux chiffres
- point facultatif
- trois chiffres
- point facultatif
- trois chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_argentina_national_id"></a>Mot clé\_argentina\_national\_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Numéro de compte bancaire en Australie

### <a name="format"></a>Format

6 à 10 chiffres avec ou sans numéro de succursale d’établissement bancaire

### <a name="pattern"></a>Modèle

Le numéro de compte inclut entre 6 et 10 chiffres.

Numéro de succursale d’établissement bancaire en Australie :

- trois chiffres
- trait d’union
- trois chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_australia_bank_account_number"></a>Mot clé\_australia\_bank\_account\_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```
## <a name="australia-business-number"></a>Numéro d’entreprise en Australie

### <a name="format"></a>Format
11 chiffres avec délimiteurs facultatifs

### <a name="pattern"></a>Modèle
11 chiffres avec délimiteurs facultatifs :

- deux chiffres
- un trait d’union ou un espace facultatif
- trois chiffres
- un trait d’union ou un espace facultatif
- trois chiffres
- un trait d’union ou un espace facultatif
- trois chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_australia_business_number"></a>Mot clé\_australia\_business\_number
```
australia business no
business number
abn#
businessid#
business id
abn
businessno#
```

## <a name="australia-company-number"></a>Numéro de société en Australie 

### <a name="format"></a>Format
neuf chiffres avec des délimiteurs

### <a name="pattern"></a>Modèle
neuf chiffres avec des délimiteurs :

- trois chiffres
- espace
- trois chiffres
- espace
- trois chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_australia_company_number"></a>Mot clé\_australia\_company\_number
```
acn
australia company no
australia company no#
australia company number
australian company no
australian company no#
australian company number
```
## <a name="australia-drivers-license-number"></a>Numéro de permis de conduire en Australie

### <a name="format"></a>Format
Neufs lettres et chiffres

### <a name="pattern"></a>Modèle
neufs lettres et chiffres :

- deux chiffres ou lettres (ne respectant pas la casse)
- deux chiffres
- cinq chiffres ou lettres (ne respectant pas la casse)

OU

- une à deux lettres facultatives (ne respectant pas la casse)
- quatre à neuf chiffres

OU

- neuf chiffres ou lettres (ne respectant pas la casse)

### <a name="keywords"></a>Mots clés

#### <a name="keyword_australia_drivers_license_number"></a>Mot clé\_australia\_drivers\_license\_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Mot clé\_australia\_drivers\_license\_number\_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>Numéro d'assurance maladie australien

### <a name="format"></a>Format

10-11 chiffres

### <a name="pattern"></a>Modèle

10-11 chiffres :

- le premier chiffre est compris entre 2 et 6
- le neuvième chiffre est un chiffre de vérification
- le dixième chiffre est le chiffre de l’émission
- le onzième chiffre (facultatif) est le numéro individuel

### <a name="keywords"></a>Mots clés

#### <a name="keyword_australia_medicare_number"></a>Mot clé\_Australia\_Medicare\_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Numéro de passeport en Australie

### <a name="format"></a>Format

Lettre suivie de sept chiffres

### <a name="pattern"></a>Modèle

Une lettre (sans respect de la casse) suivie de sept chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Keyword\_australia\_passport\_number

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Numéro d’identification fiscale en Australie

### <a name="format"></a>Format

huit à neuf chiffres

### <a name="pattern"></a>Modèle

huit à neuf chiffres généralement présentés avec des espaces, comme suit :

- trois chiffres
- espace facultatif
- trois chiffres
- espace facultatif
- deux à trois chiffres où le dernier chiffre est un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_australia_tax_file_number"></a>Keyword\_australia\_tax\_file\_number

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```
## <a name="austria-identity-card"></a>Carte d’identité en Autriche

### <a name="format"></a>Format

Combinaison de 24 caractères de lettres, de chiffres et de caractères spéciaux

### <a name="pattern"></a>Modèle

24 caractères :

- 22 lettres (sans respect de la casse), chiffres, barres obliques inverses, barres obliques ou signes plus
- Deux lettres (sans respect de la casse), chiffres, barres obliques inverses, barres obliques, signes plus ou signe égal

### <a name="keywords"></a>Mots clés

#### <a name="keyword_austria_eu_national_id_card"></a>Mot clé\_austria\_eu\_national\_id\_card

```
identity number
national id
personalausweis republik österreich
```
## <a name="austria-tax-identification-number"></a>Numéro d’identification fiscale autrichien

### <a name="format"></a>Format

neuf chiffres avec un trait d’union et une barre oblique facultatifs

### <a name="pattern"></a>Modèle

neuf chiffres avec un trait d’union et une barre oblique facultatifs :

- deux chiffres
- un trait d’union (facultatif)
- trois chiffres
- barre oblique (facultatif)
- quatre chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_austria_eu_tax_file_number"></a>Mots clés\_austria\_eu\_tax\_file\_number

```
österreich
st.nr.
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
tax number
```
## <a name="austria-passport-number"></a>Numéro de passeport en Autriche

### <a name="format"></a>Format
Une lettre suivie d’un espace facultatif et de sept chiffres

### <a name="pattern"></a>Modèle
Combinaison d’une lettre, de sept chiffres et d’un espace :

- une lettre (ne respectant pas la casse)
- un espace (facultatif)
- sept chiffres

### <a name="keywords"></a>Mots clés
#### <a name="keywords_eu_passport_number"></a>Mots clé \_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_austria_eu_passport_number"></a>Mots clé\_austria\_eu\_passport\_number
```
reisepassnummer
reisepasse
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="austria-social-security-number"></a>Numéro de sécurité sociale en Autriche 

### <a name="format"></a>Format
10 chiffres dans le format spécifié

### <a name="pattern"></a>Modèle
10 chiffres :

- trois chiffres qui correspondent à un numéro de série
- un chiffre de vérification
- six chiffres correspondant à la date de naissance (DDMMYY)

### <a name="keywords"></a>Mots clés

#### <a name="keywords_austria_eu_ssn_or_equivalent"></a>Mots clés\_austria\_eu\_ssn\_or\_equivalent
```
austrian ssn
ehic number
ehic no
insurance code
insurancecode#
insurance number
insurance no
krankenkassennummer
krankenversicherung
socialsecurityno
socialsecurityno#
social security no
social security number
social security code
sozialversicherungsnummer
sozialversicherungsnummer#
soziale sicherheit kein
sozialesicherheitkein#
ssn#
ssn
versicherungscode
versicherungsnummer
zdravstveno zavarovanje
```

## <a name="austria-tax-identification-number"></a>Numéro d’identification fiscale autrichien 

### <a name="format"></a>Format
neuf chiffres avec un trait d’union et une barre oblique facultatifs

### <a name="pattern"></a>Modèle
neuf chiffres avec un trait d’union et une barre oblique facultatifs :

- deux chiffres
- un trait d’union (facultatif)
- trois chiffres
- barre oblique (facultatif)
- quatre chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_austria_eu_tax_file_number"></a>Mots clés\_austria\_eu\_tax\_file\_number
```
österreich
st.nr.
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
tax number
```

## <a name="austria-value-added-tax"></a>TVA en Autriche 
### <a name="format"></a>Format
Modèle alphanumérique à 11 caractères

### <a name="pattern"></a>Modèle
Modèle alphanumérique à 11 caractères :

- A ou a
- T ou t
- Espace facultatif
- U ou u
- espace facultatif
- deux ou trois chiffres
- espace facultatif
- quatre chiffres
- espace facultatif
- un ou deux chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_austria_value_added_tax"></a>Mot clé\_austria\_value\_added\_tax
```
vat number
vat#
austrian vat number
vat no.
vatno#
value added tax number
austrian vat
mwst
umsatzsteuernummer
mwstnummer
ust.-identifikationsnummer
umsatzsteuer-identifikationsnummer
vat identification number
atu number
uid number
```
## <a name="belgium-drivers-license-number"></a>Numéro de permis de conduire en Belgique 

### <a name="format"></a>Format
10 chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
10 chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
 
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_belgium_eu_drivers_license_number"></a>Mots clés \_belgium\_eu\_driver's\_license\_number
```
rijbewijs
rijbewijsnummer
führerschein
führerscheinnummer
füehrerscheinnummer
fuhrerschein
fuehrerschein
fuhrerscheinnummer
fuehrerscheinnummer
permis de conduire
numéro permis conduire
```
## <a name="belgium-national-number"></a>Numéro national en Belgique

### <a name="format"></a>Format
11 chiffres plus délimiteurs facultatifs

### <a name="pattern"></a>Modèle
11 chiffres plus délimiteurs :

- six chiffres et deux points facultatifs au format YY.MM.DD pour la date de naissance
- Délimiteur facultatif (point, tiret, espace)
- trois chiffres séquentiels (impair pour les hommes, pair pour les femmes)
- Délimiteur facultatif (point, tiret, espace)
- deux chiffres de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_belgium_national_number"></a>Keyword\_belgium\_national\_number
```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```
## <a name="belgium-passport-number"></a>Numéro de passeport en Belgique 

### <a name="format"></a>Format
deux lettres suivies de six chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle
deux lettres et suivies par six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number"></a>Mots clé \_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_belgium_eu_passport_number"></a>Mots clés\_belgium\_eu\_passport\_number
```
numéro passeport
paspoort nr
paspoort-nr
paspoortnummer
paspoortnummers
Passeport carte
Passeport livre
Pass-Nr
Passnummer
reisepass kein
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="belgium-value-added-tax-number"></a>Numéro de TVA en Belgique 
### <a name="format"></a>Format
Modèle alphanumérique à 12 caractères

### <a name="pattern"></a>Modèle
Modèle alphanumérique à 12 caractères :

- une lettre B ou b
- une lettre E ou e
- un chiffre 0
- un chiffre de 1 à 9
- un point ou un trait d’union ou un espace facultatif
- quatre chiffres
- un point ou un trait d’union ou un espace facultatif
- quatre chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_belgium_value_added_tax_number"></a>Mot clé\_belgium\_value\_added\_tax\_number
```
nº tva
vat number
vat no
numéro t.v.a
umsatzsteuer-identifikationsnummer
umsatzsteuernummer
btw
btw#
vat#
```

## <a name="brazil-cpf-number"></a>Numéro CPF au Brésil

### <a name="format"></a>Format

11 chiffres incluant un chiffre de vérification et pouvant être mis en forme ou non

### <a name="pattern"></a>Modèle

Mis en forme :

- trois chiffres
- point
- trois chiffres
- point
- trois chiffres
- trait d’union
- deux chiffres, qui sont des chiffres de vérification

Sans mise en forme :

- 11 chiffres où les deux derniers chiffres sont des chiffres de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_brazil_cpf"></a>Keyword\_brazil\_cpf
```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>Numéro d’entité légale (CNPJ) au Brésil

### <a name="format"></a>Format

14 chiffres incluant un numéro d’enregistrement, un numéro de succursale et des chiffres de vérification, plus des délimiteurs

### <a name="pattern"></a>Modèle

14 chiffres plus délimiteurs :

- deux chiffres
- point
- trois chiffres
- point
- trois chiffres (les huit premiers chiffres correspondent au numéro d’enregistrement)
- barre oblique
- numéro de succursale à quatre chiffres
- trait d’union
- deux chiffres, qui sont des chiffres de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_brazil_cnpj"></a>Keyword\_brazil\_cnpj
```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>Numéro d’identification nationale au Brésil (RG)

### <a name="format"></a>Format

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Modèle

:::no-loc text="Registro Geral (old format):":::

- deux chiffres
- point
- trois chiffres
- point
- trois chiffres
- trait d’union
- un chiffre, qui est un chiffre de vérification

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 chiffres
- trait d’union
- un chiffre, qui est un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_brazil_rg"></a>Keyword\_brazil\_rg
```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```
## <a name="bulgaria-passport-number"></a>Numéro de passeport en Bulgarie 

### <a name="format"></a>Format
neuf chiffres
### <a name="pattern"></a>Modèle
neuf chiffres sans espaces ni délimiteurs
### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number"></a>mots clés\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_bulgaria_eu_passport_number"></a>mots clés\_bulgaria\_eu\_passport\_number
```
номер на паспорта
номер на паспорт
паспорт №
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="bulgaria-uniform-civil-number"></a>Numéro civil unifié en Bulgarie

### <a name="format"></a>Format
10 chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
10 chiffres sans espaces ni délimiteurs

- six chiffres correspondant à la date de naissance (AAMMJJ)
- deux chiffres correspondant à l’ordre de naissance
- un chiffre qui correspond au sexe : un chiffre pair pour homme et un chiffre impair pour femelles
- un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_bulgaria_eu_national_id_card"></a>Mots clés\_bulgaria\_eu\_national\_id\_card
```
bnn#
bnn
bucn#
bucn
edinen grazhdanski nomer
egn#
egn
identification number
national id
national number
nationalnumber#
nationalnumber
personal id
personal no
personal number
personalidnumber#
social security number
ssn#
ssn
uniform civil id
uniform civil no
uniform civil number
uniformcivilno#
uniformcivilno
uniformcivilnumber#
uniformcivilnumber
unique citizenship number
егн#
егн
единен граждански номер
идентификационен номер
личен номер
лична идентификация
лично не
национален номер
номер на гражданството
униформ id
униформ граждански id
униформ граждански не
униформ граждански номер
униформгражданскиid#
униформгражданскине.#
```

## <a name="canada-bank-account-number"></a>Numéro de compte bancaire au Canada

### <a name="format"></a>Format

7 ou 12 chiffres

### <a name="pattern"></a>Modèle

Le numéro de compte bancaire du Canada inclut 7 ou 12 chiffres.

Le numéro de transit d’un compte bancaire au Canada est le suivant :

- cinq chiffres
- trait d’union
- trois chiffres OU
- un zéro &quot;0&quot;
- huit chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_canada_bank_account_number"></a>Keyword\_canada\_bank\_account\_number
```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Numéro de permis de conduire au Canada

### <a name="format"></a>Format

Varie selon la province

### <a name="pattern"></a>Modèle

Différents modèles couvrant l’Alberta, la Colombie-Britannique, le Manitoba, le Nouveau-Brunswick, Terre-Neuve-et-Labrador, la Nouvelle-Écosse, l’Ontario, l’île du Prince Edward, le Québec et Saskatchewan

### <a name="keywords"></a>Mots clés

#### <a name="keyword_province_name_drivers_license_name"></a>Keyword\_[province\_name]\_drivers\_license\_name
```
- The province abbreviation, for example AB
- The province name, for example Alberta
```
#### <a name="keyword_canada_drivers_license"></a>Keyword\_canada\_drivers\_license

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Numéro de service médical au Canada

### <a name="format"></a>Format

10 chiffres

### <a name="pattern"></a>Modèle

10 chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_canada_health_service_number"></a>Keyword\_canada\_health\_service\_number

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Numéro de passeport au Canada

### <a name="format"></a>Format

deux lettres majuscules suivies de six chiffres

### <a name="pattern"></a>Modèle

deux lettres majuscules suivies de six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_canada_passport_number"></a>Keyword\_canada\_passport\_number

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Numéro d’identification médicale personnelle (PHIN) au Canada

### <a name="format"></a>Format

neuf chiffres

### <a name="pattern"></a>Modèle

neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_canada_phin"></a>Keyword\_canada\_phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Keyword\_canada\_provinces

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Numéro d’assurance sociale au Canada

### <a name="format"></a>Format

neuf chiffres avec traits d’union ou espaces facultatifs

### <a name="pattern"></a>Modèle

Mis en forme :

- trois chiffres
- trait d’union ou espace
- trois chiffres
- trait d’union ou espace
- trois chiffres

Non formaté : neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_sin"></a>Keyword\_sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Keyword\_sin\_collaborative

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>Numéro de carte d’identité au Chili

### <a name="format"></a>Format

sept à huit chiffres plus délimiteurs, un chiffre ou une lettre de vérification

### <a name="pattern"></a>Modèle

sept à huit chiffres plus les délimiteurs :

- un à deux chiffres
- point facultatif
- trois chiffres
- point facultatif
- trois chiffres
- tiret
- un chiffre ou une lettre (ne respectant pas la casse), qui est un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_chile_id_card"></a>Keyword\_chile\_id\_card

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>Numéro de carte d’identité de résident (RPC) en Chine

### <a name="format"></a>Format

18 chiffres

### <a name="pattern"></a>Modèle

18 chiffres :

- six chiffres, qui sont un code d’adresse
- huit chiffres au format YYYYMMDD (date de naissance)
- trois chiffres (code de commande)
- un chiffre, qui est un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_china_resident_id"></a>Keyword\_china\_resident\_id

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Numéro de carte de crédit

### <a name="format"></a>Format

14 à 16 chiffres, qui peuvent être mis en forme ou non mis en forme (dddddddddddddddd) et qui doivent réussir le test Luhn.

### <a name="pattern"></a>Modèle

Modèle complexe et robuste qui détecte les cartes de toutes les principales marques dans le monde entier, notamment les cartes Visa, MasterCard, Discover Card, JCB, American Express, cartes cadeaux et Diner Cards.

### <a name="keywords"></a>Mots clés

#### <a name="keyword_cc_verification"></a>Keyword\_cc\_verification

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>Keyword\_cc\_name

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Numéro de permis de conduire de Croatie

Cette entité de type d’informations sensibles est uniquement disponible dans le type d’informations sensibles Numéro de licence de conducteur européen.

### <a name="format"></a>Format

huit chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle

huit chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Keywords\_croatia\_eu\_driver's\_license\_number

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>Numéro de carte d’identité en Croatie

Cette entité de type d’informations sensibles est incluse dans le type d’information sensible Numéro d’identification national de l’UE et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

neuf chiffres

### <a name="pattern"></a>Modèle

neuf chiffres consécutifs

### <a name="keywords"></a>Mots clés

#### <a name="keyword_croatia_id_card"></a>Keyword\_croatia\_id\_card

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```
## <a name="croatia-passport-number"></a>Numéro de passeport en Croatie
### <a name="format"></a>Format
neuf chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_croatia_eu_passport_number"></a>Mots clés\_croatia\_eu\_passport\_number
```
broj putovnice
br. Putovnice
br putovnice
```
## <a name="croatia-personal-identification-oib-number"></a>Numéro d’identification personnelle (OIB) en Croatie

### <a name="format"></a>Format

11 chiffres

### <a name="pattern"></a>Modèle

11 chiffres :

- 10 chiffres
- le dernier chiffre est un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_croatia_oib_number"></a>Keyword\_croatia\_oib\_number

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```
## <a name="cyprus-drivers-license-number"></a>Numéro de permis de conduire à Chypre 

### <a name="format"></a>Format
12 chiffres sans espaces ni délimiteurs
### <a name="pattern"></a>Modèle
12 chiffres
### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_cyprus_eu_drivers_license_number"></a>Mots clés\_cyprus\_eu\_driver's\_license\_number
```
άδεια οδήγησης
αριθμό άδειας οδήγησης
άδειες οδήγησης
```
## <a name="cyprus-identity-card"></a>Carte d’identité à Chypre

### <a name="format"></a>Format
10 chiffres sans espaces ni délimiteurs
### <a name="pattern"></a>Modèle

10 chiffres
### <a name="keywords"></a>Mots clés

#### <a name="keywords_cyprus_eu_national_id_card"></a>Mots clés\_cyprus\_eu\_national\_id\_card
```
id card number
identity card number
kimlik karti
national identification number
personal id number
ταυτοτητασ
```
## <a name="cyprus-passport-number"></a>Numéro de passeport à Chypre
### <a name="format"></a>Format
une lettre suivie de 6-8 chiffres sans espaces ni délimiteurs
### <a name="pattern"></a>Modèle
une lettre suivie de six à huit chiffres
### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_cyprus_eu_passport_number"></a>Mots clés\_cyprus\_eu\_passport\_number
```
αριθμό διαβατηρίου
pasaportu
Αριθμός Διαβατηρίου
κυπριακό διαβατήριο
διαβατήριο#
διαβατήριο
αριθμός διαβατηρίου
Pasaport Kimliği
pasaport numarası
Pasaport no.
Αρ. Διαβατηρίου
```
#### <a name="keywords_cyprus_eu_passport_date"></a>Mots clés\_cyprus\_eu\_passport\_date
```
expires on
issued on
```
## <a name="cyprus-tax-identification-number"></a>Numéros d’identification fiscale à Chypre

### <a name="format"></a>Format
huit chiffres et une lettre dans le modèle spécifié

### <a name="pattern"></a>Modèle

huit chiffres et une lettre :

- un « 0 » ou un « 9 »
- sept chiffres
- une lettre (ne respectant pas la casse)

### <a name="keywords"></a>Mots clés
#### <a name="keywords_cyprus_eu_tax_file_number"></a>Mots clés\_cyprus\_eu\_tax\_file\_number
```
tax id
tax identification code
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tic#
tic
tin id
tin no
tin#
vergi kimlik kodu
vergi kimlik numarası
αριθμός φορολογικού μητρώου
κωδικός φορολογικού μητρώου
φορολογική ταυτότητα
φορολογικού κωδικού
```
## <a name="czech-republic-drivers-license-number"></a>Numéro de permis de conduire en République tchèque 
### <a name="format"></a>Format
deux lettres suivies de six chiffres
### <a name="pattern"></a>Modèle
huit lettres et chiffres :

- une lettre « E » (ne respectant pas la casse)
- une lettre
- espace (facultatif)
- six chiffres
### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_czech_republic_eu_drivers_license_number"></a>Mots clés\_czech\_republic\_eu\_driver's\_license\_number
```
řidičský prúkaz
řidičské průkazy
číslo řidičského průkazu
čísla řidičských průkazů
```

## <a name="czech-passport-number"></a>Numéro de passeport en République Tchèque 
### <a name="format"></a>Format
huit chiffres sans espaces ni délimiteurs
### <a name="pattern"></a>Modèle
huit chiffres sans espaces ni délimiteurs

### <a name="keywords"></a>Mots clés
#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_czech_republic_eu_passport_number"></a>Mots clés\_czech\_republic\_eu\_passport\_number
```
cestovní pas
číslo pasu
cestovní pasu
passeport no
čísla pasu
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="denmark-drivers-license-number"></a>Numéro de permis de conduire au Danemark 

### <a name="format"></a>Format
huit chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
huit chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_denmark_eu_drivers_license_number"></a>Mots clés\_denmark\_eu\_driver's\_license\_number
```
kørekort
kørekortnummer
```
## <a name="denmark-passport-number"></a>Numéro de passeport du Danemark 
### <a name="format"></a>Format
neuf chiffres sans espaces ni délimiteurs
### <a name="pattern"></a>Modèle
neuf chiffres
### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_denmark_eu_passport_number"></a>Mots clés\_denmark\_eu\_passport\_number
```
pasnummer
Passeport n°
pasnumre
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="denmark-personal-identification-number"></a>Numéro d’identification personnelle au Danemark

### <a name="format"></a>Format

10 chiffres contenant un trait d’union

### <a name="pattern"></a>Modèle

10 chiffres :

- six chiffres au format DDMMYY (date de naissance)
- trait d’union
- quatre chiffres où le dernier chiffre est un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_denmark_id"></a>Keyword\_denmark\_id

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```
## <a name="estonia-drivers-license-number"></a>Numéro de permis de conduire en Estonie 
### <a name="format"></a>Format
deux lettres suivies de six chiffres
### <a name="pattern"></a>Modèle
deux lettres et six chiffres :

- Les lettres « ET » (ne respectant pas la casse)
- six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_estonia_eu_drivers_license_number"></a>Mots clés\_estonia\_eu\_driver's\_license\_number
```
-- permis de conduire
juhilubade numbrid
juhiloa number
juhiluba
```
## <a name="estonia-passport-number"></a>Numéro de passeport en Estonie 
### <a name="format"></a>Format
une lettre suivie de sept chiffres sans espaces ni délimiteurs
### <a name="pattern"></a>Modèle
une lettre suivie de sept chiffres
### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_estonia_eu_passport_number"></a>Mots clés\_estonia\_eu\_passport\_number
```
eesti kodaniku pass passi number passinumbrid document number document no dokumendi nr
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="estonia-personal-identification-code-isikukood"></a>Code d’identification personnelle en Estonie (isikukood)

### <a name="format"></a>Format
11 chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
11 chiffres :

- un chiffre qui correspond au sexe et au siècle de naissance (nombre impair mâle, même nombre féminin ; 1-2 : 19e siècle ; 3-4 : 20e siècle ; 5-6 : 21e siècle)
- six chiffres correspondant à la date de naissance (AAMMJJ)
- trois chiffres qui correspondent à un numéro de série séparant les personnes nées à la même date
- un chiffre de vérification
### <a name="keywords"></a>Mots clés
#### <a name="keywords_estonia_eu_national_id_card"></a>Mots clés\_estonia\_eu\_national\_id\_card
```
id-kaart
ik
isikukood#
isikukood
maksu id
maksukohustuslase identifitseerimisnumber
maksunumber
national identification number
national number
personal code
personal id number
personal identification code
personal identification number
personalidnumber#
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="eu-debit-card-number"></a>Numéro de carte de débit UE

### <a name="format"></a>Format

16 chiffres

### <a name="pattern"></a>Modèle

Modèle complexe et robuste

### <a name="keywords"></a>Mots clés

#### <a name="keyword_eu_debit_card"></a>Keyword\_eu\_debit\_card

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Keyword\_card\_terms\_dict

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>Keyword\_card\_security\_terms\_dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>Keyword\_card\_expiration\_terms\_dict

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>Numéro de permis de conduire dans l’Union européenne

Il s’agit des entités du type d’informations sensibles Numéro de permis de conduire dans l’Union européenne.

- Autriche
- Belgique
- Bulgarie
- Croatie
- Chypre
- Tchèque
- Danemark
- Estonie
- Finlande
- France
- Allemagne
- Grèce
- Hongrie
- Irlande
- Italie
- Lettonie
- Lituanie
- Luxembourg
- Malte
- Pays-Bas
- Pologne
- Portugal
- Roumanie
- Slovaquie
- Slovénie
- Espagne
- Suède
- au Royaume-Uni

## <a name="eu-national-identification-number"></a>Numéro d’identification nationale dans l’Union européenne

Il s’agit des entités du type d’informations sensibles Numéro d’identification nationale dans l’Union européenne.

- Autriche
- Belgique
- Bulgarie
- Croatie
- Chypre
- Tchèque
- Danemark
- Estonie
- Finlande
- France
- Allemagne
- Grèce
- Hongrie
- Irlande
- Italie
- Lettonie
- Lituanie
- Luxembourg
- Malte
- Pays-Bas
- Pologne
- Portugal
- Roumanie
- Slovaquie
- Slovénie
- Espagne
- au Royaume-Uni

## <a name="eu-passport-number"></a>Numéro de passeport dans l’Union européenne

Il s’agit des entités du type d’informations sensibles Numéro de passeport dans l’Union européenne. Il s’agit des entités du groupe Numéro de passeport dans l’Union européenne.

- Autriche
- Belgique
- Bulgarie
- Croatie
- Chypre
- Tchèque
- Danemark
- Estonie
- Finlande
- France
- Allemagne
- Grèce
- Hongrie
- Irlande
- Italie
- Lettonie
- Lituanie
- Luxembourg
- Malte
- Pays-Bas
- Pologne
- Portugal
- Roumanie
- Slovaquie
- Slovénie
- Espagne
- Suède
- au Royaume-Uni

## <a name="eu-social-security-number-or-equivalent-identification"></a>Numéro de sécurité sociale dans l’Union européenne (ou équivalent)

Il s’agit des entités du type d’informations sensibles Numéro de sécurité sociale dans l’Union européenne (ou équivalent).

- Autriche
- Belgique
- Croatie
- Tchèque
- Danemark
- Finlande
- France
- Allemagne
- Grèce
- Hongrie
- Portugal
- Espagne
- Suède

## <a name="eu-tax-identification-number"></a>Numéros d’identification fiscale dans l’Union européenne

Il s’agit des entités du type d’informations sensibles Numéros d’identification fiscale dans l’Union européenne.

- Autriche
- Belgique
- Bulgarie
- Croatie
- Chypre
- Tchèque
- Danemark
- Estonie
- Finlande
- France
- Allemagne
- Grèce
- Hongrie
- Irlande
- Italie
- Lettonie
- Lituanie
- Luxembourg
- Malte
- Pays-Bas
- Pologne
- Portugal
- Roumanie
- Slovaquie
- Slovénie
- Espagne
- Suède
- au Royaume-Uni

## <a name="finland-drivers-license-number"></a>Numéro de permis de conduire en Finlande 

### <a name="format"></a>Format
10 chiffres contenant un trait d’union

### <a name="pattern"></a>Modèle
10 chiffres contenant un trait d’union :

- six chiffres
- trait d’union
- trois chiffres
- un chiffre ou une lettre

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_finland_eu_drivers_license_number"></a>Mots clés\_finland\_eu\_driver's\_license\_number
```
ajokortti
permis de conduire
ajokortin numero
kuljettaja lic.
körkort
körkortnummer
förare lic.
ajokortit
ajokortin numerot
```
## <a name="finland-european-health-insurance-number"></a>Numéro d’assurance maladie européen en Finlande

### <a name="format"></a>Format
nombre à 20 chiffres
### <a name="pattern"></a>Modèle
nombre à 20 chiffres
- 10 chiffres - 8024680246
- un espace ou trait d’union facultatif
- 10 chiffres

### <a name="keywords"></a>Mots clés
#### <a name="keyword_finland_european_health_insurance_number"></a>Mot clé\_finland\_european\_health\_insurance\_number
```
ehic#
ehic
finlandehicnumber#
finska sjukförsäkringskort
health card
health insurance card
health insurance number
hälsokort
sairaanhoitokortin
sairausvakuutuskortti
sairausvakuutusnumero
sjukförsäkring nummer
sjukförsäkringskort
suomen sairausvakuutuskortti
terveyskortti
```
## <a name="finland-national-id"></a>Numéro d’identité nationale en Finlande

### <a name="format"></a>Format

six chiffres plus un caractère indiquant un siècle plus trois chiffres plus un chiffre de vérification

### <a name="pattern"></a>Modèle

Le modèle doit inclure tous les éléments suivants :

- six chiffres au format DDMMYY (date de naissance)
- marqueur de siècle (-, + ou a)
- numéro d’identification personnelle à 3 chiffres
- un chiffre ou une lettre (respectant la casse), qui est un chiffre de vérification

### <a name="keywords"></a>Mots clés

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Numéro de passeport en Finlande

Cette entité de type d’informations sensibles est disponible dans le type d’informations sensibles Numéro de passeport européen et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

combinaison de neuf lettres et chiffres

### <a name="pattern"></a>Modèle

combinaison de neuf lettres et chiffres :

- deux lettres (ne respectant pas la casse)
- sept chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Keyword\_finland\_passport\_number

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>Numéro de permis de conduire en France

Cette entité de type d’informations sensibles est incluse dans le type d’information sensible Numéro de permis de conduire européen et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

12 chiffres

### <a name="pattern"></a>Modèle

12 chiffres avec validation pour tenir compte des modèles similaires tels que les numéros de téléphone français

### <a name="keywords"></a>Mots clés

#### <a name="keyword_french_drivers_license"></a>Keyword\_french\_drivers\_license

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```
## <a name="france-health-insurance-number"></a>Numéro d’assurance maladie en France
### <a name="format"></a>Format
nombre à 21 chiffres
### <a name="pattern"></a>Modèle
nombre à 21 chiffres :

- 10 chiffres
- espace facultatif
- 10 chiffres
- espace facultatif
- un chiffre
### <a name="keywords"></a>Mots clés
#### <a name="keyword_france_health_insurance_number"></a>Mot clé\_France\_health\_insurance\_number
```
insurance card
carte vitale
carte d'assuré social
```
## <a name="france-national-id-card-cni"></a>Carte nationale d’identité (CNI) en France

### <a name="format"></a>Format

12 chiffres

### <a name="pattern"></a>Modèle

12 chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_france_eu_national_id_card"></a>Keywords\_france\_eu\_national\_id\_card

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Numéro de passeport en France

Cette entité de type d’informations sensibles est disponible dans le type d’informations sensibles Numéro de passeport européen et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

neuf chiffres et lettres

### <a name="pattern"></a>Modèle

neuf chiffres et lettres :

- deux chiffres
- deux lettres (ne respectant pas la casse)
- cinq chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>Numéro de sécurité sociale en France (INSEE) ou équivalent

Cette entité de type d’informations sensibles est incluse dans le type d’informations sensibles Numéro de sécurité sociale en France (INSEE) ou équivalent et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

15 chiffres

### <a name="pattern"></a>Modèle

Doit correspondre à l’un des deux modèles suivants :

- 13 chiffres suivis d’un espace suivi de deux chiffres ou
- 15 chiffres consécutifs

### <a name="keywords"></a>Mots clés

#### <a name="keyword_fr_insee"></a>Keyword\_fr\_insee

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```
## <a name="france-tax-identification-number-numro-spi"></a>Numéros d’identification fiscale en France (numéro SPI.) 
### <a name="format"></a>Format 
13 chiffres
### <a name="pattern"></a>Modèle
13 chiffres

- Un chiffre qui doit être 0, 1, 2, ou 3
- Un chiffre
- Un espace (facultatif)
- Deux chiffres
- Un espace (facultatif)
- Trois chiffres
- Un espace (facultatif)
- Trois chiffres
- Un espace (facultatif)
- Trois chiffres de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_france_eu_tax_file_number"></a>Mots clés\_france\_eu\_tax\_file\_number
```
numéro d'identification fiscale
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="france-value-added-tax-number"></a>Numéro de TVA en France 

### <a name="format"></a>Format 
Modèle alphanumérique à 13 caractères

### <a name="pattern"></a>Modèle
Modèle alphanumérique à 13 caractères :

- deux lettres - FR (non-respect de la casse)
- un espace ou trait d’union facultatif
- Deux lettres ou chiffres
- espace, point, trait d’Union ou virgule facultatif
- trois chiffres
- espace, point, trait d’Union ou virgule facultatif
- trois chiffres
- espace, point, trait d’Union ou virgule facultatif
- trois chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_france_value_added_tax_number"></a>Mot clé\_France\_value\_added\_tax\_number
```
vat number
vat no
vat#
value added tax
siren identification no numéro d'identification taxe sur valeur ajoutée
taxe valeur ajoutée
taxe sur la valeur ajoutée
n° tva
numéro de tva
numéro d'identification siren
```
## <a name="germany-drivers-license-number"></a>Numéro de permis de conduire en Allemagne

Cette entité de type d’informations sensibles est incluse dans le type d’information sensible Numéro de permis de conduire européen et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

combinaison de 11 chiffres et lettres

### <a name="pattern"></a>Modèle

11 chiffres et lettres (ne respectant pas la casse) :

- un chiffre ou une lettre
- deux chiffres
- six chiffres ou lettres
- un chiffre
- un chiffre ou une lettre

### <a name="keywords"></a>Mots clés

#### <a name="keyword_german_drivers_license_number"></a>Keyword\_german\_drivers\_license\_number

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Numéro de carte d’identité en Allemagne

### <a name="format"></a>Format

depuis le 1er novembre 2010 : Neufs lettres et chiffres

du 1er avril 1987 jusqu’au 31 octobre 2010 : 10 chiffres

### <a name="pattern"></a>Modèle

depuis le 1er novembre 2010 :

- une lettre (ne respectant pas la casse)
- huit chiffres

du 1er avril 1987 jusqu’au 31 octobre 2010 :

- 10 chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_germany_id_card"></a>Keyword\_germany\_id\_card

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Numéro de passeport en Allemagne

Cette entité de type d’informations sensibles est incluse dans le type d’informations sensibles Numéro de passeport européen et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

10 chiffres ou lettres

### <a name="pattern"></a>Modèle

Le modèle doit inclure tous les éléments suivants :

- le premier caractère est un chiffre ou une lettre de cet ensemble (C, F, G, H, J, K)
- trois chiffres
- cinq chiffres ou lettres de cet ensemble (C,H, J-N, P, R, T, V-Z)
- un chiffre

### <a name="keywords"></a>Mots clés

#### <a name="keyword_german_passport"></a>Keyword\_german\_passport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```
## <a name="germany-tax-identification-number"></a>Numéros d’identification fiscale en Allemagne 

### <a name="format"></a>Format 
11 chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
11 chiffres

- Deux chiffres
- Un espace facultatif
- Trois chiffres
- Un espace facultatif
- Trois chiffres
- Un espace facultatif
- Deux chiffres
- un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_germany_eu_tax_file_number"></a>Mots clés\_germany\_eu_tax\_file\_number
```
identifikationsnummer
steuer id
steueridentifikationsnummer
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
zinn#
zinn
zinnnummer
```
## <a name="germany-value-added-tax-number"></a>Numéro de TVA en Allemagne 

### <a name="format"></a>Format 
Modèle alphanumérique à 11 caractères

### <a name="pattern"></a>Modèle
Modèle alphanumérique à 11 caractères :

- une lettre D ou d
- une lettre E ou e
- espace facultatif
- trois chiffres
- Espace ou virgule facultatif
- trois chiffres
- Espace ou virgule facultatif
- trois chiffres

### <a name="keywords"></a>Mots clés 

#### <a name="keyword_germany_value_added_tax_number"></a>Mot clé\_germany\_value\_added\_tax\_number
```
vat number
vat no
vat#
vat#  mehrwertsteuer
mwst
mehrwertsteuer identifikationsnummer
mehrwertsteuer nummer
```
## <a name="greece-drivers-license-number"></a>Numéro de permis de conduire en Grèce 
neuf chiffres sans espaces ni délimiteurs

### <a name="format"></a>Format 
neuf chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_greece_eu_drivers_license_number"></a>Mots clés\_greece\_eu\_driver's\_license\_number
```
δεια οδήγησης
Adeia odigisis
Άδεια οδήγησης
Δίπλωμα οδήγησης
```
## <a name="greece-national-id-card"></a>Numéro de carte d’identité nationale en Grèce

### <a name="format"></a>Format

Combinaison de 7-8 lettres et chiffres plus un tiret

### <a name="pattern"></a>Modèle

Sept lettres et chiffres (ancien format) :

- Une lettre (n’importe quelle lettre de l’alphabet grec)
- Tiret
- Six chiffres

Huit lettres et chiffres (nouveau format) :

- Deux lettres dont les caractères majuscules se trouvent à la fois dans les alphabets grecs et latins (ABEZHIKMNOPTYX)
- Tiret
- Six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_greece_id_card"></a>Keyword\_greece\_id\_card

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```
## <a name="greece-passport-number"></a>Numéro de passeport en Grèce

### <a name="format"></a>Format 
Deux lettres suivies de sept chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle
Deux lettres suivies de sept chiffres

### <a name="keywords"></a>Mots clés
#### <a name="keywords_eu_passport_number"></a>Mots clé \_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_greece_eu_passport_number"></a>Mots clés\_greece\_eu\_passport\_number
```
αριθμός διαβατηρίου
αριθμούς διαβατηρίου
αριθμός διαβατηριο
```
## <a name="greece-social-security-number-amka"></a>Numéro de sécurité sociale (AMKA) en Grèce

### <a name="format"></a>Format 
11 chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
- Six chiffres comme date de naissance AAMMJJ
- Quatre chiffres
- un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_greece_eu_ssn_or_equivalent"></a>Mots clés\_greece\_eu\_ssn\_or\_equivalent
```
- ssn
- ssn#
- social security no
- socialsecurityno#
- social security number
- amka
- a.m.k.a.
- Αριθμού Μητρώου Κοινωνικής Ασφάλισης
```
## <a name="greece-tax-identification-number"></a>Numéro d’identification fiscale en Grèce

### <a name="format"></a>Format 
Neuf chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
Neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_greece_eu_tax_file_number"></a>Mots clés\_greece\_eu\_tax\_file\_number
```
afm#
afm
aφμ|aφμ αριθμός
aφμ
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax registry no
tax registry number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
taxregistryno#
tin id
tin no
tin#
αριθμός φορολογικού μητρώου
τον αριθμό φορολογικού μητρώου
φορολογικού μητρώου νο
```
## <a name="hong-kong-identity-card-hkid-number"></a>Numéro de carte d’identité à Hong Kong (R.A.S.)

### <a name="format"></a>Format

Combinaison de 8-9 lettres et chiffres plus les parenthèses facultatives autour du caractère final

### <a name="pattern"></a>Modèle

Combinaison de 8-9 lettres :

- 1-2 lettres (ne respectant pas la casse)
- Six chiffres
- Le caractère final (n’importe quel chiffre ou lettre A), qui est le chiffre de vérification et est éventuellement placé entre parenthèses.

### <a name="keywords"></a>Mots clés

#### <a name="keyword_hong_kong_id_card"></a>Keyword\_hong\_kong\_id\_card

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```
## <a name="hungary-drivers-license-number"></a>Numéro de permis de conduire en Hongrie 

### <a name="format"></a>Format 
Deux lettres suivies de six chiffres

### <a name="pattern"></a>Modèle
Deux lettres et six chiffres :

- Deux lettres (ne respectant pas la casse)
- Six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_hungary_eu_drivers_license_number"></a>Mots clés\_hungary\_eu\_driver's\_license\_number
```
vezetoi engedely
vezetői engedély
vezetői engedélyek
```
## <a name="hungary-passport-number"></a>Numéro de passeport en Hongrie 

### <a name="format"></a>Format 
Deux lettres suivies de six ou sept chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle
Deux lettres suivies de six ou sept chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number"></a>Mots clé \_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_hungary_eu_passport_number"></a>Mots clés\_hungary\_eu\_passport\_number
```
útlevél száma
Útlevelek száma
útlevél szám
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
Date de sortie date d’expiration

## <a name="hungary-personal-identification-number"></a>Numéro d’identification personnelle en Hongrie 

### <a name="format"></a>Format 
11 chiffres

### <a name="pattern"></a>Modèle
11 chiffres :

- Un chiffre qui correspond au sexe, 1 pour mâle, 2 pour femelle. D’autres valeurs sont également possibles pour les citoyens nés avant le 1900 ou avec des citoyens à double citoyenneté.
- Six chiffres correspondant à la date de naissance (AAMMJJ)
- Trois chiffres qui correspondent à un numéro de série
- Un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_hungary_eu_national_id_card"></a>Mots clés\_hungary\_eu\_national\_id\_card
```
id number
identification number
sz ig
sz. ig.
sz.ig.
személyazonosító igazolvány
személyi igazolvány
```
## <a name="hungary-social-security-number-taj"></a>Numéro de sécurité sociale de la Hongrie (TAJ) 

### <a name="format"></a>Format 
Neuf chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
Neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_hungary_eu_ssn_or_equivalent"></a>Mots clés\_hungary\_eu\_ssn\_or\_equivalent
```
- hungarian social security number
- social security number
- socialsecuritynumber#
- hssn#
- socialsecuritynno
- hssn
- taj
- taj#
- ssn
- ssn#
- social security no
- áfa
- közösségi adószám
- általános forgalmi adó szám
- hozzáadottérték adó
- áfa szám
- magyar áfa szám
```
## <a name="hungary-value-added-tax-number"></a>Numéro de TVA en Hongrie 

### <a name="format"></a>Format 
Modèle alphanumérique à 10 caractères

### <a name="pattern"></a>Modèle
Modèle alphanumérique à 10 caractères :

- deux lettres - HU ou HU
- espace facultatif
- huit chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_hungary_value_added_tax_number"></a>Mot clé\_Hungary\_value\_added\_tax\_number
```
vat
value added tax number
vat#
vatno#
hungarianvatno#
tax no.
value added tax áfa
közösségi adószám
általános forgalmi adó szám
hozzáadottérték adó
áfa szám
```

## <a name="hungary-tax-identification-number"></a>Numéro d’identification fiscale en Hongrie 

### <a name="format"></a>Format 
10 chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle
10 chiffres :

- Un chiffre qui doit être « 8 »
- Huit chiffres
- Un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_hungary_eu_tax_file_number"></a>Mots clés\_hungary\_eu\_tax\_file\_number
```
adóazonosító szám
adóhatóság szám
adószám
hungarian tin
hungatiantin#
tax authority no
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
vat number
```
## <a name="ip-address"></a>Adresse IP

### <a name="format"></a>Format

#### <a name="ipv4"></a>IPv4 :

Modèle complexe, qui tient compte des versions mises en forme (points) et non mises en forme (absence de point) des adresses IPv4

#### <a name="ipv6"></a>IPv6 :

Modèle complexe qui compte les numéros IPv6 mis en forme (y compris les signes deux-points)

### <a name="pattern"></a>Modèle

### <a name="keywords"></a>Mots clés

#### <a name="keyword_ipaddress"></a>Keyword\_ipaddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Numéro de compte permanent (PAN) en Inde

### <a name="format"></a>Format

10 lettres ou chiffres

### <a name="pattern"></a>Modèle

10 lettres ou chiffres :

- Trois lettres (ne respectant pas la casse)
- Une lettre parmi C, P, H, F, A, T, B, L, J, G (ne respecte pas la casse)
- Une lettre
- Quatre chiffres
- Une lettre (ne respectant pas la casse)

### <a name="keywords"></a>Mots clés

#### <a name="keyword_india_permanent_account_number"></a>Keyword\_india\_permanent\_account\_number

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Numéro d’identification unique (Aadhaar) en Inde

### <a name="format"></a>Format

12 chiffres contenant des espaces ou des tirets facultatifs

### <a name="pattern"></a>Modèle

12 chiffres :

- Un chiffre, qui n’est pas 0 ou 1
- Trois chiffres
- Espace ou tiret facultatif
- Quatre chiffres
- Espace ou tiret facultatif
- Le chiffre final, qui est le chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_india_aadhar"></a>Keyword\_india\_aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>Numéro de carte d’identité (KTP) en Indonésie

### <a name="format"></a>Format

16 chiffres contenant des points facultatifs

### <a name="pattern"></a>Modèle

16 chiffres :

- Code de province à deux chiffres
- Point (facultatif)
- Code de régence ou ville à deux chiffres
- Code de sous-secteur à deux chiffres
- Point (facultatif)
- Six chiffres au format DDMMYY (date de naissance)
- Point (facultatif)
- Quatre chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_indonesia_id_card"></a>Keyword\_indonesia\_id\_card

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>Numéro de compte bancaire international (IBAN)

### <a name="format"></a>Format

Code pays (deux lettres) plus chiffres de vérification (deux chiffres) plus :::no-loc text="bban"::: nombre (jusqu’à 30 caractères)

### <a name="pattern"></a>Modèle

Le modèle doit inclure tous les éléments suivants :

- Code pays à deux lettres
- Deux chiffres de vérification (suivis d’un espace facultatif)
- 1-7 groupes de quatre lettres ou chiffres (peuvent être séparés par des espaces)
- 1-3 lettres ou chiffres

Le format de chaque pays est légèrement différent. Le type d’informations sensibles IBAN couvre les 60 pays suivants :

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Mots clés

Aucun

## <a name="ireland-drivers-license-number"></a>Numéro de permis de conduire en Irlande 

### <a name="format"></a>Format 
Six chiffres suivis de quatre lettres

### <a name="pattern"></a>Modèle
Six chiffres et quatre lettres :

- Six chiffres
- Quatre lettres (ne respectant pas la casse)

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_ireland_eu_drivers_license_number"></a>Mots clés\_ireland\_eu\_driver's\_license\_number
```
ceadúnas tiomána
ceadúnais tiomána 
```
## <a name="ireland-passport-number"></a>Numéro de passeport en Irlande 


### <a name="format"></a>Format 
Deux lettres ou chiffres suivis de sept chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle
Deux lettres ou chiffres suivis de sept chiffres :

- Deux chiffres ou lettres (ne respectant pas la casse)
- Sept chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_ireland_eu_passport_number"></a>Mots clés\_ireland\_eu\_passport\_number
```
passeport numero
uimhreacha pasanna
uimhir pas
uimhir phas
uimhreacha pas
uimhir cárta
uimhir chárta
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="ireland-personal-public-service-pps-number"></a>Numéro de service public personnel (PPS) en Irlande

### <a name="format"></a>Format

Ancien format (jusqu’au 31 décembre 2012) :

- sept chiffres suivis de 1-2 lettres

Nouveau format (à partir du 1er janvier 2013) :

- sept chiffres suivis de deux lettres

### <a name="pattern"></a>Modèle

Ancien format (jusqu’au 31 décembre 2012) :

- sept chiffres
- une à deux lettres (sans respect de la casse)

Nouveau format (à partir du 1er janvier 2013) :

- sept chiffres
- lettre (ne respectant pas la casse) qui est un chiffre de vérification alphabétique
- Une lettre facultative dans la plage A-I ou &quot;W&quot;

### <a name="keywords"></a>Mots clés

#### <a name="keywords_ireland_eu_national_id_card"></a>Keywords\_ireland\_eu\_national\_id\_card

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>Numéro de compte bancaire en Israël

### <a name="format"></a>Format

13 chiffres

### <a name="pattern"></a>Modèle

Mis en forme :

- deux chiffres
- tiret
- trois chiffres
- tiret
- huit chiffres

Sans mise en forme :

- 13 chiffres consécutifs

### <a name="keywords"></a>Mots clés

#### <a name="keyword_israel_bank_account_number"></a>Keyword\_israel\_bank\_account\_number

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>Numéro d’identification nationale en Israël

### <a name="format"></a>Format

neuf chiffres

### <a name="pattern"></a>Modèle

neuf chiffres consécutifs

### <a name="keywords"></a>Mots clés

#### <a name="keyword_israel_national_id"></a>Keyword\_Israel\_National\_ID

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>Numéro de permis de conduire en Italie

Cette entité de type d’informations sensibles est incluse dans le type d’information sensible Numéro de permis de conduire européen et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

combinaison de 10 lettres et chiffres

### <a name="pattern"></a>Modèle

combinaison de 10 lettres et chiffres :

- une lettre (ne respectant pas la casse)
- la lettre &quot;A&quot; ou &quot;V&quot; (sans respect de la casse)
- sept chiffres
- une lettre (ne respectant pas la casse)

### <a name="keywords"></a>Mots clés

#### <a name="keyword_italy_drivers_license_number"></a>Keyword\_italy\_drivers\_license\_number

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```
## <a name="italy-fiscal-code"></a>Code fiscal en Italie 

### <a name="format"></a>Format 
combinaison de 16 caractères de lettres et de chiffres dans le modèle spécifié

### <a name="pattern"></a>Modèle
combinaison de 16 caractères de lettres et de chiffres :

- trois lettres qui correspondent aux trois premières consonnes dans le nom de famille
- trois lettres qui correspondent à la première, troisième et quatrième consonnes dans le prénom
- deux chiffres qui correspondent aux derniers chiffres de l’année de naissance
- une lettre qui correspond à la lettre du mois de naissance : les lettres sont utilisées par ordre alphabétique, mais seules les lettres A à E, H, L, M, P, R à T sont utilisées (par conséquent, janvier est A et octobre est R)
- deux chiffres qui correspondent au jour du mois de naissance afin de faire la distinction entre les sexes, 40 est ajouté au jour de naissance pour les femmes
- quatre chiffres correspondant à l’indicatif régional propre à la commune où la personne est née (les codes à l’ensemble du pays sont utilisés pour les pays étrangers)
- un chiffre de parité 

### <a name="keywords"></a>Mots clés

#### <a name="keywords_italy_eu_national_id_card"></a>Mots clés\_italy\_eu\_national\_id\_card
```
codice fiscal
codice fiscale
codice id personale
codice personale
fiscal code
numero certificato personale
numero di identificazione fiscale
numero id personale
numero personale
personal certificate number
personal code
personal id code
personal id number
personalcodeno#
tax code
tax id
tax identification no
tax identification number
tax identity number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="italy-passport-number"></a>Numéro de passeport en Italie 

### <a name="format"></a>Format 
deux lettres ou chiffres suivis de sept chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle
deux lettres ou chiffres suivis de sept chiffres :

- deux chiffres ou lettres (ne respectant pas la casse)
- sept chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_italy_eu_passport_number"></a>Mots clés\_italy\_eu\_passport\_number
```
italiana passaporto
passaporto italiana
passaporto numero
numéro passeport
numero di passaporto
numeri del passaporto
passeport italien
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="italy-value-added-tax"></a>Numéro de TVA en Italie 

### <a name="format"></a>Format 
modèle alphanumérique de 13 caractères avec délimiteurs facultatifs

### <a name="pattern"></a>Modèle
modèle alphanumérique de 13 caractères avec délimiteurs facultatifs :

- I ou i
- T ou t
- espace, point, trait d’union ou virgule facultatif
- 11 chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_italy_value_added_tax_number"></a>Mot clé\_italy\_value\_added\_tax\_number
```
vat number
vat no
vat#
iva
iva#
```
## <a name="japan-bank-account-number"></a>Numéro de compte bancaire au Japon

### <a name="format"></a>Format

sept ou huit chiffres

### <a name="pattern"></a>Modèle

Numéro de compte bancaire :

- sept ou huit chiffres
- Code de la succursale du compte bancaire :
- quatre chiffres
- espace ou tiret (facultatif)
- trois chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_jp_bank_account"></a>Keyword\_jp\_bank\_account

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Keyword\_jp\_bank\_branch\_code

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Numéro de permis de conduire au Japon

### <a name="format"></a>Format

12 chiffres

### <a name="pattern"></a>Modèle

12 chiffres consécutifs

### <a name="keywords"></a>Mots clés

#### <a name="keyword_jp_drivers_license_number"></a>Keyword\_jp\_drivers\_license\_number

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```
## <a name="japanese-my-number--corporate"></a>« Mon numéro » japonais - Entreprise 


### <a name="format"></a>Format 
nombre à 13 chiffres

### <a name="pattern"></a>Modèle
nombre à 13 chiffres :

- un chiffre entre un et neuf
- 12 chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_japan_my_number_corporate"></a>Mot clé\_japan\_my\_number\_corporate
```
corporate number
マイナンバー
共通番号
マイナンバーカード
マイナンバーカード番号
個人番号カード
個人識別番号
個人識別ナンバー
法人番号
指定通知書
```
## <a name="japanese-my-number--personal"></a>« Mon numéro » japonais - Personnel 


### <a name="format"></a>Format 
nombre à 12 chiffres

### <a name="pattern"></a>Modèle
nombre à 12 chiffres :

- quatre chiffres
- un espace, un point ou un trait d’union facultatif
- quatre chiffres
- un espace, un point ou un trait d’union facultatif
- quatre chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_japan_my_number_personal"></a>Mot clé\_japan\_my\_number\_personal
```
my number
マイナンバー
個人番号
共通番号
マイナンバーカード
マイナンバーカード番号
個人番号カード
個人識別番号
個人識別ナンバー
通知カード
```
## <a name="japan-passport-number"></a>Numéro de passeport au Japon

### <a name="format"></a>Format

deux lettres suivies de sept chiffres

### <a name="pattern"></a>Modèle

deux lettres (ne respectant pas la casse) suivies de sept chiffres

#### <a name="keyword_jp_passport"></a>Keyword\_jp\_passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Numéro de carte de résidence au Japon

### <a name="format"></a>Format

12 lettres et chiffres

### <a name="pattern"></a>Modèle

12 lettres et chiffres :

- deux lettres (ne respectant pas la casse)
- huit chiffres
- deux lettres (ne respectant pas la casse)

### <a name="keywords"></a>Mots clés

#### <a name="keyword_jp_residence_card_number"></a>Keyword\_jp\_residence\_card\_number

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Numéro d’inscription de résident au Japon

### <a name="format"></a>Format

11 chiffres

### <a name="pattern"></a>Modèle

11 chiffres consécutifs

### <a name="keywords"></a>Mots clés

#### <a name="keyword_jp_resident_registration_number"></a>Keyword\_jp\_resident\_registration\_number

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Numéro d’assurance sociale (SIN) au Japon

### <a name="format"></a>Format

7-12 chiffres

### <a name="pattern"></a>Modèle

7-12 chiffres :

- quatre chiffres
- un trait d’union (facultatif)
- six chiffres OU
- 7-12 chiffres consécutifs

### <a name="keywords"></a>Mots clés

#### <a name="keyword_jp_sin"></a>Keyword\_jp\_sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```
## <a name="latvia-drivers-license-number"></a>Numéro de permis de conduire en Lettonie

### <a name="format"></a>Format
Trois lettres suivies de six chiffres

### <a name="pattern"></a>Modèle
trois lettres et six chiffres :

- Trois lettres (ne respectant pas la casse)
- six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_latvia_eu_drivers_license_number"></a>Mots clés\_latvia\_eu\_driver's\_license\_number
```
autovadītāja apliecība
autovadītāja apliecības
vadītāja apliecība
```
## <a name="latvia-passport-number"></a>Numéro de passeport en Lettonie 

### <a name="format"></a>Format 
deux lettres ou chiffres suivis de sept chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle
deux lettres ou chiffres suivis de sept chiffres :

- deux chiffres ou lettres (ne respectant pas la casse)
- sept chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_latvia_eu_passport_number"></a>Mots clés\_latvia\_eu\_passport\_number
```
pase numurs
pase numur
pases numuri
pases nr
passeport no
n° du Passeport
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="latvia-personal-code-personas-kods-or-pic"></a>Code personnel de Lettonie (personas kods ou PIC)  

### <a name="format"></a>Format 
11 chiffres et un trait d’union facultatif

### <a name="pattern"></a>Modèle
Ancien format

11 chiffres et un trait d’union :

- six chiffres correspondant à la date de naissance (DDMMYY)
- trait d’union
- un chiffre qui correspond au siècle de naissance (« 0 » pour le 19e siècle, « 1 » pour le 20e siècle et « 2 » pour le 21e siècle)
- quatre chiffres, générés de manière aléatoire
- Nouveau format

11 chiffres

- Deux chiffres « 32 »
- Neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_latvia_eu_national_id_card"></a>Mots clés\_latvia\_eu\_national\_id\_card
```
administrative number
alvas nē
birth number
citizen number
civil number
electronic census number
electronic number
fiscal code
healthcare user number
id#
id-code
identification number
identifikācijas numurs
id-number
individual number
latvija alva
nacionālais id
national id
national identifying number
national identity number
national insurance number
national register number
nodokļa numurs
nodokļu id
nodokļu identifikācija numurs
personal certificate number
personal code
personal id code
personal id number
personal identification code
personal identifier
personal identity number
personal number
personal numeric code
personalcodeno#
personas kods
population identification code
public service number
registration number
revenue number
social insurance number
social security number
state tax code
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
voter’s number
```
## <a name="lithuania-drivers-license-number"></a>Numéro de permis de conduire en Lituanie 

### <a name="format"></a>Format 
huit chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
huit chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_lithuania_eu_drivers_license_number"></a>Mots clés\_lithuania\_eu\_driver's\_license\_number
```
vairuotojo pažymėjimas
vairuotojo pažymėjimo numeris
vairuotojo pažymėjimo numeriai
```
## <a name="lithuania-passport-number"></a>Numéro de passeport en Lituanie 

### <a name="format"></a>Format 
huit chiffres ou lettres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle
huit chiffres ou lettres (ne respectant pas la casse)

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number"></a>Mots clé \_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_lithuania_eu_passport_number"></a>Mots clés\_lithuania\_eu\_passport\_number
```
paso numeris
paso numeriai
paso nr
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="lithuania-personal-code-asmens-kodas"></a>Code personnel de la Lituanie (asmens kodas)  

### <a name="format"></a>Format 
11 chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
11 chiffres sans espaces ni délimiteurs :

- un chiffre (1-6) qui correspond au sexe et au siècle de la personne
- Six chiffres correspondant à la date de naissance (AAMMJJ)
- trois chiffres correspondant au numéro de série de la date de naissance
- un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_lithuania_eu_national_id_card"></a>Mots clés\_lithuania\_eu\_national\_id\_card
```
asmeninis skaitmeninis kodas
asmens kodas
citizen service number
mokesčių id
mokesčių identifikavimas numeris
mokesčių identifikavimo numeris
mokesčių numeris
national identification number
personal code
personal numeric code
piliečio paslaugos numeris
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
unikalus identifikavimo kodas
unikalus identifikavimo numeris
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="luxembourg-drivers-license-number"></a>Numéro de permis de conduire au Luxembourg 

### <a name="format"></a>Format 
Six chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_luxemburg_eu_drivers_license_number"></a>Mots clés\_luxemburg\_eu\_driver's\_license\_number
```
fahrerlaubnis
Führerschäin
```
## <a name="luxembourg-passport-number"></a>Numéro de passeport au Luxembourg 

### <a name="format"></a>Format 
huit chiffres ou lettres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle
huit chiffres ou lettres (ne respectant pas la casse)

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number"></a>Mots clé \_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_luxemburg_eu_passport_number"></a>Mots clés\_luxemburg\_eu\_passport\_number
```
ausweisnummer
luxembourg pass
luxembourg passeport
luxembourg passport
no de passeport
no-reisepass
nr-reisepass
numéro de passeport
pass net
pass nr
passnummer
passeport nombre
reisepässe
reisepass-nr
reisepassnummer
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="luxemburg-national-identification-number---natural-persons"></a>Numéro d’identification nationale au Luxembourg - personnes physiques

### <a name="format"></a>Format 
13 chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle
13 chiffres :

- 11 chiffres
- deux chiffres de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_luxemburg_eu_national_id_card"></a>Mots clés\_luxemburg\_eu\_national\_id\_card
```
eindeutige id
eindeutige id-nummer
eindeutigeid#
id personnelle
idpersonnelle#
idpersonnelle
individual code
individual id
individual identification
individual identity
numéro d'identification personnel
personal id
personal identification
personal identity
personalidno#
personalidnumber#
persönliche identifikationsnummer
unique id
unique identity
uniqueidkey#
```
## <a name="luxemburg-national-identification-number---non-natural-persons"></a>Numéro d’identification nationale au Luxembourg - personnes morales

### <a name="format"></a>Format 
11 chiffres

### <a name="pattern"></a>Modèle
11 chiffres

- deux chiffres
- espace facultatif
- trois chiffres
- espace facultatif
- trois chiffres
- espace facultatif
- deux chiffres
- un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_luxemburg_eu_tax_file_number"></a>Mots clés\_luxemburg\_eu\_tax\_file\_number
```
carte de sécurité sociale
étain non
étain#
identifiant d'impôt
luxembourg tax identifikatiounsnummer
numéro d'étain
numéro d'identification fiscal luxembourgeois
numéro d'identification fiscale
social security
sozialunterstützung
sozialversécherung
sozialversicherungsausweis
steier id
steier identifikatiounsnummer
steier nummer
steuer id
steueridentifikationsnummer
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
zinn#
zinn
zinnzahl
```
## <a name="malaysia-identification-card-number"></a>Numéro de carte d’identification de Malaisie

### <a name="format"></a>Format

12 chiffres contenant des tirets facultatifs

### <a name="pattern"></a>Modèle

12 chiffres :

- six chiffres au format YYMMDD (date de naissance)
- tiret (facultatif)
- code de lieu de naissance à deux lettres
- tiret (facultatif)
- trois chiffres aléatoires
- code de sexe à un chiffre

### <a name="keywords"></a>Mots clés

#### <a name="keyword_malaysia_id_card_number"></a>Keyword\_malaysia\_id\_card\_number

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```
## <a name="malta-drivers-license-number"></a>Numéro de permis de conduire à Malte 

### <a name="format"></a>Format 
Combinaison de deux caractères et de six chiffres dans le modèle spécifié

### <a name="pattern"></a>Modèle
combinaison de deux caractères et de six chiffres :

- deux caractères (chiffres ou lettres, ne respectant pas la casse)
- espace (facultatif)
- trois chiffres
- espace (facultatif)
- trois chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_malta_eu_drivers_license_number"></a>Mots clés\_malta\_eu\_driver's\_license\_number
```
liċenzja tas-sewqan
liċenzji tas-sewwieq
```
## <a name="malta-identity-card-number"></a>Numéro de carte d’identité à Malte 

### <a name="format"></a>Format 
sept chiffres suivis d’une lettre

### <a name="pattern"></a>Modèle
sept chiffres suivis d’une lettre :

- sept chiffres
- une lettre « M, G, A, P, L, H, B, Z » (non-respect de la casse)

### <a name="keywords"></a>Mots clés

#### <a name="keywords_malta_eu_national_id_card"></a>Mots clés\_malta\_eu\_national\_id\_card
```
citizen service number
id tat-taxxa
identifika numru tal-biljett
kodiċi numerali personali
numru ta 'identifikazzjoni personali
numru ta 'identifikazzjoni tat-taxxa
numru ta 'identifikazzjoni uniku
numru ta' identità uniku
numru tas-servizz taċ-ċittadin
numru tat-taxxa
personal numeric code
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="malta-passport-number"></a>Numéro de passeport à Malte 

### <a name="format"></a>Format 
sept chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle
sept chiffres

### <a name="keywords"></a>Mots clés
#### <a name="keywords_eu_passport_number"></a>Mots clé \_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_malta_eu_passport_number"></a>Mots clés\_malta\_eu\_passport\_number
```
numru tal-passaport
numri tal-passaport
Nru tal-passaport
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="malta-tax-id-number"></a>Numéro d’identification fiscal à Malte 

### <a name="format"></a>Format 
Pour les ressortissants de Malte :

- sept chiffres et une lettre dans le modèle spécifié

Ressortissants non-maltais et entités maltaises :

- neuf chiffres

### <a name="pattern"></a>Modèle

Ressortissants maltais : sept chiffres et une lettre

- sept chiffres
- une lettre (ne respectant pas la casse)

Ressortissants non-maltais et entités maltaises : neuf chiffres

- neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_malta_eu_tax_file_number"></a>Mots clés\_malta\_eu\_tax\_file\_number
```
citizen service number
id tat-taxxa
identifika numru tal-biljett
kodiċi numerali personali
numru ta 'identifikazzjoni personali
numru ta 'identifikazzjoni tat-taxxa
numru ta 'identifikazzjoni uniku
numru ta' identità uniku
numru tas-servizz taċ-ċittadin
numru tat-taxxa
personal numeric code
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="netherlands-citizens-service-bsn-number"></a>Numéro de service citoyen (BSN) au Pays-Bas

### <a name="format"></a>Format

huit à neuf chiffres contenant des espaces facultatifs

### <a name="pattern"></a>Modèle

huit à neuf chiffres :

- trois chiffres
- espace (facultatif)
- trois chiffres
- espace (facultatif)
- deux à trois chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_netherlands_eu_national_id_card"></a>Keywords\_netherlands\_eu\_national\_id\_card

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="netherlands-drivers-license-number"></a>Numéro de permis de conduire au Pays-bas 

### <a name="format"></a>Format 

10 chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle

10 chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_netherlands_eu_drivers_license_number"></a>Mots clés\_netherlands\_eu\_driver's\_license\_number
```
permis de conduire
rijbewijs
rijbewijsnummer
rijbewijzen
rijbewijs nummer
rijbewijsnummers
```

## <a name="netherlands-passport-number"></a>Numéro de passeport aux Pays-bas 

### <a name="format"></a>Format 

neuf lettres ou chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle

Neuf lettres ou chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number"></a>Mots clé \_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_netherlands_eu_passport_number"></a>Mots clés\_netherlands\_eu\_passport\_number
```
paspoort nummer
paspoortnummers
paspoortnummer
paspoort nr
```
## <a name="netherlands-tax-identification-number"></a>Numéro d’identification fiscale aux Pays-Bas 

### <a name="format"></a>Format 

neuf chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle

neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_netherlands_eu_tax_file_number"></a>Mots clés\_netherlands\_eu\_tax\_file\_number
```
btw nummer
hollânske tax identification
hulandes impuesto id number
hulandes impuesto identification
identificatienummer belasting
identificatienummer van belasting
impuesto identification number
impuesto number
nederlands belasting id nummer
nederlands belasting identificatie
nederlands belasting identificatienummer
nederlands belastingnummer
nederlandse belasting identificatie
netherlands tax identification
netherland's tax identification
netherlands tin
netherland's tin
tax id
tax identification no
tax identification number
tax identification tal
tax no#
tax no
tax number
tax registration number
tax tal
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="netherlands-value-added-tax-number"></a>Numéro de TVA aux Pays-Bas 

### <a name="format"></a>Format 

Modèle alphanumérique à 14 caractères

### <a name="pattern"></a>Modèle

Modèle alphanumérique à 14 caractères :

- N ou n
- L ou l
- espace, un point ou un trait d’union facultatif
- neuf chiffres
- espace, un point ou un trait d’union facultatif
- B ou b
- deux chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_netherlands_value_added_tax_number"></a>Mot clé\_netherlands\_value\_added\_tax\_number
```
vat number
vat no
vat#
wearde tafoege tax getal
btw nûmer
btw-nummer
```
## <a name="new-zealand-bank-account-number"></a>Numéro de compte bancaire en Nouvelle-Zélande 

### <a name="format"></a>Format 

modèle de 14 à 16 chiffres avec délimiteur facultatif

### <a name="pattern"></a>Modèle

modèle de 14 à 16 chiffres avec délimiteur facultatif :

- deux chiffres
- un trait d’union ou un espace facultatif
- trois à quatre chiffres
- un trait d’union ou un espace facultatif
- sept chiffres
- un trait d’union ou un espace facultatif
- deux à trois chiffres
- un trait d’union ou un espace

### <a name="keywords"></a>Mots clés

#### <a name="keyword_new_zealand_bank_account_number"></a>Mot clé\_new\_zealand\_bank\_account\_number
```
account number
bank account
bank_acct_id
bank_acct_branch
bank_acct_nbr
```
## <a name="new-zealand-driver-license"></a>Permis de conduire de Nouvelle-Zélande 

### <a name="format"></a>Format 

Modèle alphanumérique à 8 caractères

### <a name="pattern"></a>Modèle

Modèle alphanumérique à 8 caractères

- deux lettres
- six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_new_zealand_drivers_license_number"></a>Mot clé\_new\_zealand\_drivers\_license\_number
```
driverlicence
driverlicences
driver lic
driver licence
driver licences
driverslic
driverslicence
driverslicences
drivers lic
drivers lics
drivers licence
drivers licences
driver'lic
driver'lics
driver'licence
driver'licences
driver' lic
driver' lics
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's licence
driver's licences
driverlic#
driverlics#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver licence#
driver licences#
driverslic#
driverslics#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's licence#
driver's licences#
international driving permit
international driving permits
nz automobile association
new zealand automobile association
```
## <a name="new-zealand-inland-revenue-number"></a>Numéro Inland Revenue Department (IRD) en Nouvelle-Zélande 

### <a name="format"></a>Format 

huit ou neuf chiffres avec des délimiteurs facultatifs

### <a name="pattern"></a>Modèle

huit ou neuf chiffres avec des délimiteurs facultatifs

- deux ou trois chiffres
- un espace ou trait d’union facultatif
- trois chiffres
- un espace ou trait d’union facultatif
- trois chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_new_zealand_inland_revenue_number"></a>Mot clé\_new\_zealand\_inland\_revenue\_number
```
ird no.
ird no#
nz ird
new zealand ird
ird number
inland revenue number
```
## <a name="new-zealand-social-welfare-number"></a>Numéro de protection sociale en Nouvelle-Zélande

### <a name="format"></a>Format

neuf chiffres

### <a name="pattern"></a>Modèle

neuf chiffres

- trois chiffres
- un trait d’union facultatif
- trois chiffres
- un trait d’union facultatif
- trois chiffres

#### <a name="keyword_new_zealand_social_welfare_number"></a>Mot clé\_new\_zealand\_social\_welfare\_number
```
social welfare #
social welfare#
social welfare No.  
social welfare number
swn#
```
## <a name="new-zealand-ministry-of-health-number"></a>Numéro du ministère de la santé en Nouvelle-Zélande

### <a name="format"></a>Format

trois lettres, un espace (facultatif) et quatre chiffres

### <a name="pattern"></a>Modèle

- trois lettres (ne respectant pas la casse) sauf « I » et « O »
- espace (facultatif)
- quatre chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_nz_terms"></a>Keyword\_nz\_terms

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>Numéro d’identification de la Norvège

### <a name="format"></a>Format

11 chiffres

### <a name="pattern"></a>Modèle

11 chiffres :

- six chiffres au format DDMMYY (date de naissance)
- nombre individuel à trois chiffres
- deux chiffres de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_norway_id_number"></a>Keyword\_norway\_id\_number

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Numéro d’identité polyvalent unifié aux Philippines

### <a name="format"></a>Format

12 chiffres séparés par des traits d’union

### <a name="pattern"></a>Modèle

12 chiffres :

- quatre chiffres
- trait d’union
- sept chiffres
- trait d’union
- un chiffre

### <a name="keywords"></a>Mots clés

#### <a name="keyword_philippines_id"></a>Keyword\_philippines\_id

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-drivers-license-number"></a>Numéro de permis de conduire en Pologne

### <a name="format"></a>Format

14 chiffres contenant deux barres obliques

### <a name="pattern"></a>Modèle

14 chiffres et deux barres obliques :

- cinq chiffres
- barre oblique
- deux chiffres
- barre oblique
- sept chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_poland_eu_drivers_license_number"></a>Mots clés\_poland\_eu\_driver's\_license\_number
```
prawo jazdy
prawa jazdy
```

## <a name="poland-identity-card"></a>Carte d’identité en Pologne

### <a name="format"></a>Format

trois lettres et six chiffres

### <a name="pattern"></a>Modèle

trois lettres (ne respectant pas la casse) suivies de six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>Numéro d’identité nationale (PESEL) en Pologne

### <a name="format"></a>Format

11 chiffres

### <a name="pattern"></a>Modèle

- 6 chiffres représentant la date de naissance au format AAMMJJ
- 4 chiffres
- 1 chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keyword_pesel_identification_number"></a>Keyword\_pesel\_identification\_number

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Numéro de passeport en Pologne

Cette entité de type d’informations sensibles est incluse dans le type d’informations sensibles Numéro de passeport européen et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

deux lettres et sept chiffres

### <a name="pattern"></a>Modèle

Deux lettres (ne respectant pas la casse) suivies de sept chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="poland-regon-number"></a>Numéro REGON en Pologne

### <a name="format"></a>Format

nombre à 9 chiffres ou 14 chiffres

### <a name="pattern"></a>Modèle

nombre à 9 chiffres ou 14 chiffres :

- neuf chiffres ou
- neuf chiffres
- trait d’union
- cinq chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_poland_regon_number"></a>Mots clés\_poland\_regon\_number
```
regon id
statistical number
statistical id
statistical no
regon number
regonid#
regonno#
company id
companyid#
companyidno#
numer statystyczny
numeru regon
numerstatystyczny#
numeruregon#
```
## <a name="poland-tax-identification-number"></a>Numéros d’identification fiscale en Pologne

### <a name="format"></a>Format

11 chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle

11 chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_poland_eu_tax_file_number"></a>Mots clés\_poland\_eu\_tax\_file\_number
```
nip#
nip
numer identyfikacji podatkowej
numeridentyfikacjipodatkowej#
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
vat id#
vat id
vat no
vat number
vatid#
vatid
vatno#
```
## <a name="portugal-citizen-card-number"></a>Numéro de carte de citoyen au Portugal

### <a name="format"></a>Format

huit chiffres

### <a name="pattern"></a>Modèle

huit chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_portugal_citizen_card"></a>Keyword\_portugal\_citizen\_card

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Numéro de permis de conduire au Portugal

Cette entité de type d’informations sensibles est uniquement disponible dans le type d’informations sensibles Numéro de licence de conducteur européen.

### <a name="format"></a>Format

deux modèles : deux lettres suivies de 5-8 chiffres avec des caractères spéciaux

### <a name="pattern"></a>Modèle

Modèle 1 : Deux lettres suivies de 5/6 avec des caractères spéciaux :

- Deux lettres (ne respectant pas la casse)
- Un trait d’union
- Cinq ou six chiffres
- Un espace
- Un chiffre

Modèle 2 : Une lettre suivie de 6/8 chiffres avec des caractères spéciaux :

- Une lettre (ne respectant pas la casse)
- Un trait d’union
- Six ou huit chiffres
- Un espace
- Un chiffre

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Keywords\_portugal\_eu\_driver's\_license\_number

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```
## <a name="portugal-passport-number"></a>Numéro de passeport au Portugal

### <a name="format"></a>Format

une lettre suivie de six chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle

une lettre suivie de six chiffres :

- une lettre (ne respectant pas la casse)
- six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number"></a>Mots clé \_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_portugal_eu_passport_number"></a>Mots clés\_portugal\_eu\_passport\_number
```
número do passaporte
portuguese passport
portuguese passeport
portuguese passaporte
passaporte nº
passeport nº
números de passaporte
portuguese passports
número passaporte
números passaporte
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```

## <a name="portugal-tax-identification-number"></a>Numéro d’identification fiscale au Portugal

### <a name="format"></a>Format

neuf chiffres avec espaces facultatifs

### <a name="pattern"></a>Modèle

- trois chiffres
- espace facultatif
- trois chiffres
- espace facultatif
- trois chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_portugal_eu_tax_file_number"></a>Mots clés\_portugal\_eu\_tax\_file\_number
```
cpf#
cpf
nif#
nif
número de identificação fisca
numero fiscal
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="romania-drivers-license-number"></a>Numéro de permis de conduire en Roumanie

### <a name="format"></a>Format

un caractère suivi de huit chiffres

### <a name="pattern"></a>Modèle

un caractère suivi de huit chiffres :

- une lettre (ne respectant pas la casse) ou chiffre
- huit chiffres

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_romania_eu_drivers_license_number"></a>Mots clés\_romania\_eu\_driver's\_license\_number
```
permis de conducere
permisului de conducere
permisului conducere
permisele de conducere
permisele conducere
permis conducere
```

## <a name="romania-passport-number"></a>Numéro de passeport en Roumanie

### <a name="format"></a>Format

Huit ou neuf chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle

huit ou neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number"></a>Mots clés \_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_romania_eu_passport_number"></a>Mots clés\_romania\_eu\_passport\_number
```
numărul pașaportului numarul pasaportului numerele pașaportului Pașaport nr
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```

## <a name="romania-personal-numeric-code-cnp"></a>Code numérique personnel (CNP) en Roumanie

### <a name="format"></a>Format

13 chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle

- un chiffre de 1 à 9
- six chiffres représentant la date de naissance (AAMMJJ)
- deux chiffres, qui peuvent être 01-52 ou 99
- quatre chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_romania_eu_national_id_card"></a>Mots clés\_romania\_eu\_national\_id\_card
```
cnp#
cnp
cod identificare personal
cod numeric personal
cod unic identificare
codnumericpersonal#
codul fiscal nr.
identificarea fiscală nr#
id-ul taxei
insurance number
insurancenumber#
national id#
national id
national identification number
număr identificare personal
număr identitate
număr personal unic
număridentitate#
număridentitate
numărpersonalunic#
numărpersonalunic
număru de identificare fiscală
numărul de identificare fiscală
personal numeric code
pin#
pin
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
unique identification number
unique identity number
uniqueidentityno#
uniqueidentityno
```

## <a name="russia-passport-number-domestic"></a>Numéro de passeport domestique en Russie

### <a name="format"></a>Format

nombre à 10 chiffres

### <a name="pattern"></a>Modèle

nombre à 10 chiffres :

- deux chiffres
- un espace ou trait d’union facultatif
- deux chiffres
- espace facultatif
- six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_russia_passport_number_domestic"></a>Mot clé\_russia\_passport\_number\_domestic
```
passport number
passport no
passport #
passport id
passportno#
passportnumber#
паспорт нет
паспорт id
pоссийской паспорт
pусский номер паспорта
паспорт#
паспортid#
номер паспорта
номерпаспорта#
```
## <a name="russia-passport-number-international"></a>Numéro de passeport international en Russie

### <a name="format"></a>Format

nombre à neuf chiffres

### <a name="pattern"></a>Modèle

nombre à neuf chiffres :

- deux chiffres
- un espace ou trait d’union facultatif
- sept chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_russia_passport_number_international"></a>Mots clés\_russia\_passport\_number\_international
```
passport number
passport no
passport #
passport id
passportno#
passportnumber#
паспорт нет
паспорт id
pоссийской паспорт
pусский номер паспорта
паспорт#
паспортid#
номер паспорта
номерпаспорта#
```

## <a name="saudi-arabia-national-id"></a>Numéro national en Arabie saoudite

### <a name="format"></a>Format

10 chiffres

### <a name="pattern"></a>Modèle

10 chiffres consécutifs

### <a name="keywords"></a>Mots clés

#### <a name="keyword_saudi_arabia_national_id"></a>Keyword\_saudi\_arabia\_national\_id

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>Numéro de carte d’identité d’inscription nationale (NRIC) à Singapour

### <a name="format"></a>Format

neufs lettres et chiffres

### <a name="pattern"></a>Modèle

- neufs lettres et chiffres :
- lettre &quot;F&quot;, &quot;G&quot;, &quot;S&quot; ou &quot;T&quot; (sans respect de la casse)
- sept chiffres
- chiffre de vérification alphabétique

### <a name="keywords"></a>Mots clés

#### <a name="keyword_singapore_nric"></a>Keyword\_singapore\_nric

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```
## <a name="slovakia-drivers-license-number"></a>Numéro de permis de conduire en Slovaquie

### <a name="format"></a>Format

un caractère suivi de sept chiffres

### <a name="pattern"></a>Modèle

un caractère suivi de sept chiffres

- une lettre (ne respectant pas la casse) ou chiffre
- sept chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_slovakia_eu_drivers_license_number"></a>Mots clés\_slovakia\_eu\_driver's\_license\_number
```
vodičský preukaz
vodičské preukazy
vodičského preukazu
vodičských preukazov
```
## <a name="slovakia-personal-number"></a>Numéro personnel en Slovaquie

### <a name="format"></a>Format
neuf ou 10 chiffres contenant une barre oblique inverse facultative

### <a name="pattern"></a>Modèle
- six chiffres représentant la date de naissance
- barre oblique (/) facultative
- trois chiffres
- un chiffre de vérification facultatif

### <a name="keywords"></a>Mots clés

#### <a name="keywords_slovakia_eu_national_id_card"></a>Mots clés\_slovakia\_eu\_national\_id\_card
```
azonosító szám
birth number
číslo národnej identifikačnej karty
číslo občianského preukazu
daňové číslo
id number
identification no
identification number
identifikačná karta č
identifikačné číslo
identity card no
identity card number
národná identifikačná značka č
national number
nationalnumber#
nemzeti személyazonosító igazolvány
personalidnumber#
rč
rodne cislo
rodné číslo
social security number
ssn#
ssn
személyi igazolvány szám
személyi igazolvány száma
személyigazolvány szám
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="slovenia-drivers-license-number"></a>Numéro de permis de conduire en Slovénie

### <a name="format"></a>Format

neuf chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle

neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_slovenia_eu_drivers_license_number"></a>Mots clés\_slovenia\_eu\_driver's\_license\_number
```
vozniško dovoljenje
vozniška številka licence
vozniških dovoljenj
številka vozniškega dovoljenja
številke vozniških dovoljenj
```
## <a name="slovenia-passport-number"></a>Numéro de passeport en Slovénie

### <a name="format"></a>Format

deux lettres suivies de sept chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle

deux lettres suivies de sept chiffres :

- la lettre « P »
- une lettre majuscule
- sept chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number"></a>Mots clés \_eu\_passport\_number
```
- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- passport number
- passportnumbers
- passport numbers
```
#### <a name="keywords_slovenia_eu_passport_number"></a>Mots clés\_slovenia\_eu\_passport\_number
```
- številka potnega lista
- potek veljavnosti
- potni list#
- datum rojstva
- potni list
- številke potnih listov
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
- date of issue
- date of expiry
```
## <a name="slovenia-tax-identification-number"></a>Numéro d’identification fiscale en Slovénie

### <a name="format"></a>Format

Huit chiffres sans espaces ni séparateurs

### <a name="pattern"></a>Modèle

- Un chiffre de 1 à 9
- six chiffres
- un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_slovenia_eu_tax_file_number"></a>Mots clés\_slovenia\_eu\_tax\_file\_number
```
davčna številka
identifikacijska številka davka
številka davčne datoteke
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```

## <a name="slovenia-unique-master-citizen-number"></a>Numéro d’identification nationale de Slovénie de Slovénie

### <a name="format"></a>Format

13 chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle

13 chiffres selon le modèle spécifié :

- sept chiffres correspondant à la date de naissance (DDMMLLL) où « LLL » correspond aux trois derniers chiffres de l’année de naissance
- deux chiffres correspondant à la zone de naissance « 50 »
- trois chiffres correspondant à une combinaison de sexe et de numéro de série pour les personnes nées le même jour. 000-499 pour un homme et 500-999 pour une femme.
- un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_slovenia_eu_national_id_card"></a>Mots clés\_slovenia\_eu\_national\_id\_card
```
edinstvena številka glavnega državljana
emšo
enotna maticna številka obcana
id card
identification number
identifikacijska številka
identity card
nacionalna id
nacionalni potni list
national id
osebna izkaznica
osebni koda
osebni ne
osebni številka
personal code
personal number
personal numeric code
številka državljana
unique citizen number
unique id number
unique identity number
unique master citizen number
unique registration number
uniqueidentityno #
uniqueidentityno#
```

## <a name="south-africa-identification-number"></a>Numéro d’identification en Afrique du Sud

### <a name="format"></a>Format

13 chiffres pouvant contenir des espaces

### <a name="pattern"></a>Modèle

13 chiffres :

- six chiffres au format YYMMDD (date de naissance)
- quatre chiffres
- un indicateur de citoyenneté à un seul chiffre
- le chiffre &quot;8&quot; ou &quot;9&quot;
- un chiffre, qui est un chiffre de somme de contrôle

### <a name="keywords"></a>Mots clés

#### <a name="keyword_south_africa_identification_number"></a>Keyword\_south\_africa\_identification\_number

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Numéro d’inscription de résident en Corée du Sud

### <a name="format"></a>Format

13 chiffres contenant un trait d’union

### <a name="pattern"></a>Modèle

13 chiffres :

- six chiffres au format YYMMDD (date de naissance)
- trait d’union
- un chiffre déterminé par le siècle et le sexe
- Code de région de naissance à quatre chiffres
- un chiffre utilisé pour différencier les personnes dont les nombres précédents sont identiques
- 1 chiffre de vérification.

### <a name="keywords"></a>Mots clés

#### <a name="keyword_south_korea_resident_number"></a>Keyword\_south\_korea\_resident\_number

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-dni"></a>Numéro d’identification nationale en Espagne 

### <a name="format"></a>Format

Huit chiffres suivis d’un caractère

### <a name="pattern"></a>Modèle

Sept chiffres suivis d’un caractère

- huit chiffres
- Un espace ou trait d’union facultatif
- Une lettre de vérification (ne respectant pas la casse)

### <a name="keywords"></a>Mots clés

#### <a name="keywords_spain_eu_national_id_card"></a>Mots clés\_spain\_eu\_national\_id\_card
```
carné de identidad
dni#
dni
dninúmero#
documento nacional de identidad
identidad único
identidadúnico#
insurance number
national identification number
national identity
nationalid#
nationalidno#
nie#
nie
nienúmero#
número de identificación
número nacional identidad
personal identification number
personal identity no
unique identity number
uniqueid#
```
## <a name="spain-drivers-license-number"></a>Numéro de permis de conduire en Espagne 

### <a name="format"></a>Format

Huit chiffres suivis d’un caractère

### <a name="pattern"></a>Modèle

Huit chiffres suivis d’un caractère :

- huit chiffres
- un chiffre ou une lettre (sans respect de la casse)

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_spain_eu_drivers_license_number"></a>Mots clés\_spain\_eu\_driver's\_license\_number
```
permiso de conducción
permiso conducción
licencia de conducir
licencia conducir
permiso conducir
permiso de conducir
permisos de conducir
permisos conducir
carnet conducir
carnet de conducir
licencia de manejo
licencia manejo
```
## <a name="spain-passport-number"></a>Numéro de passeport en Espagne 

### <a name="format"></a>Format

combinaison de lettres et de chiffres à huit ou neuf caractères sans espaces ni séparateurs

### <a name="pattern"></a>Modèle

combinaison de lettres et de chiffres composée de huit ou neuf caractères :

- deux chiffres ou lettres
- un chiffre ou une lettre (facultatif)
- six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_passport_number"></a>Mots clés \_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_spain_eu_passport_number"></a>Mots clés\_spain\_eu\_passport\_number
```
libreta pasaporte
número pasaporte
españa pasaporte
números de pasaporte
número de pasaporte
números pasaporte
pasaporte no
Passeport n°
n° Passeport
pasaporte no.
pasaporte n°
spain passport
```
#### <a name="keywords_eu_passport_date"></a>Mots clé\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="spain-social-security-number-ssn"></a>Numéro de sécurité sociale (SSN) en Espagne

Cette entité de type d’informations sensibles est incluse dans le type d’informations sensibles Numéro de sécurité sociale en France (INSEE) ou équivalent et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

11-12 chiffres

### <a name="pattern"></a>Modèle

11-12 chiffres :

- deux chiffres
- barre oblique (facultatif)
- sept ou huit chiffres
- barre oblique (facultatif)
- deux chiffres

### <a name="keywords"></a>Mots clés

Aucun

## <a name="spain-tax-identification-number"></a>Numéros d’identification fiscale en Espagne 

### <a name="format"></a>Format

sept ou huit chiffres et une ou deux lettres dans le modèle spécifié

### <a name="pattern"></a>Modèle

Personnes physiques espagnoles avec une carte d’identité nationale en Espagne :

- huit chiffres
- une lettre majuscule (respect de la casse)

Espagnols non résidents sans carte d’identité nationale de l’Espagne

- une lettre majuscule « L » (respect de la casse)
- sept chiffres
- une lettre majuscule (respect de la casse)

Espagnols résidents de moins de 14 ans sans carte d’identité nationale de l’Espagne :

- une lettre majuscule « K » (respect de la casse)
- sept chiffres
- une lettre majuscule (respect de la casse)

Étrangers avec le numéro d’identification d’un étranger

- une lettre majuscule « X », « Y » ou « Z » (respect de la casse)
- sept chiffres
- une lettre majuscule (respect de la casse)

Étrangers sans numéro d’identification étranger

- une lettre majuscule « M » (respect de la casse)
- sept chiffres
- une lettre majuscule (respect de la casse)

### <a name="keywords"></a>Mots clés

#### <a name="keywords_spain_eu_tax_file_number"></a>Mots clés\_spain\_eu\_tax\_file\_number
```
cif
cifid#
cifnúmero#
número de contribuyente
número de identificación fiscal
número de impuesto corporativo
spanishcifid#
spanishcifid
spanishcifno#
spanishcifno
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="sweden-drivers-license-number"></a>Numéro de permis de conduire en Suède

### <a name="format"></a>Format

10 chiffres contenant un trait d’union

### <a name="pattern"></a>Modèle

10 chiffres contenant un trait d’union :

- six chiffres
- trait d’union
- quatre chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_sweden_eu_drivers_license_number"></a>Mots clés\_sweden\_eu\_driver's\_license\_number
```
ajokortti
permis de conducere
ajokortin numero
kuljettajat lic.
drivere lic.
körkort
numărul permisului de conducere
שאָפער דערלויבעניש נומער
förare lic.
דריווערס דערלויבעניש
körkortsnummer
```

## <a name="sweden-national-id"></a>Numéro d’identité nationale en Suède

### <a name="format"></a>Format

10 ou 12 chiffres et un délimiteur facultatif

### <a name="pattern"></a>Modèle

10 ou 12 chiffres et un délimiteur facultatif :

- deux chiffres (facultatif)
- Six chiffres au format de date AAMMJJ
- délimiteur de &quot;-&quot; ou &quot;+&quot; (facultatif)
- quatre chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_swedish_national_identifier"></a>Keywords\_swedish\_national\_identifier

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>Numéro de passeport en Suède

Cette entité de type d’informations sensibles est incluse dans le type d’informations sensibles Numéro de passeport européen et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

huit chiffres

### <a name="pattern"></a>Modèle

huit chiffres consécutifs

### <a name="keywords"></a>Mots clés

#### <a name="keyword_sweden_passport"></a>Keyword\_sweden\_passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="sweden-tax-identification-number"></a>Numéro d’identification fiscale en Suède

### <a name="format"></a>Format

10 chiffres et un symbole dans le modèle spécifié

### <a name="pattern"></a>Modèle

10 chiffres et un symbole :

- six chiffres correspondant à la date de naissance (AAMMJJ)
- un signe plus ou moins
- trois chiffres qui rendent le numéro d’identification unique :
- pour les numéros émis avant le 1990, le septième et le huitième chiffre identifient le comté de naissance ou les personnes étrangères
- le chiffre de neuvième position indique le sexe, paire pour les hommes ou impaire pour les femmes
- un chiffre de vérification

### <a name="keywords"></a>Mots clés

#### <a name="keywords_sweden_eu_tax_file_number"></a>Mots clés\_sweden\_eu\_tax\_file\_number
```
personal id number
personnummer
skatt id nummer
skatt identifikation
skattebetalarens identifikationsnummer
sverige tin
tax file
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```

## <a name="swift-code"></a>Code Swift

### <a name="format"></a>Format

quatre lettres suivies de 5 à 31 lettres ou chiffres

### <a name="pattern"></a>Modèle

quatre lettres suivies de 5 à 31 lettres ou chiffres :

- code bancaire à quatre lettres (sans respect de la casse)
- espace facultatif
- 4 à 28 lettres ou chiffres (numéro de compte bancaire de base (IBAN))
- espace facultatif
- un à trois lettres ou chiffres (le reste du IBAN)

### <a name="keywords"></a>Mots clés

#### <a name="keyword_swift"></a>Keyword\_swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
\#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="switzerland-ssn-ahv-number"></a>Numéro SSN AVS de Suisse

### <a name="format"></a>Format

nombre à 13 chiffres

### <a name="pattern"></a>Modèle

nombre à 13 chiffres :

- trois chiffres - 756
- un point facultatif
- quatre chiffres
- un point facultatif
- quatre chiffres
- un point facultatif
- deux chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_swiss_ssn_ahv_number"></a>Mot clé\_swiss\_ssn\_AHV\_number
```
ahv
ssn
pid
insurance number
personalidno#
social security number
personal id number
personal identification no.
insuranceno#
uniqueidno#
unique identification no.
avs number
personal identity no versicherungsnummer
identifikationsnummer
einzigartige identität nicht
sozialversicherungsnummer
identification personnelle id
numéro de sécurité sociale
```

## <a name="taiwan-national-identification-number"></a>Numéro d’identification nationale à Taïwan

### <a name="format"></a>Format

une lettre (en anglais) suivie de neuf chiffres

### <a name="pattern"></a>Modèle

une lettre (en anglais) suivie de neuf chiffres :

- une lettre (en anglais) (sans respect de la casse)
- le chiffre &quot;1&quot; ou &quot;2&quot;
- huit chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_taiwan_national_id"></a>Keyword\_taiwan\_national\_id

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Numéro de passeport à Taïwan

### <a name="format"></a>Format

- numéro de passeport biométrique : neuf chiffres
- numéro de passeport non biométrique : neuf chiffres

### <a name="pattern"></a>Modèle

numéro de passeport biométrique :

- le caractère &quot;3&quot;
- huit chiffres

numéro de passeport non biométrique :

- neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_taiwan_passport"></a>Keyword\_taiwan\_passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Numéro de certificat de résident (ARC/TARC) à Taïwan

### <a name="format"></a>Format

10 lettres et chiffres

### <a name="pattern"></a>Modèle

10 lettres et chiffres :

- deux lettres (ne respectant pas la casse)
- huit chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_taiwan_resident_certificate"></a>Keyword\_taiwan\_resident\_certificate

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>au Royaume-Uni Numéro de permis de conduire

Cette entité de type d’informations sensibles est incluse dans le type d’information sensible Numéro de permis de conduire européen et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

Combinaison de 18 lettres et chiffres dans le format spécifié

### <a name="pattern"></a>Modèle

18 lettres et chiffres :

- cinq lettres (ne respectant pas la casse) ou le chiffre &quot;9&quot; à la place d’une lettre
- un chiffre
- cinq chiffres au format de date MMDDY pour la date de naissance (le septième caractère est incrémenté de 50 si le conducteur est une femme, c’est-à-dire 51 à 62 au lieu de 01 à 12)
- deux lettres (ne respectant pas la casse) ou le chiffre &quot;9&quot; à la place d’une lettre
- cinq chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_uk_drivers_license"></a>Keyword\_uk\_drivers\_license

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>au Royaume-Uni Numéro d’électeur

### <a name="format"></a>Format

deux lettres suivies de 1-4 chiffres

### <a name="pattern"></a>Modèle

deux lettres (ne respectant pas la casse) suivies de 1-4 chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_uk_electoral"></a>Keyword\_uk\_electoral

- Nomination au Conseil
- Formulaire de nomination
- registre électoral
- liste électorale

## <a name="uk-national-health-service-number"></a>au Royaume-Uni Numéro du service de santé national

### <a name="format"></a>Format

10-17 chiffres séparés par des espaces

### <a name="pattern"></a>Modèle

10-17 chiffres :

- trois ou 10 chiffres
- espace
- trois chiffres
- espace
- quatre chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_uk_nhs_number"></a>Keyword\_uk\_nhs\_number

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Keyword\_uk\_nhs\_number1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Keyword\_uk\_nhs\_number\_dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>au Royaume-Uni Numéro d’assurance nationale (NINO)

Cette entité de type d’informations sensibles est incluse dans le type d’information sensible Numéro d’identification national de l’UE et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

sept caractères ou neuf caractères séparés par des espaces ou des tirets

### <a name="pattern"></a>Modèle

deux modèles possibles :

- deux lettres (les numéros NINO valide utilisent uniquement certains caractères dans ce préfixe, que ce modèle valide ; ne respecte pas la casse)
- six chiffres
- « A », « B », « C » ou « D » (comme le préfixe, seuls certains caractères sont autorisés dans le suffixe ; ne respecte pas la casse)

OU

- deux lettres
- espace ou tiret
- deux chiffres
- espace ou tiret
- deux chiffres
- espace ou tiret
- deux chiffres
- espace ou tiret
- « A », « B », « C » ou « D »


### <a name="keywords"></a>Mots clés

#### <a name="keyword_uk_nino"></a>Keyword\_uk\_nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>au Royaume-Uni Numéro de référence fiscal unique (UTR)

Ce type d’informations sensibles n’est disponible que pour une utilisation dans :

- stratégies de protection contre la perte de données
- stratégies de conformité des communications
- gouvernance des informations
- gestion des enregistrements
- Microsoft Cloud App Security

### <a name="format"></a>Format

10 chiffres sans espaces ni délimiteurs

### <a name="pattern"></a>Modèle

10 chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keywords_uk_eu_tax_file_number"></a>Keywords\_uk\_eu\_tax\_file\_number

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="ukraine-passport-domestic"></a>passeport domestique en Ukraine

### <a name="format"></a>Format

neuf chiffres

### <a name="pattern"></a>Modèle

neuf chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_ukraine_passport_domestic"></a>Mot clé\_ukraine\_passport\_domestic
```
ukraine passport
passport number
passport no
паспорт України
номер паспорта
персональний
```
## <a name="ukraine-passport-international"></a>passeport international en Ukraine

### <a name="format"></a>Format

Modèle alphanumérique à 8 caractères

#### <a name="pattern"></a>Modèle

Modèle alphanumérique à 8 caractères :

- Deux lettres ou chiffres
- six chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_ukraine_passport_international"></a>Mot clé\_ukraine\_passport\_international
```
ukraine passport
passport number
passport no
паспорт України
номер паспорта
```
## <a name="us-bank-account-number"></a>Numéro de compte bancaire aux USA

### <a name="format"></a>Format

6-17 chiffres

### <a name="pattern"></a>Modèle

6-17 chiffres consécutifs

### <a name="keywords"></a>Mots clés

#### <a name="keyword_usa_bank_account"></a>Keyword\_usa\_Bank\_Account

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>Numéro de permis de conduire aux États-Unis

### <a name="format"></a>Format

Dépend de l’État

### <a name="pattern"></a>Modèle

dépend de l’État (par exemple, New York) :

- neuf chiffres mis en forme, comme ccc, correspondent.
- neuf chiffres tels que ccccccccc ne correspondent pas.

### <a name="keywords"></a>Mots clés

#### <a name="keyword_us_drivers_license_abbreviations"></a>Keyword\_us\_drivers\_license\_abbreviations

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>Keyword\_us\_drivers\_license

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Keyword\_[state\_name]\_drivers\_license\_name

- Abréviation d’État (par exemple, &quot;NY&quot;)
- Nom de l’État (par exemple, &quot;New York&quot;)

## <a name="us-individual-taxpayer-identification-number-itin"></a>Numéro d'identification de contribuable pour les États-Unis (ITIN)

### <a name="format"></a>Format

neuf chiffres commençant par un &quot;9&quot; et contenant un &quot;7&quot; ou &quot;8&quot; comme quatrième chiffre, éventuellement mis en forme avec des espaces ou des tirets

### <a name="pattern"></a>Modèle

mis en forme :

- le chiffre &quot;9&quot;
- deux chiffres
- espace ou tiret
- &quot;7&quot; ou &quot;8&quot;
- un chiffre
- espace ou tiret
- quatre chiffres

non mis en forme :

- le chiffre &quot;9&quot;
- deux chiffres
- &quot;7&quot; ou &quot;8&quot;
- cinq chiffres

### <a name="keywords"></a>Mots clés

#### <a name="keyword_itin"></a>Keyword\_itin

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>Numéro de sécurité sociale (SSN) aux États-Unis

### <a name="format"></a>Format

neuf chiffres, qui peuvent être dans un modèle mis en forme ou non mis en forme

> [!Note]
> S’il est émis avant le mi-2011, un SSN a une mise en forme forte dans laquelle certaines parties du nombre doivent être comprises dans certaines plages pour être valides (mais il n’y a pas de somme de contrôle).
>

### <a name="pattern"></a>Modèle

quatre fonctions recherchent les numéros de sécurité sociale dans quatre modèles différents :

- Func\_ssn trouve les numéros de sécurité sociale avec une mise en forme forte de 2011 qui est mise en forme avec des tirets ou des espaces (ddd-dd-dddd OU ddd dd dddd)
- Func\_non formaté\_ssn trouve des numéros de sécurité sociale avec une mise en forme forte de pré-2011 qui n’est pas mise en forme avec neuf chiffres consécutifs (ccccccccc)
- Func\_mise en forme\_aléatoire\_ssn trouve les numéros de sécurité sociales postérieurs à 2011 qui sont mis en forme avec des tirets ou des espaces (ddd-dd-dddd OR ddd dd dddd)
- Func\_aléatoire\_non formaté\_ssn trouve des numéros de sécurité sociale de pré-2011 qui ne sont pas mise en forme avec neuf chiffres consécutifs (ccccccccc)

### <a name="keywords"></a>Mots clés

#### <a name="keyword_ssn"></a>Keyword\_ssn

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>États-Unis/Royaume-Uni Numéro de passeport

L’ entité de type d’informations sensibles est disponible dans le type d’informations sensibles Numéro de passeport britannique et elle est disponible en tant qu’entité de type d’informations sensibles autonome.

### <a name="format"></a>Format

neuf chiffres

### <a name="pattern"></a>Modèle

neuf chiffres consécutifs

### <a name="keywords"></a>Mots clés

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
