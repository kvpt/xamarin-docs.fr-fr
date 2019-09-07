---
title: Xamarin. Forms, DataPages
description: Cet article présente Xamarin. Forms DataPages, qui fournit une API pour lier rapidement et facilement une source de données à des affichages prédéfinis.
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 1dc62066b71842e1d3b07495912fa35a549c0f1e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759680"
---
# <a name="xamarinforms-datapages"></a>Xamarin. Forms, DataPages

![](~/media/shared/preview.png "Cette API est actuellement en version préliminaire")

> [!IMPORTANT]
> DataPages requiert une référence de thème Xamarin. Forms à restituer. Cela implique d’installer le package NuGet [Xamarin. Forms. Theme. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) dans votre projet, suivi des packages NuGet [Xamarin. Forms. Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) ou [Xamarin. Forms. Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

Xamarin. Forms DataPages ont été annoncés à l’évolution de 2016 et sont disponibles en version préliminaire pour que les clients essaient et fournissent des commentaires.

Pages de données fournissent une API pour rapidement et facilement lier une source de données à des affichages prédéfinis. Les éléments de liste et les pages de détails affichent automatiquement les données et peuvent être personnalisés à l’aide de thèmes.

Pour voir comment fonctionne la démonstration faire évoluer le discours, consultez le [Guide de mise](get-started.md)en route.

[![](images/demo-sml.png "Exemple de pages de données d’Application")](images/demo.png#lightbox "exemple des pages de données d’Application")

## <a name="introduction"></a>Présentation

Les sources de données et les pages de données associées permettent aux développeurs de consommer rapidement et facilement une source de données prise en charge et de les restituer à l’aide de la génération de modèles automatique d’interface utilisateur intégrée qui peuvent être personnalisées avec des thèmes.

Les DataPages sont ajoutées à une application Xamarin. Forms en incluant le package NuGet **Xamarin. Forms. pages** .

### <a name="data-sources"></a>Data Sources

La version préliminaire contient des sources de données prédéfinies pouvant être utilisées :

* **JsonDataSource**
* **AzureDataSource** (NuGet distinct)
* **AzureEasyTableDataSource** (NuGet distinct)

Pour obtenir un exemple d’utilisation d’un `JsonDataSource`, consultez le [Guide de mise en route](get-started.md).

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

Une source de données Xamarin. Forms adhère à `IDataSource` l’interface.

L’infrastructure Xamarin. Forms interagit avec une source de données par le biais des propriétés suivantes :

* `Data`: liste en lecture seule d’éléments de données qui peuvent être affichés.
* `IsLoading`: valeur booléenne qui indique si les données sont chargées et disponibles pour le rendu.
* `[key]`: indexeur permettant de récupérer des éléments.

Il existe deux méthodes `MaskKey` `UnmaskKey` qui peuvent être utilisées pour masquer (ou afficher) les propriétés d’éléments de données (par ex. les empêcher d’être rendues).
La clé correspond à une propriété nommée sur l’objet d’élément de données.
