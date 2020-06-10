---
titre : «Résumé du chapitre 9. Appels d’API spécifiques à la plateforme "Description :" création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 9. L’API spécifique à la plateforme appelle "ms. Prod : xamarin ms. Technology : xamarin-Forms ms. AssetID : 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D auteur : davidbritch ms. Author : dabritch ms. Date : 07/19/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Résumé du chapitre 9. Appels d’API spécifiques de la plateforme

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> Les notes de cette page indiquent les zones où elles Xamarin.Forms divergent du matériel présenté dans le livre.

Il est parfois nécessaire d’exécuter du code qui varie selon la plateforme. Ce chapitre explore les techniques.

## <a name="preprocessing-in-the-shared-asset-project"></a>Prétraitement dans le projet de ressource partagée

Un Xamarin.Forms projet de ressource partagé peut exécuter du code différent pour chaque plateforme à l’aide des directives de préprocesseur C# `#if` , `#elif` et `endif` . Cela est illustré dans [**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Capture d’écran triple du paragraphe mis en forme variable](images/ch09fg01-small.png "Modèle d’appareil et système d’exploitation")](images/ch09fg01-large.png#lightbox "Modèle d’appareil et système d’exploitation")

Toutefois, le code résultant peut être difficile à lire.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classes parallèles dans le projet d’actifs partagés

Une approche plus structurée de l’exécution de code spécifique à la plateforme dans SAP est présentée dans l’exemple [**PlatInfoSap2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) . Chacun des projets de plateforme contient une classe et des méthodes de même nom, mais elles sont implémentées pour cette plateforme particulière. Le SAP instancie ensuite simplement la classe et appelle la méthode.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService et la bibliothèque de classes portable

> [!NOTE] 
> Les bibliothèques de classes portables ont été remplacées par des bibliothèques de .NET Standard. Tout l’exemple de code du livre a été converti pour utiliser des bibliothèques .NET standard.

Une bibliothèque ne peut normalement pas accéder aux classes des projets d’application. Cette restriction semble empêcher l’utilisation de la technique indiquée dans **PlatInfoSap2** dans une bibliothèque. Toutefois, Xamarin.Forms contient une classe nommée [`DependencyService`](xref:Xamarin.Forms.DependencyService) qui utilise la réflexion .net pour accéder aux classes publiques dans le projet d’application à partir de la bibliothèque.

La bibliothèque doit définir un `interface` avec les membres qu’il doit utiliser dans chaque plateforme. Ensuite, chacune des plateformes contient une implémentation de cette interface. La classe qui implémente l’interface doit être identifiée par un [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) au niveau de l’assembly.

La bibliothèque utilise ensuite la [`Get`](xref:Xamarin.Forms.DependencyService.Get*) méthode générique de `DependencyService` pour obtenir une instance de la classe Platform qui implémente l’interface.

Cela est illustré dans l’exemple [**DisplayPlatformInfo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) .

## <a name="platform-specific-sound-generation"></a>Génération de sons spécifique à la plateforme

L’exemple [**MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) ajoute des signaux sonores au programme **MonkeyTap** en accédant à des fonctionnalités de génération de sons sur chaque plateforme.

## <a name="related-links"></a>Liens connexes

- [Chapitre 9 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Exemples du chapitre 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Service de dépendance](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
