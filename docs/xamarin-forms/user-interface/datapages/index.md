---
title: Xamarin.FormsDataSourceControl
description: Cet article présente la Xamarin.Forms classe DataPages, qui fournit une API permettant de lier rapidement et facilement une source de données à des vues prédéfinies.
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5e1ea94e42e98609b3f77f0198e125b94e2b437d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86928833"
---
# <a name="xamarinforms-datapages"></a>Xamarin.FormsDataSourceControl

![Cette API est actuellement en version préliminaire](~/media/shared/preview.png)

> [!IMPORTANT]
> DataPages requiert une Xamarin.Forms référence de thème à restituer. Cela implique l’installation de [ Xamarin.Forms . Package. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) NuGet dans votre projet, suivi de l’un ou l’autre [ Xamarin.Forms . Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) ou [ Xamarin.Forms . Packages NuGet Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Xamarin.FormsLes DataPages ont été annoncées à l’évolution de 2016 et sont disponibles en version préliminaire pour que les clients essaient et fournissent des commentaires.

Les DataPages fournissent une API pour lier rapidement et facilement une source de données à des vues prédéfinies. Les éléments de liste et les pages de détails affichent automatiquement les données et peuvent être personnalisés à l’aide de thèmes.

Pour voir comment fonctionne la démonstration faire évoluer le discours, consultez le [Guide de mise](get-started.md)en route.

[![Exemple d’application DataPages](images/demo-sml.png)](images/demo.png#lightbox "Exemple d’application DataPages")

## <a name="introduction"></a>Introduction

Les sources de données et les pages de données associées permettent aux développeurs de consommer rapidement et facilement une source de données prise en charge et de les restituer à l’aide de la génération de modèles automatique d’interface utilisateur intégrée qui peuvent être personnalisées avec des thèmes.

Les DataPages sont ajoutées à une Xamarin.Forms application en incluant ** Xamarin.Forms . **Package de pages NuGet.

### <a name="data-sources"></a>Sources de données

La version préliminaire contient des sources de données prédéfinies pouvant être utilisées :

* **JsonDataSource**
* **AzureDataSource** (NuGet distinct)
* **AzureEasyTableDataSource** (NuGet distinct)

Pour obtenir un exemple d’utilisation d’un, consultez le [Guide de mise](get-started.md) en route `JsonDataSource` .

### <a name="pages--controls"></a>Pages & contrôles

Les pages et les contrôles suivants sont inclus pour permettre une liaison facile avec les sources de données fournies :

* **ListDataPage** : consultez l' [exemple de prise](get-started.md)en main.
* **DirectoryPage** : liste dans laquelle le regroupement est activé.
* **PersonDetailPage** : vue d’élément de données unique personnalisée pour un type d’objet spécifique (entrée de contact).
* **DataView** : vue permettant d’exposer des données à partir de la source de manière générique.
* **CardView** : affichage stylisé contenant une image, un texte de titre et un texte de description.
* **HeroImage** : affichage de rendu d’image.
* **ListItem** : vue prédéfinie avec une disposition similaire aux éléments de liste iOS et Android natifs.

Consultez les informations de référence sur les [contrôles DataPages](controls.md) pour obtenir des exemples.

### <a name="under-the-hood"></a>En coulisses

Une Xamarin.Forms source de données adhère à l' `IDataSource` interface.

L' Xamarin.Forms infrastructure interagit avec une source de données par le biais des propriétés suivantes :

* `Data`: liste en lecture seule d’éléments de données qui peuvent être affichés.
* `IsLoading`: valeur booléenne qui indique si les données sont chargées et disponibles pour le rendu.
* `[key]`: indexeur permettant de récupérer des éléments.

Il existe deux méthodes `MaskKey` `UnmaskKey` qui peuvent être utilisées pour masquer (ou afficher) les propriétés d’éléments de données (par ex. les empêcher d’être rendues).
La clé correspond à une propriété nommée sur l’objet d’élément de données.
