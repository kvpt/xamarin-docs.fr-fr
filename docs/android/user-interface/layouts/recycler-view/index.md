---
title: RecyclerView
description: RecyclerView est un groupe d’affichage pour l’affichage des collections ; Il est conçu pour être un remplacement plus souple pour les anciens groupes d’affichage tels que ListView et GridView.  Ce guide explique comment utiliser et personnaliser RecyclerView dans les applications Xamarin. Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/03/2018
ms.openlocfilehash: bce89be8bec512ac70ca40015521c7d56f3460d3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028813"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView est un groupe d’affichage pour l’affichage des collections ; Il est conçu pour être un remplacement plus souple pour les anciens groupes d’affichage tels que ListView et GridView.  Ce guide explique comment utiliser et personnaliser RecyclerView dans les applications Xamarin. Android._

## <a name="recyclerview"></a>RecyclerView

De nombreuses applications ont besoin d’afficher des regroupements du même type (tels que des messages, des contacts, des images ou des chansons). souvent, cette collection est trop grande pour tenir sur l’écran, de sorte que la collection est présentée dans une petite fenêtre qui peut faire défiler facilement tous les éléments de la collection.
`RecyclerView` est un widget Android qui affiche une collection d’éléments dans une liste ou une grille, ce qui permet à l’utilisateur de faire défiler la collection. Voici une capture d’écran d’un exemple d’application qui utilise `RecyclerView` pour afficher le contenu de la boîte de réception de messagerie dans une liste de défilement verticale :

[![exemple d’application utilisant RecyclerView pour répertorier les messages de la boîte de réception](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` offre deux fonctionnalités attrayantes :

- Il dispose d’une architecture flexible qui vous permet de modifier son comportement en branchant vos composants préférés.

- Elle est efficace avec les collections volumineuses, car elle réutilise les vues d’élément et requiert l’utilisation de *conteneurs de vue* pour mettre en cache des références de vue.

Ce guide explique comment utiliser `RecyclerView` dans les applications Xamarin. Android. Il explique comment ajouter le package `RecyclerView` à votre projet Xamarin. Android et décrit comment `RecyclerView` fonctions dans une application classique. Des exemples de code réels sont fournis pour vous montrer comment intégrer `RecyclerView` dans votre application, comment implémenter le mode d’affichage des éléments et comment actualiser `RecyclerView` quand ses données sous-jacentes sont modifiées. Ce guide part du principe que vous êtes familiarisé avec le développement Xamarin. Android.

### <a name="requirements"></a>spécifications

Bien que `RecyclerView` soit souvent associé à Android 5,0 Lollipop, il est proposé sous la forme d’une bibliothèque de prise en charge &ndash; `RecyclerView` fonctionne avec les applications qui ciblent le niveau d’API 7 (Android 2,1) et versions ultérieures. Les éléments suivants sont requis pour utiliser `RecyclerView` dans les applications basées sur Xamarin :

- **Xamarin. android** &ndash; Xamarin. Android 4,20 ou version ultérieure doit être installé et configuré à l’aide de Visual Studio ou de Visual Studio pour Mac.

- Votre projet d’application doit inclure le package **Xamarin. Android. support. v7. RecyclerView** . Pour plus d’informations sur l’installation des packages NuGet, consultez [procédure pas à pas : inclusion d’un NuGet dans votre projet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

### <a name="overview"></a>Vue d'ensemble

`RecyclerView` peut être considérée comme un remplacement des widgets `ListView` et `GridView` dans Android. Comme ses prédécesseurs, `RecyclerView` est conçu pour afficher un jeu de données volumineux dans une petite fenêtre, mais `RecyclerView` offre plus d’options de disposition et est mieux optimisé pour l’affichage des collections volumineuses. Si vous êtes familiarisé avec `ListView`, il existe plusieurs différences importantes entre `ListView` et `RecyclerView`:

- `RecyclerView` est légèrement plus complexe à utiliser : vous devez écrire du code supplémentaire pour utiliser `RecyclerView` par rapport à `ListView`.

- `RecyclerView` ne fournit pas d’adaptateur prédéfini ; vous devez implémenter le code d’adaptateur qui accède à votre source de données. Toutefois, Android comprend plusieurs adaptateurs prédéfinis qui fonctionnent avec `ListView` et `GridView`.

- `RecyclerView` n’offre pas d’événement de clic d’élément lorsqu’un utilisateur appuie sur un élément ; au lieu de cela, les événements de clic d’élément sont gérés par les classes d’assistance. En revanche, `ListView` offre un événement de clic d’élément.

- `RecyclerView` améliore les performances en recyclant les vues et en appliquant le modèle de porte-conteneurs, ce qui élimine les recherches de ressources de disposition inutiles. L’utilisation du modèle de conteneur d’affichage est facultative dans `ListView`.

- `RecyclerView` est basé sur une conception modulaire qui facilite la personnalisation. Par exemple, vous pouvez intégrer une stratégie de mise en page différente sans modification significative du code de votre application.
    En revanche, `ListView` est relativement monolithique dans la structure.

- `RecyclerView` comprend des animations intégrées pour l’ajout et la suppression d’éléments. `ListView` animations requièrent des efforts supplémentaires sur la partie du développeur de l’application.

### <a name="sections"></a>Sections

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Composants et fonctionnalités RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

Cette rubrique explique comment les `Adapter`, `LayoutManager`et `ViewHolder` fonctionnent ensemble comme des classes d’assistance pour prendre en charge les `RecyclerView`.
Il fournit une vue d’ensemble détaillée de chacune de ces classes d’assistance et explique comment les utiliser dans votre application.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Exemple de RecyclerView de base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Cette rubrique s’appuie sur les informations fournies dans les [composants et les fonctionnalités RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) en fournissant des exemples de code réels sur la façon dont les différents éléments de `RecyclerView` sont implémentés pour créer une application de navigation de photos réaliste.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Extension de l’exemple RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

Cette rubrique ajoute du code supplémentaire à l’exemple d’application présenté dans [un exemple RecyclerView de base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) pour montrer comment gérer les événements de clic d’élément et mettre à jour les `RecyclerView` lorsque la source de données sous-jacente change.

### <a name="summary"></a>Récapitulatif

Ce guide a présenté le widget `RecyclerView` Android. Il a expliqué comment ajouter la bibliothèque de prise en charge `RecyclerView` aux projets Xamarin. Android, comment `RecyclerView` recycle les vues, comment elle applique le modèle de porte-garde pour améliorer l’efficacité et comment les différentes classes d’assistance qui composent `RecyclerView` collaborent pour afficher des collections. Il a fourni un exemple de code pour illustrer la façon dont `RecyclerView` est intégré à une application, mais il a expliqué comment adapter la stratégie de disposition des `RecyclerView`en connectant différents gestionnaires de disposition et comment gérer les événements de clic d’élément et notifier `RecyclerView` de données. modifications de la source.

Pour plus d’informations sur `RecyclerView`, consultez la référence de la [classe RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).

## <a name="related-links"></a>Liens associés

- [RecyclerViewer (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Présentation du symbole Lollipop](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
