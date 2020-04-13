---
title: Analyseurs de diagnostics de mise en page Android
description: Ce guide répertorie tous les analyseurs de diagnostic de mise en page Android actuellement pris en charge sur Xamarin.Android.
ms.prod: xamarin
ms.assetid: BD252EA7-7E69-4DB4-96AB-D52CC0510C8F
ms.technology: xamarin-android
author: decriptor
ms.author: stepsha
ms.date: 04/07/2020
ms.openlocfilehash: 6203ce444bb97fa453a912a724f7f5724558b32a
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987605"
---
# <a name="android-designer-diagnostic-analyzers"></a>Analyseurs diagnostiques de concepteur d’Android

Ce guide répertorie tous les analyseurs de diagnostic de mise en page Android actuellement pris en charge.

## <a name="accessibility"></a>Accessibilité

Les analyseurs suivants aident à améliorer le soutien à l’accessibilité :

| id | Intitulé | severity | Description |
|----|-------|----------|-------------|
| ContentDescription | Image sans`contentDescription` | Avertissement | Attribut `contentDescription` manquant sur l’image |

## <a name="correctness"></a>Exactitude

Les analyseurs suivants aident à résoudre les problèmes de correction dans une mise en page :

| id | Intitulé | severity | Description | Aide |
|----|-------|----------|-------------|------|
| AdaptViewChildren | AdaptEurView avec les enfants | Avertissement | AdaptViews ne peut pas avoir d’enfants dans XML | [Lien](xref:Android.Widget.AdapterView) |
| AbsentId | Les fragments `id` doivent spécifier un`tag` | Avertissement |Cette `<fragment>` balise `id` doit `tag` spécifier un ou un état pour préserver à travers les redémarrages d’activités | [Lien](xref:Android.App.Fragment) |
| NestedScrollingVertical | Éléments de défilement vertical imbriqués | Avertissement | Widgets de défilement imbriqués |
| NestedScrollingHorizontal | Éléments de défilement horizontalement imbriqués | Avertissement | Widgets de défilement imbriqués |
| ScrollViewSize (en) | Les enfants de ScrollView ayant de mauvaises tailles de fill_parent/match_parent | Avertissement | Les enfants de ScrollView ayant de mauvaises tailles de fill_parent/match_parent |
| ScrollViewCount (en) | ScrollViews ne peut avoir qu’un seul enfant | Avertissement | Une vue de défilement peut avoir un seul enfant |
| MissingAndroidNamespace | Espace de nom Android manquant sur l’attribut | Error | Espace de nom Android XML manquant; votre attribut sera interprété comme un attribut personnalisé |
| DuplicateIDs DuplicateIDs DuplicateIDs DuplicateI | Les DIU en double | Error | Ids en double dans une seule mise en page |
| InclureLayoutParamsMissingWidthAndHeight | Manque à la fois de largeur et de hauteur | Error | Les params de mise en page ignorés sur incluent | [Lien](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| InclureLayoutParamsMissingWidth | Largeur manquante | Error | Les params de mise en page ignorés sur incluent | [Lien](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| InclureLayoutParamsMissingHeight | Hauteur manquante | Error | Les params de mise en page ignorés sur incluent | [Lien](https://stackoverflow.com/questions/2631614/does-android-xml-layouts-include-tag-really-work) |
| Orientation | Absence d’orientation explicite | Error | Absence d’orientation explicite |
| Suspicious0dp | Dimension suspecte de 0dp | Error | Dimension suspecte de 0dp |
| RequisSizeWidth | Attribut de largeur manquant | Error | Attribut manquant : layout_width |
| RequiredSizeHeight | Attribut de hauteur manquant | Error | Attribut manquant : layout_height |
| WebViewLayout (en) | WebViews chez les parents wrap_content | Error |
| MalCase | Mauvais cas pour l’étiquette de vue | Error | Mauvais cas pour l’étiquette de vue | [Lien](xref:Android.App.Fragment) |

## <a name="design"></a>Conception

Les analyseurs suivants aident à améliorer la façon dont vous joignez les fichiers de mise en page :

| id | Intitulé | severity | Description |
|----|-------|----------|-------------|
| HardcodedColor (en) | Couleur hardcoded | Info | La couleur codée dure conduit souvent à l’incohérence |
| HardcodedSize (en)  | Taille codée par dur  | Info | La taille codée par dur conduit souvent à l’incohérence  |
| HardcodedText (en)  | Texte codé en dur  | Avertissement | Texte codé en dur |
| Non résoluResource | URL de ressource non résolue | Avertissement | Cette URL de ressource ne peut pas être résolue |
| XmlErrors XmlErrors | Erreur de syntaxe XML | Error | Erreur de syntaxe XML |

## <a name="performance"></a>Performances

Les analyseurs suivants aident à améliorer les performances de votre mise en page :

| id | Intitulé | severity | Description |
|----|-------|----------|-------------|
| Poids imbriqués | Poids de mise en page imbriqués | Avertissement | Les poids imbriqués sont mauvais pour la performance |
| TooManyViews (en anglais) | La mise en page a trop de vues | Avertissement | La mise en page a trop de vues |
| TooDeepLayout | La hiérarchie de mise en page est trop profonde | Avertissement | La hiérarchie de mise en page est trop profonde |
| InutileParent | Mise en page inutile des parents | Avertissement | Mise en page inutile des parents |
| InutileLeaf | Disposition inutile de la feuille | Avertissement | Cette `%1$s` vue est inutile (pas d’enfants, non, `background`non, `id`non `style`) |

## <a name="usability"></a>Facilité d'utilisation

Les analyseurs suivants aident à améliorer la facilité d’utilisation de la mise en page pour vos clients :

| id | Intitulé | severity | Description |
|----|-------|----------|-------------|
| NégatifMargin | Marges négatives | Avertissement | Marges négatives |
| MissingInputType | EditText sans entréeType | Avertissement | Aucun type d’entrée spécifié |
| InputTypePhone (en) | EditText semble être un numéro de téléphone | Avertissement | Le nom de vue suggère qu’il s’agit d’un numéro de téléphone, mais il n’inclut `phone` pas dans le`inputType` |
| InputTypeNumber (en) | EditText semble être un nombre | Avertissement | Le nom de vue suggère qu’il s’agit d’un nombre, mais il n’inclut pas un numérique `inputType` (tel que `numberDecimal`) |
| InputTypePassword (en) | EditText apparaît comme un mot de passe | Avertissement | Le nom de vue suggère qu’il `password` s’agit d’un mot de passe, mais il n’inclut pas dans le `inputType` (tel que `textVisiblePassword`) |
| InputTypePIN (en) | EditText apparaît comme un NIP | Avertissement | Le nom de vue suggère qu’il s’agit d’un mot de passe (NIP), mais il n’inclut `numberPassword` pas dans le`inputType` |
| InputTypeEmail (en) | EditText semble être un e-mail | Avertissement | Le nom de vue suggère qu’il s’agit `email` d’une adresse e-mail, mais il n’inclut pas dans le `inputType` (tel que `textEmailAddress`) |
| InputTypeURI (en) | EditText semble être un URI | Avertissement | Le nom de vue suggère qu’il s’agit d’une URI, mais il n’inclut `textUri` pas dans le`inputType` |
| InputTypeDate (en) | EditText semble être une date | Avertissement | Le nom de vue suggère qu’il `date` s’agit d’une date, mais il n’inclut pas dans le `inputType` (tel que `datetime`) |
