---
title : "informations de référence sur les contrôles" : description de tous les éléments de l’interface utilisateur utilisés pour construire une Xamarin.Forms application. Cet article répertorie les groupes de contrôle qui composent l’interface utilisateur d’une Xamarin.Forms application.»
ms. Prod : xamarin ms. AssetID : F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 08/08/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="controls-reference"></a>Informations de référence sur les contrôles

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

L’interface utilisateur d’une Xamarin.Forms application est constituée d’objets qui mappent aux contrôles natifs de chaque plateforme cible. Cela permet aux applications spécifiques à la plateforme pour iOS, Android et le plateforme Windows universelle d’utiliser le Xamarin.Forms code contenu dans une [bibliothèque de .NET standard](~/cross-platform/app-fundamentals/net-standard.md).

Les quatre principaux groupes de contrôles utilisés pour créer l’interface utilisateur d’une Xamarin.Forms application sont les suivants :

- [**Pages**](pages.md)
- [**Dispositions**](layouts.md)
- [**Views**](views.md)
- [**Cellules**](cells.md)

Une Xamarin.Forms page occupe généralement la totalité de l’écran. La page contient généralement une disposition, qui contient des vues et éventuellement d’autres dispositions. Les cellules sont des composants spécialisés utilisés dans le cadre de la connexion à [`TableView`](views.md#tableview) et [`ListView`](views.md#listview) . Un diagramme de classes qui affiche la hiérarchie des types généralement utilisés pour générer une interface utilisateur dans Xamarin.Forms peut être trouvé dans la [ Xamarin.Forms hiérarchie de classes de contrôles](~/xamarin-forms/internals/class-hierarchy.md).

Dans les quatre articles sur les [**pages**](pages.md), les [**dispositions**](layouts.md), les [**vues**](views.md)et les [**cellules**](cells.md), chaque type de contrôle est décrit avec des liens vers sa documentation d’API, un article décrivant son utilisation (le cas échéant) et un ou plusieurs exemples de programmes (s’ils existent). Chaque type de contrôle est également accompagné d’une capture d’écran montrant une page de l’exemple [**FormsGallery**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) s’exécutant sur des appareils iOS et Android. Sous chaque capture d’écran se trouvent des liens vers le code source de la page C#, la page XAML équivalente et (le cas échéant) le fichier code-behind C# pour la page XAML.

> [!NOTE]
> Les pages, les dispositions et les vues dérivent de la `VisualElement` classe. La `VisualElement` classe fournit diverses propriétés, méthodes et événements qui sont utiles dans la dérivation des classes. Pour plus d’informations, consultez [Propriétés, méthodes et événements VisualElement](common-properties.md).

Outre les contrôles fournis avec, des Xamarin.Forms contrôles tiers sont disponibles. Pour plus d’informations, consultez [contrôles tiers](thirdparty.md).

## <a name="related-links"></a>Liens connexes

- [Xamarin.FormsExemple FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsHiérarchie des classes de contrôles](~/xamarin-forms/internals/class-hierarchy.md)
- [Documentation sur les API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
